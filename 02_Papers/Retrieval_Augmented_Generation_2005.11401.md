# [논문 스터디] Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (RAG)

> - **작성일**: 2026.04.17
> - **Institution**: Facebook AI Research
> - **Year/Venue**: 2020 / NeurIPS 2020
> - **Paper**: [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)
> - **Code**: [Hugging Face RAG](https://huggingface.co/docs/transformers/model_doc/rag)
> - **한줄평**: 파라메트릭 메모리(LLM)와 비파라메트릭 메모리(검색 인덱스)를 결합해, 지식 집약형 질의응답의 패러다임을 바꾼 RAG 원전.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - 대형 언어모델은 파라미터에 지식을 저장하지만 최신성/근거성/정밀 업데이트가 어려움.
  - 지식집약 태스크(Open-domain QA)에서는 단일 seq2seq의 한계가 뚜렷함.
  - RAG는 외부 위키 인덱스를 검색해 근거 문서를 생성 모델에 결합하는 구조를 제시함.

* **모델 진화/비교**:

| 비교 항목 | Parametric-only Seq2Seq | RAG |
|---|---|---|
| **지식 저장 위치** | 모델 파라미터 내부 | 파라미터 + 외부 검색 인덱스 |
| **최신성 갱신** | 재학습 필요 | 인덱스 갱신으로 대응 가능 |
| **근거 제시** | 제한적 | 검색 문서 기반 근거 가능 |
| **지식집약 태스크 성능** | 한계 존재 | 개선 |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Parametric + Non-parametric 결합** | 생성 모델과 dense retriever를 통합 학습/추론 |
| **RAG-Sequence / RAG-Token** | 문서 조건화 방식을 두 가지로 제시 |
| **SOTA 실증** | 다수 open-domain QA에서 기존 모델 대비 성능 향상 |

![Figure 1](./images/2005.11401/figure%20(1).png)
*Figure 1 | RAG의 검색-생성 결합 구조.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **아키텍처 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Retriever (DPR)** | 질의와 관련 문서를 dense retrieval로 탐색 |
| **Generator (BART/T5 계열)** | 검색 문서 조건 하에서 답변 생성 |
| **RAG-Sequence** | 생성 전체에서 동일 문서 집합 조건화 |
| **RAG-Token** | 토큰별로 문서 조건이 달라질 수 있는 유연한 방식 |

* **훈련/추론 과정**:

| 단계 | 특징 |
|---|---|
| **문서 인덱싱** | 위키 기반 dense index 구축 |
| **질의 검색** | top-k 문서 retrieve |
| **조건부 생성** | 검색 문서를 컨텍스트로 답변 생성 |
| **학습 목적** | 정답 likelihood 최대화 + 검색-생성 결합 최적화 |

> **💡 추가 해설: 왜 RAG가 중요했는가**
> - "모든 지식을 모델에 넣자"에서 "필요한 지식을 그때 가져오자"로 관점을 전환함.
> - 이후 대부분의 지식형 LLM 시스템이 RAG 변형을 기본 구조로 채택함.

![Figure 2](./images/2005.11401/figure%20(2).png)
*Figure 2 | RAG-Sequence와 RAG-Token의 조건화 차이.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | 디테일 |
|---|---|
| **태스크** | Natural Questions, TriviaQA, WebQuestions 등 |
| **비교군** | Parametric seq2seq, retrieve-and-extract 계열 |
| **지표** | Exact Match, F1 등 QA 표준 지표 |

* **벤치마크 결과**:

| 평가 항목 | 결과 요약 |
|---|---|
| **Open-domain QA** | 다수 벤치마크에서 SOTA 달성/근접 |
| **생성 품질** | 더 구체적이고 사실성 높은 생성 경향 |
| **유연성** | 인덱스 교체로 도메인 확장 가능성 확보 |

* **정성적 결과**:
  - 답변 근거를 검색 문서에서 확인할 수 있어 신뢰성 확보에 유리함.
  - 지식 업데이트가 필요한 서비스 시나리오에서 실용성이 큼.

* **절제 연구**:
  - top-k 수, retriever 품질, sequence/token 조건화 방식 비교.
  - retriever 품질이 전체 성능 상한을 크게 좌우함을 확인.

![Figure 3](./images/2005.11401/figure%20(3).png)
*Figure 3 | 주요 QA 벤치마크 성능 비교.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - RAG는 LLM 지식 한계를 외부 메모리와 결합해 보완하는 표준 해법의 출발점.
  - 오늘날의 엔터프라이즈 QA/검색형 챗봇 아키텍처에 직접적 영향을 줌.

* **한계점 (Limitations)**:
  - 검색 실패 시 생성 품질이 함께 하락하는 retrieval bottleneck 문제.
  - 인덱스 구축/갱신 비용과 운영 복잡도 존재.

* **시사점 및 활용 방안**:
  - 도메인 지식 기반 챗봇/문서 QA에서 기본 구조로 채택 가능.
  - 후속 연구(Self-RAG, Corrective RAG 등)의 비교 기준점으로 유효함.
