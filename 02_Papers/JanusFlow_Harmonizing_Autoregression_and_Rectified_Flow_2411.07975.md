# [논문 스터디] JanusFlow: Harmonizing Autoregression and Rectified Flow for Unified Multimodal Understanding and Generation

> - **작성일**: 2026.04.16
> - **Institution**: DeepSeek-AI
> - **Year/Venue**: 2024 / arXiv (Accepted: CVPR 2025)
> - **Paper**: [arXiv:2411.07975](https://arxiv.org/abs/2411.07975)
> - **Code**: [GitHub (deepseek-ai/Janus)](https://github.com/deepseek-ai/Janus)
> - **한줄평**: LLM 기반 통합 모델에서 “이해(autoregression)”와 “생성(rectified flow)”의 충돌을 인코더 분리+정렬로 풀어내는 접근.

---
## 1. 도입 및 배경 (Introduction)
* **논문이 해결하려는 문제**:
  - 통합 멀티모달 모델은 하나의 프레임에서 `이해(understanding)`와 `생성(generation)`을 동시에 수행해야 함.
  - 그런데 이 두 작업은 필요한 시각 정보 성격이 다름.
    - 이해: 의미/추상적 특징 중심(semantic)
    - 생성: 픽셀/잠재공간에서 재구성 가능한 구조 중심(생성용 표현)
  - 따라서 기존 “통합 autoregressive + diffusion/flow”류에서는 공유 인코더가 성능 타협을 만들 수 있음.

* **핵심 아이디어**:
  - **autoregressive LLM**은 이해 작업의 next-token prediction에 사용.
  - 이미지 생성은 LLM 안에 **rectified flow** 연산을 자연스럽게 결합해, 잠재공간에서 노이즈를 점진적으로 업데이트해 이미지를 만듦.
  - 추가로 **이해용 인코더와 생성용 인코더를 분리(Decoupling)**하고, 통합 학습 중 **표현 정렬(Representation Alignment Regularization)**로 두 작업의 간섭을 줄임.

* **구성 한눈에 보기**:
| 축 | JanusFlow에서의 처리 |
|---|---|
| 이해(Understanding) | LLM autoregression + 시맨틱 연속 특징(SigLIP) |
| 생성(Generation) | LLM 안에서 rectified flow로 latent 업데이트 + SDXL-VAE latent 디코드 |
| 통합 충돌 완화 | 이해/생성용 비전 인코더 분리 + 중간 표현 정렬 |

![JanusFlow Results](./images/2411.07975/figure%20(1).png)
*Figure 1 | 다중모달 이해와 이미지 생성 결과(해상도: 384×384)*

---
## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **기본 뼈대(LLM)**
  - DeepSeek-LLM 기반(1.3B), 24 transformer blocks
  - 시퀀스 길이: 4,096
  - 이해/생성 모두에서 주 입력 이미지 해상도: 384×384

* **인코더 디커플링(Decoupling Encoders)**
  - **이해 인코더 (f_enc)**: pre-trained `SigLIP-Large-Patch/16` 사용 (약 300M 파라미터급)
  - **생성 인코더/디코더 (g_enc, g_dec)**: latent 공간에서 동작하며, `ConvNeXt` 블록 기반으로 구성(초기화부터 학습)
  - 생성 encoder와 decoder 사이에 **long skip connection**을 추가해 생성 품질/학습 안정성을 확보.

![JanusFlow Architecture](./images/2411.07975/figure%20(2).png)
*Figure 2 | JanusFlow 아키텍처: 이해는 autoregressive, 생성은 rectified flow로 latent 업데이트*

* **이미지 생성 절차(추론)**
  - latent 공간에서 시작: `z0`를 Gaussian noise로 샘플링
  - `t=0 -> 1`로 진행하며, LLM이 예측한 velocity vector로 `z_t`를 반복 업데이트
  - `t=1`에 도달하면 SDXL-VAE 디코더로 최종 이미지를 복원
  - 생성 시 품질 향상을 위해 **CFG(Classifier-Free Guidance)**를 사용:
    - 무조건/조건 velocity를 조합해 semantic alignment를 높이는 방식
  - 이미지 생성 시작을 알리기 위해 입력 시퀀스에 **`|BOI|` 토큰**을 사용.

* **훈련 스테이지(3-stage training)**
  - Figure 3처럼 3단계 순차 학습을 수행.
    1. **Stage 1**: 랜덤 초기화된 모듈(선형층/생성 encoder/decoder)만 학습해서 pre-trained LLM/SigLIP와 결합되도록 적응
    2. **Stage 2 (Unified Pre-Training)**: 비전 인코더를 동결한 채 전체 모델을 통합 사전학습
       - 데이터 타입: `multimodal understanding`, `image generation`, `text-only`
       - 초기 구간에서는 이해 비중을 더 높게 두고, 이후 생성 수렴을 위해 생성 비중을 확대
    3. **Stage 3 (SFT)**: instruction tuning 데이터로 미세조정
       - 이 단계에서 SigLIP 인코더까지 unfreeze

![Training Stages](./images/2411.07975/figure%20(3).png)
*Figure 3 | JanusFlow의 3단계 훈련 프로세스(학습 가능한 모듈과 동결 모듈)*

* **학습 목적함수(Training Objective)**
  - 이해(Understanding): `autoregression objective`로 response 토큰에 대해 최대우도(teacher forcing) 학습
  - 생성(Generation): `rectified flow objective`
    - 조건 텍스트와 함께 latent `z_t`에서 velocity를 학습해 latent을 목표 분포로 이동
    - time distribution은 logit-normal을 사용
    - CFG를 가능하게 하기 위해 학습 중 텍스트 프롬프트를 일부 드롭(약 10%)
  - 생성-이해 정합: `representation alignment regularization`
    - 이해 인코더의 특징과 LLM 중간 표현을 cosine similarity로 정렬
    - 구현상 이해 인코더로 gradient가 역전파되지 않도록 설계해 안정성을 확보

---
## 3. 실험 및 결과 (Experiments)
* **주요 이미지 생성 벤치마크**

| 벤치마크 | JanusFlow(ours) | 의미 |
|---|---:|---|
| GenEval (overall) | `0.63` | 객체/관계가 프롬프트에 정렬되는지(semantic accuracy) |
| DPG-Bench (overall) | `80.09%` | entity/attribute/relation 중심 정합 |
| MJHQ FID-30k | `9.51` | 시각 품질(분포 거리, FID) |

  - 생성 관점에서 이전 통합 프레임 대비 우위를 보이며, 1.3B 급에서도 최상위 수준을 주장.
  - 특히 rectified flow가 autoregressive 기반 생성(예: Janus)보다 생성 품질을 개선함을 강조.

* **주요 멀티모달 이해 벤치마크(대표 수치)**
  - POPE: `88.0`
  - MME-P: `1333.1`
  - MMBdev: `74.9`
  - SEED: `70.5`
  - VQAv2(test): `79.8`
  - GQA: `60.3`
  - MMMU: `29.3`
  - MM-Vet: `30.9`
  - ChartQA: `64.6`
  - TextVQA: `55.5`

* **정성 결과(이미지 생성)**
![Generation Qualitative](./images/2411.07975/figure%20(4).png)
*Figure 4 | 텍스트 프롬프트와 의미적으로 일관된 이미지 생성 예시*

* **정성 결과(시각 이해)**
![Understanding Qualitative](./images/2411.07975/figure%20(5).png)
*Figure 5 | 질의응답/플롯 해석/객체 카운팅 등 다양한 시각 이해 예시*

* **절제 연구(Ablation)에서 강조한 점**
  - **Representation Alignment** 정규화를 넣었을 때 FID 및 CLIP 관련 지표가 동시에 개선됨.
  - **Decoupling(비전 인코더 분리)**이 공유 인코더 설계보다 유리함을 보여줌.

---
## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - JanusFlow는 통합 멀티모달 모델에서
    - 이해는 autoregression,
    - 생성은 rectified flow
    - 그리고 이를 하나의 LLM 프레임에 “최소한의 추가 구성”으로 묶어내며,
    - 인코더 디커플링과 표현 정렬로 성능 타협을 줄이려는 설계를 보여줌.

* **한계/주의점(문서 기반 관찰)**:
  - 대표 결과는 384×384 해상도와 SDXL-VAE latent 기반 생성 파이프라인에 의존.
  - 따라서 “고해상도 픽셀 단위 생성”이나 “더 복잡한 모달리티(영상/3D)”로의 확장에는 별도 설계가 필요할 수 있음.

* **시사점**:
  - 통합 모델에서 이해/생성의 요구사항 차이를 “모듈 분리 + 정렬”로 다루는 패턴이 이후 연구의 베이스라인이 될 가능성이 큼.

