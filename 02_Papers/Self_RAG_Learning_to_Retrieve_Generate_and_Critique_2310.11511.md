# [논문 스터디] Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection

> - **작성일**: 2026.04.17
> - **Institution**: University of Washington, Allen Institute for AI 외
> - **Year/Venue**: 2023 / arXiv
> - **Paper**: [arXiv:2310.11511](https://arxiv.org/abs/2310.11511)
> - **Project**: [Self-RAG](https://selfrag.github.io/)
> - **한줄평**: "항상 검색"하는 고정형 RAG에서 벗어나, 필요할 때 검색하고 스스로 비평까지 수행하는 적응형 RAG 프레임워크.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - 기존 RAG는 태스크와 무관하게 고정 개수 문서를 검색/주입하는 경우가 많아 비효율적임.
  - 불필요한 검색은 응답 품질 저하와 비용 증가를 함께 유발함.
  - Self-RAG는 retrieval/generation/critique를 하나의 LM에서 reflection token으로 제어해 적응형 동작을 구현함.

* **모델 진화/비교**:

| 비교 항목 | 고정형 RAG | Self-RAG |
|---|---|---|
| **검색 호출** | 항상/고정 횟수 | 필요 시 on-demand |
| **근거 평가** | 외부 재순위 의존 | 모델 내부 self-critique |
| **제어성** | 낮음 | reflection token으로 높음 |
| **장문 사실성** | 개선 제한 | factuality/citation 정확도 개선 |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Reflection Tokens** | 검색 필요성, 근거 품질, 응답 품질을 토큰으로 명시 |
| **단일 모델 통합** | retrieve-generate-critique를 한 모델에서 수행 |
| **성능 개선** | QA/추론/검증/장문 생성에서 factuality 향상 |

![Figure 1](./images/2310.11511/figure%20(1).png)
*Figure 1 | Self-RAG의 retrieve-generate-critique 통합 흐름.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **아키텍처 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Retriever** | 질의 관련 문서 후보 제공 |
| **Generator (LM)** | 응답 생성과 reflection token 출력 동시 수행 |
| **Critique Signal** | 근거 사용 적절성/사실성 점검 신호 |
| **Inference Control** | reflection token 기반으로 동작 모드 제어 |

* **훈련/추론 과정**:

| 단계 | 특징 |
|---|---|
| **데이터 구성** | 근거 기반 QA/생성 데이터로 supervision 구성 |
| **토큰 학습** | reflection token을 함께 예측하도록 학습 |
| **적응형 검색** | 필요할 때만 retrieval 호출 |
| **출력 필터링** | self-critique 점수를 활용한 응답 선택 |

> **💡 추가 해설: 왜 Self-RAG가 중요한가**
> - 기존 RAG는 "검색은 항상 좋다"는 가정을 둠.
> - Self-RAG는 검색 자체를 의사결정 대상으로 바꿔 비용/품질을 함께 최적화함.

![Figure 2](./images/2310.11511/figure%20(2).png)
*Figure 2 | reflection token을 이용한 추론 제어 예시.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | 디테일 |
|---|---|
| **모델 스케일** | 7B / 13B 계열 실험 |
| **비교군** | ChatGPT, Llama2-chat + RAG baseline |
| **태스크** | Open-domain QA, reasoning, fact verification, long-form generation |

* **벤치마크 결과**:

| 평가 항목 | 결과 요약 |
|---|---|
| **정확도** | 다수 태스크에서 baseline 대비 유의미한 성능 향상 |
| **사실성** | 장문 생성 factuality 및 citation 정확도 개선 |
| **유연성** | 태스크별로 retrieval 강도를 조절해 성능/비용 균형 확보 |

* **정성적 결과**:
  - 잘못된 근거를 무비판적으로 따르지 않고 self-critique로 조정하는 사례 제시.
  - 검색이 불필요한 질문에서 불필요한 retrieval 호출을 줄임.

* **절제 연구**:
  - reflection token 제거 시 성능 및 제어성 하락.
  - 고정형 retrieval 정책 대비 적응형 정책의 이점 확인.

![Figure 3](./images/2310.11511/figure%20(3).png)
*Figure 3 | factuality/citation 관점 성능 비교.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - Self-RAG는 RAG를 "검색+생성 파이프라인"에서 "자기반성 기반 의사결정 시스템"으로 확장함.
  - 에이전트형 RAG 설계에서 중요한 기준점 역할을 함.

* **한계점 (Limitations)**:
  - reflection token 설계/학습 데이터 품질에 민감함.
  - 추론 경로가 길어지면 토큰 비용이 증가할 수 있음.

* **시사점 및 활용 방안**:
  - 실무 RAG에서 retrieval gating 정책과 품질 평가 루프를 설계할 때 직접 적용 가능.
  - 장문 보고서 생성/근거 제시가 중요한 도메인(법률/의료/리서치)에서 활용 가치가 큼.
