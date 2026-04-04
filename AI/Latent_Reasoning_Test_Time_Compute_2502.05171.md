# Scaling up Test-Time Compute with Latent Reasoning (arXiv:2502.05171)

**원문:** [arXiv:2502.05171](https://arxiv.org/abs/2502.05171) · [HTML (그림 포함)](https://arxiv.org/html/2502.05171v2)  
**제목:** *Scaling up Test-Time Compute with Latent Reasoning: A Recurrent Depth Approach*  
**저자:** Jonas Geiping et al.

아래 그림은 논문 저자가 arXiv HTML 버전에 게시한 자료를 **원문 링크로만** 삽입한 것입니다. GitHub에서 미리보기 시 네트워크로 불러와야 보일 수 있습니다.

---

## Research Question

테스트타임 연산을 **더 많은 토큰(특히 긴 Chain-of-Thought)으로 늘리는 주류 방식** 말고, **연속 잠재공간에서의 반복 계산**만으로 추론 성능을 끌어올릴 수 있는가? 그리고 그런 구조를 **대규모 사전학습**까지 확장했을 때, 일반 추론·수학·코딩 벤치에서 **실질적인 이득**이 나타나는가?

**Figure 1 (논문 그림):** 테스트타임에 반복 횟수를 늘려 잠재공간에서 추론하는 개념과, 과제별로 필요한 연산이 다름을 시각화.

![Figure 1 — Latent test-time reasoning vs. long CoT (논문 원본)](https://arxiv.org/html/2502.05171v2/x1.png)

*캡션 요약: 3.5B 규모 depth-recurrent LM. 테스트타임에 더 긴 반복으로 연산을 늘리면 성능이 오름. 긴 CoT로 말로 풀기보다 잠재공간에서 추론. OpenBookQA는 빨리 수렴하고 GSM8K 등은 더 많은 연산을 활용.*

---

## Proposed Method

- **Depth-recurrent LM:** 디코더형 트랜스포머를 **Prelude \(P\)** → **공유 반복 코어 \(R\)** → **Coda \(C\)** 로 구성.
- **잠재 상태:** \(e = P(x)\), 무작위 초기 \(s_0\)에서 \(s_i = R(e, s_{i-1})\)를 \(r\)번 반복 후 \(p = C(s_r)\)로 다음 토큰 분포 예측.
- **학습:** 매 스텝마다 반복 횟수 \(r\)을 **로그정규–푸아송 혼합**으로 샘플링해 기대 손실 최적화. **마지막 \(k\)스텝(본문 \(k=8\))에 대해서만 truncated backprop**으로 메모리 절약.
- **세부:** RoPE, RMSNorm, gated SiLU MLP; 코어에서 **\(s\)와 \(e\)의 concat → adapter**로 순환 안정화; 대규모에서는 **sandwich-style norm**이 필요.

**Figure 2 (논문 그림):** Prelude(파랑) → 반복되는 Recurrent block(초록) → Coda(빨강) 아키텍처.

![Figure 2 — Architecture: Prelude, recurrent block, Coda (논문 원본)](https://arxiv.org/html/2502.05171v2/x2.png)

**Figure 3 (논문 그림):** 학습 시 매 스텝에서 반복 횟수를 샘플링하는 분포(로그정규–푸아송).

![Figure 3 — Log-normal Poisson distribution for training-time recurrence sampling (논문 원본)](https://arxiv.org/html/2502.05171v2/x3.png)

---

## Experimental Setup & Results

- **모델·학습:** 약 **3.5B 파라미터**, **800B 토큰** 규모의 단일 대규모 러닝; 데이터는 **코드·수학·과학 비중이 큰 공개 혼합** + 지시 데이터 일부, 패킹 길이 **4096**, BPE vocab **65536**.
- **평가:** lm-eval-harness(ARC, HellaSwag, MMLU, OpenBookQA 등), 수학(GSM8K, MATH, MathQA), 코드(HumanEval, MBPP), bigcode-bench 등.
- **요지:** 공개 데이터 기준 유사 규모와 비교 시 Pythia 상회, 다수 지표에서 초기 OLMo-7B급과 유사하나 후기 OLMo에는 열세(저자 서술). 수학·코딩에서 비교군 대비 강한 면이 있으나 코드 전용 모델(예: StarCoder2)에는 미달. **비반복 트윈(180B)** 대비 어려운 과제에서 반복 구조 이득이 큼. **테스트타임 \(r\) 증가**에 따라 GSM8K CoT·HumanEval 등이 개선; 쉬운 과제는 적은 반복에서 포화. EMA + 높은 \(r\)에서 GSM8K CoT **flexible ~47.23% / strict ~38.59%** 등 수치 보고. 초록에서는 연산 부담을 **훨씬 큰 고정깊이 모델(최대 ~50B급 연산)**에 비유 가능한 이득을 주장.

**Figure 4 (논문 그림):** 사전학습 데이터 소스 분포(웹·과학 글·코드 등).

![Figure 4 — Pretraining data source distribution (논문 원본)](https://arxiv.org/html/2502.05171v2/x4.png)

**Figure 7 (논문 그림):** 반복 횟수(연산) 증가에 따른 GSM8K CoT, HellaSwag, HumanEval 성능 변화.

![Figure 7 — Benchmark performance vs. test-time recurrences (논문 원본)](https://arxiv.org/html/2502.05171v2/x8.png)

*캡션 요약: 연산을 늘리면 벤치 성능이 상승. HellaSwag는 약 8회 반복 부근에서 거의 포화, 다른 벤치는 더 많은 반복을 활용.*

---

## Innovation

1. **테스트타임 스케일링 축:** 주류 “긴 CoT·더 많은 생성 토큰”과 달리, 토큰 길이를 늘리지 않고 **잠재공간에서 동일 블록 \(R\)을 여러 번 unroll**해 연산만 늘리는 **제3의 스케일링 축**을 제시.
2. **데이터·표현 제약 완화:** CoT 전용 긴 데모 없이 일반 사전학습이 가능하고, **짧은 컨텍스트**로 동작하며, **언어로 표현하기 어려운 추론**을 잠재공간에서 다룰 여지를 연다.

**Figure 12 (논문 그림):** 선택 토큰에서 잠재 상태 궤적(PCA 6차원). 수렴, 궤도(orbit), 슬라이더 등 패턴이 산술·복잡한 숙고에 쓰인다고 저자가 해석.

![Figure 12 — Latent space trajectories (PCA); convergence, orbits, sliders (논문 원본)](https://arxiv.org/html/2502.05171v2/x14.png)

---

## Future Work

- **단일 대규모 러닝의 proof-of-concept:** 학습률 스케줄·데이터 믹스·가속기를 더 최적화한 재학습 여지.
- **포스트트레이닝:** 반복 **압축** 파인튜닝, 난이도 다른 데이터 **RL**, CoT 추론을 순환 **내부로 내재화** 등.
- **아키텍처:** 연산 집약적 recurrent 구조와 **MoE**의 상보성, **recurrent MoE** 같은 결합을 제안.
- 데이터 혼합 **ablation** 부족, 분산 학습에서 깊이 샘플링 **스케줄링** 개선 등 본문에서 한계로 언급.

---

## 그림 사용에 대해

위 이미지 URL은 **arXiv가 호스트하는 논문 HTML 변환본**의 경로입니다. 논문 전체 PDF·저작권·라이선스는 [arXiv 논문 페이지](https://arxiv.org/abs/2502.05171)를 따릅니다. 오프라인·방화벽 환경에서는 그림이 보이지 않을 수 있으니, 그때는 [HTML 버전](https://arxiv.org/html/2502.05171v2)에서 동일 Figure를 확인하면 됩니다.
