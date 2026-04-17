# [논문 스터디] Attention Is All You Need

> - **작성일**: 2026.04.15
> - **Institution**: Google Brain, Google Research
> - **Year/Venue**: 2017 / NeurIPS
> - **Paper**: [arXiv:1706.03762](https://arxiv.org/abs/1706.03762)
> - **Code**: [Tensor2Tensor](https://github.com/tensorflow/tensor2tensor)
> - **한줄평**: RNN/CNN 없이 Self-Attention만으로 시퀀스 모델링의 병목을 깨고, 현대 LLM 시대의 기본 문법을 만든 전환점.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - 기존 시퀀스 변환(번역) 모델은 RNN/LSTM 중심이라 순차 계산 특성 때문에 병렬화가 어렵고 학습 속도가 느렸음.
  - 긴 문장에서 멀리 떨어진 단어 간 의존성(Long-range dependency)을 학습할 때 경로 길이가 길어 정보 전달이 비효율적이었음.
  - 제안 모델인 Transformer는 Recurrence 없이 Attention만으로 입력-출력 의존성을 직접 연결해 병렬성과 성능을 동시에 확보함.

* **모델 진화/비교**:

| 비교 항목 | RNN/LSTM 기반 Seq2Seq | CNN 기반 시퀀스 모델 | Transformer |
|---|---|---|---|
| **연산 구조** | 순차(시간축) 연산 필수 | 합성곱 기반 지역 수용영역 | Self-Attention 기반 전역 상호작용 |
| **병렬화** | 낮음 | 중간 | 매우 높음 |
| **장거리 의존성 처리** | 경로 길이 길어 비효율 | 층 깊이에 의존 | 한 번의 Attention으로 직접 참조 |
| **학습/추론 속도** | 상대적으로 느림 | 중간 | 빠름 (특히 학습) |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Self-Attention Only 아키텍처** | 인코더/디코더 모두 Attention과 FFN으로 구성해 순환 구조 제거 |
| **Multi-Head Attention** | 서로 다른 표현 서브공간에서 병렬적으로 관계를 포착해 표현력 강화 |
| **Positional Encoding** | 순환 구조가 없는 대신 위치 정보를 주입해 토큰 순서 인식 |
| **SOTA + 효율성** | WMT 번역 과제에서 성능 향상과 학습 비용 절감을 동시에 달성 |

![Figure 1](./images/1706.03762/figure%20(1).png)
*Figure 1 | Transformer 전체 구조. 인코더/디코더 스택, Multi-Head Attention, FFN, Residual + LayerNorm 블록으로 구성됨.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **아키텍처 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Encoder Stack (N=6)** | Self-Attention + Position-wise FFN 반복. 입력 문맥을 컨텍스트 표현으로 변환 |
| **Decoder Stack (N=6)** | Masked Self-Attention으로 미래 토큰 차단, Encoder-Decoder Attention으로 소스 참조 |
| **Multi-Head Attention** | `h=8` 헤드로 분할해 다양한 관계 패턴을 병렬 학습 |
| **FFN** | 각 토큰별로 동일하게 적용되는 2층 MLP (`d_model=512`, `d_ff=2048`) |
| **Residual + LayerNorm** | 깊은 네트워크 학습 안정화 및 정보 보존 |

* **훈련 과정**:

| 단계 | 특징 |
|---|---|
| **입력 임베딩 + 위치 인코딩** | 토큰 임베딩에 사인/코사인 기반 위치 정보 추가 |
| **Attention 연산** | Scaled Dot-Product Attention으로 Query-Key 정합 후 Value 가중합 계산 |
| **목적함수 최적화** | Label Smoothing(0.1) 적용한 Cross-Entropy 최소화 |
| **최적화 스케줄** | Adam + Warmup 기반 학습률 스케줄로 초기 학습 안정화 |

> **💡 추가 해설: 왜 "Scaled" Dot-Product인가**
> - 내적값이 커지면 Softmax가 급격히 포화되어 학습이 불안정해질 수 있음.
> - 그래서 차원 크기 $d_k$의 제곱근으로 나눠 분산을 제어함.
> - 핵심 식은 다음과 같음.
> - $$\mathrm{Attention}(Q, K, V)=\mathrm{softmax}\left(\frac{QK^\top}{\sqrt{d_k}}\right)V$$

![Figure 2](./images/1706.03762/figure%20(2).png)
*Figure 2 | (좌) Scaled Dot-Product Attention, (우) Multi-Head Attention. 여러 헤드가 서로 다른 관계를 병렬로 포착함.*

![Figure 3](./images/1706.03762/figure%20(3).png)
*Figure 3 | Positional Encoding의 주파수 패턴. 토큰 위치 정보를 연속 함수 형태로 주입함.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | Base 모델 | Big 모델 |
|---|---|---|
| **Layers (Enc/Dec)** | 6 / 6 | 6 / 6 |
| **d_model** | 512 | 1024 |
| **Heads** | 8 | 16 |
| **d_ff** | 2048 | 4096 |
| **Dropout** | 0.1 | 0.3 |

* **벤치마크 결과 (SOTA 달성)**:

| 평가 항목 | 결과 요약 |
|---|---|
| **WMT 2014 En→De** | Transformer (Big) 28.4 BLEU로 당시 최고 성능 달성 |
| **WMT 2014 En→Fr** | 41.8 BLEU 달성, 단일 모델 기준 매우 강력한 성능 |
| **학습 효율성** | 기존 RNN 계열 대비 더 적은 학습 비용으로 높은 품질 확보 |

* **정성적 결과 (Qualitative Results)**:
  - Attention 시각화에서 문법적 관계(주어-동사, 수식 관계)와 장거리 정렬(Alignment)을 명확히 포착하는 패턴 확인됨.
  - 번역 시 입력 문장 전체를 동시에 참조하므로 문맥 일관성이 좋아지고 정보 누락이 줄어듦.

* **절제 연구 (Ablation Studies)**:
  - 헤드 수를 줄이면 표현 다양성이 감소해 성능 저하가 나타남.
  - 위치 인코딩 제거 시 단어 순서 정보 손실로 번역 품질이 떨어짐.
  - Self-Attention 층 수를 줄이면 장거리 의존성 학습 이점이 약해짐.

![Figure 4](./images/1706.03762/figure%20(4).png)
*Figure 4 | 레이어별 Attention 패턴 예시. 헤드마다 서로 다른 문법/정렬 신호를 학습함.*

![Figure 5](./images/1706.03762/figure%20(5).png)
*Figure 5 | 모델 구조별 연산 복잡도와 경로 길이 비교. Transformer가 병렬성과 장거리 연결에서 강점을 가짐.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - Transformer는 "시퀀스 모델링 = 순환 구조"라는 고정관념을 깨고, Self-Attention 중심 설계로 성능과 효율을 동시에 끌어올린 아키텍처임.
  - 이후 BERT, GPT 계열을 포함한 대다수 현대 NLP/멀티모달 모델의 공통 토대가 됨.

* **한계점 (Limitations)**:
  - Self-Attention의 시퀀스 길이 기준 메모리/연산 복잡도는 $O(n^2)$라 초장문 처리에서 비용이 급증함.
  - 원 논문은 주로 번역 태스크 중심 검증이라, 이후 다양한 도메인 확장 과정에서 추가 설계가 필요했음.

* **시사점 및 활용 방안**:
  - 대규모 병렬 학습 인프라와 결합할 때 Transformer의 장점이 극대화됨.
  - 현재 실무에서는 Long Context를 위한 Sparse/Linear Attention 계열 개선이 필수적이며, 이는 원 논문의 한계를 계승한 핵심 연구 축임.
  - 에이전트/멀티모달 시스템에서도 "모든 모달리티를 토큰화하고 Attention으로 통합"하는 방향이 주류가 된 출발점으로 볼 수 있음.
