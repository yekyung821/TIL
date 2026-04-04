# 테스트타임 연산 × 잠재공간 추론 (arXiv:2502.05171)

테스트타임 연산(test-time compute) 쪽을 공부하면서, 토큰을 길게 늘리는 CoT 말고 **잠재공간에서 반복만으로** 성능을 올리는 논문을 읽고 정리했다.

- **링크:** [arXiv:2502.05171](https://arxiv.org/abs/2502.05171) · 그림 잘 나온 [HTML](https://arxiv.org/html/2502.05171v2)
- **제목:** *Scaling up Test-Time Compute with Latent Reasoning: A Recurrent Depth Approach*
- **저자:** Jonas Geiping et al.

그림은 HTML 페이지에 올라온 걸 그대로 링크해 뒀다. 미리보기에서 안 뜨면 HTML 쪽을 열면 된다.

---

## Research Question

요즘 흔한 방식은 긴 Chain-of-Thought로 **생성 토큰 수**를 늘려 테스트타임 연산을 키우는 건데, 이 논문은 그 대신 **연속 잠재공간에서 같은 블록을 여러 번 돌리는** 쪽이 통할지 묻는다. 그리고 그걸 **대규모 사전학습**까지 가져갔을 때, 일반 벤치·수학·코딩에서 실제로 이득이 있는지 본다.

**그림 1.** 반복 횟수를 늘리면서 잠재공간에서 추론하는 그림. 과제마다 필요한 반복이 다르다는 것도 같이 적혀 있다.

![Fig.1](https://arxiv.org/html/2502.05171v2/x1.png)

논문 캡션 기준으로는 3.5B depth-recurrent LM이고, OpenBookQA는 빨리 수렴하는데 GSM8K 쪽은 반복을 더 쓰는 식.

---

## Proposed Method

읽으면서 적은 구조 메모:

- **블록 나눔:** 디코더 트랜스포머를 **Prelude \(P\)** → **공유 반복 코어 \(R\)** → **Coda \(C\)**.
- **순전파:** \(e = P(x)\), \(s_0\)는 랜덤 초기화, \(s_i = R(e, s_{i-1})\)를 \(r\)번 반복한 뒤 \(p = C(s_r)\)로 다음 토큰 확률.
- **학습:** 매 스텝마다 \(r\)을 **로그정규–푸아송**으로 뽑아서 기대 손실 최적화. 역전파는 **마지막 \(k\)스텝만** (본문 \(k=8\)) — 메모리 아끼려고 truncated backprop.
- **구현 디테일:** RoPE, RMSNorm, gated SiLU MLP. 코어는 \(s\)랑 \(e\) **concat → adapter**로 안정화. 큰 모델에서는 **sandwich-style norm**이 꼭 필요했다고 함.

**그림 2.** 파란 Prelude → 초록 recurrent(반복) → 빨간 Coda.

![Fig.2](https://arxiv.org/html/2502.05171v2/x2.png)

**그림 3.** 학습할 때 매 배치마다 반복 횟수를 어떤 분포로 샘플하는지.

![Fig.3](https://arxiv.org/html/2502.05171v2/x3.png)

---

## Experimental Setup & Results

- **스케일:** 대략 **3.5B 파라미터**, **800B 토큰** 한 번 큰 러닝. 데이터는 코드·수학·과학 비중 큰 공개 믹스 + 지시 데이터 조금, 시퀀스 **4096**, BPE **65536**.
- **벤치:** lm-eval-harness 쪽(ARC, HellaSwag, MMLU, OpenBookQA 등), 수학 GSM8K/MATH/MathQA, 코드 HumanEval/MBBP, bigcode-bench 등.
- **내가 이해한 요약:** 공개 데이터 기준으로 Pythia보다는 낫고, 어떤 지표는 초기 OLMo-7B 비슷한데 더 큰 OLMo에는 밀린다고 적혀 있음. 수학·코딩은 비교군 중에서는 괜찮은데 StarCoder2 같은 코드 전용에는 못 미침. **같은 설정의 비반복 모델(180B까지)**이랑 비교하면 어려운 과제에서 recurrent 쪽 이득이 크다고 함. 테스트타임에 **\(r\)만 키우면** GSM8K CoT·HumanEval이 따라 올라가고, HellaSwag 같은 건 반복 몇 번이면 포화에 가깝다고 함. EMA랑 높은 \(r\) 조합 예시로 GSM8K CoT flexible **~47.23%**, strict **~38.59%** 같은 숫자도 나옴. 초록에서는 연산량을 아주 큰 고정깊이 모델급이랑 비유할 수 있다는 식으로 적혀 있음.

**그림 4.** 사전학습에 뭘 얼마나 섞었는지(웹, 과학 글, 코드 등).

![Fig.4](https://arxiv.org/html/2502.05171v2/x4.png)

**그림 7.** 반복 횟수 늘릴수록 GSM8K CoT / HellaSwag / HumanEval이 어떻게 움직이는지. HellaSwag는 대략 8번 근처에서 거의 끝나고, 나머지는 더 길게 쓴다고 함.

![Fig.7](https://arxiv.org/html/2502.05171v2/x8.png)

---

## Innovation (내가 본 차별점)

1. **스케일링 축:** “토큰을 길게 쓴다”가 아니라 **잠재공간에서 \(R\)을 여러 번 unroll**해서 연산만 늘리는 축을 분명히 잡음.
2. **데이터 부담:** 긴 CoT 데모를 따로 만들 필요 없이 일반 사전학습으로 가고, 컨텍스트도 짧게 가져갈 수 있다는 점. 말로 표현하기 애매한 추론을 벡터 쪽에서 처리할 여지를 연다고 읽었다.

**그림 12.** 일부 토큰에서 잠재 궤적을 PCA로 찍은 것. 수렴하는 애, 궤도(orbit) 도는 애, 슬라이더처럼 움직이는 애가 있고, 저자는 산술·복잡한 숙고랑 연결해서 해석함.

![Fig.12](https://arxiv.org/html/2502.05171v2/x14.png)

---

## Future Work (논문에 나온 다음 과제)

- 아직 **한 번 큰 러닝 proof-of-concept** 수준이라, LR 스케줄·데이터 믹스·하드웨어 최적화 여지가 있음.
- 포스트트레이닝으로 반복을 **압축**한다든지, 난이도 섞은 **RL**, CoT를 순환 안으로 **내재화**하는 방향이 언급됨.
- **MoE**랑 같이 쓰면 상보적일 수 있다, **recurrent MoE** 같은 결합도 제안.
- 데이터 믹스 **ablation**은 못 했다고 하고, 분산 학습에서 깊이 샘플링 스케줄도 더 다듬을 수 있다고 적혀 있음.
