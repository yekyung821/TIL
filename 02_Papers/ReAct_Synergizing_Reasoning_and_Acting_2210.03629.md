# [논문 스터디] ReAct: Synergizing Reasoning and Acting in Language Models

> - **작성일**: 2026.04.17
> - **Institution**: Princeton University, Google Research 외
> - **Year/Venue**: 2022 / ICLR 2023 (Workshop/Poster 계열 공개)
> - **Paper**: [arXiv:2210.03629](https://arxiv.org/abs/2210.03629)
> - **Project**: [ReAct Project Page](https://react-lm.github.io/)
> - **한줄평**: "생각(Reasoning)과 행동(Action)을 번갈아 수행"하는 간단한 프롬프트 포맷으로, 에이전트형 LLM의 기본 문법을 만든 논문.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - CoT는 추론에는 강하지만 외부 지식 갱신이나 환경 상호작용에는 약함.
  - 반대로 행동 중심 에이전트는 계획/설명 가능성이 부족해 오류 원인 추적이 어려움.
  - ReAct는 reasoning trace와 action을 인터리브해 두 접근의 장점을 결합함.

* **모델 진화/비교**:

| 비교 항목 | CoT-only | Act-only | ReAct |
|---|---|---|---|
| **추론 설명 가능성** | 높음 | 낮음 | 높음 |
| **외부 정보 활용** | 제한적 | 높음 | 높음 |
| **환각 대응** | 취약 | 중간 | 상대적 강건 |
| **에이전트 적용성** | 중간 | 높음 | 높음 |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Thought-Action-Observation 루프** | 추론과 도구 사용을 한 궤적으로 통합 |
| **해석 가능성 강화** | 중간 reasoning trace로 정책 의사결정 추적 가능 |
| **범용성 실증** | QA, fact verification, embodied/web tasks에서 성능 향상 |

![Figure 1](./images/2210.03629/figure%20(1).png)
*Figure 1 | ReAct의 Thought-Action-Observation 인터리브 구조.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Prompting)
* **프롬프트 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Thought** | 현재 상태 해석, 계획 업데이트 |
| **Action** | API 호출/환경 명령 등 외부 상호작용 |
| **Observation** | 도구/환경에서 받은 피드백 |
| **Final Answer** | 루프 종료 후 최종 응답 생성 |

* **실행 과정**:

| 단계 | 특징 |
|---|---|
| **초기 상태 인식** | 문제와 목표를 파악 |
| **행동 선택** | 필요한 경우 검색/탐색 실행 |
| **관측 반영** | 관측값으로 추론 경로 수정 |
| **종료 조건 판단** | 충분한 근거 시 답변 생성 |

> **💡 추가 해설: 왜 ReAct가 환각을 줄일 수 있나**
> - 모델이 내부 파라미터 기억만 믿지 않고, 필요한 순간 외부 근거를 조회함.
> - 틀린 가설이 생겨도 observation 단계에서 경로 수정이 가능함.

![Figure 2](./images/2210.03629/figure%20(2).png)
*Figure 2 | QA 태스크에서 ReAct trajectory 예시.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | 디테일 |
|---|---|
| **언어 태스크** | HotpotQA, FEVER |
| **결정/상호작용 태스크** | ALFWorld, WebShop |
| **비교군** | CoT prompting, imitation/RL 기반 정책 |

* **벤치마크 결과**:

| 평가 항목 | 결과 요약 |
|---|---|
| **HotpotQA/FEVER** | CoT 대비 환각 및 오류 전파 완화, 성능 향상 |
| **ALFWorld/WebShop** | 소수 예시 prompting만으로 RL/IL 대비 높은 성공률 |
| **해석 가능성** | 사람 검토 가능한 trajectory 제공으로 신뢰성 향상 |

* **정성적 결과**:
  - 중간 thought를 통해 모델의 오판 지점을 명확히 추적 가능.
  - 도구 호출 실패/정보 부족 상황에서 적응적 재시도가 가능함.

* **절제 연구**:
  - reasoning만/acting만 분리 실험 대비 결합형(ReAct)이 전반적으로 안정적.

![Figure 3](./images/2210.03629/figure%20(3).png)
*Figure 3 | 태스크별 성능 비교 및 trajectory 품질 예시.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - ReAct는 에이전트형 LLM에서 "생각 + 행동 + 관측" 루프를 표준화한 기초 연구임.
  - 이후 Tool-use, Agent framework, LangGraph류 설계에 직접적인 영향을 줌.

* **한계점 (Limitations)**:
  - 긴 trajectory로 토큰 비용이 커질 수 있음.
  - thought 품질이 낮거나 action space가 크면 탐색 오류가 누적될 수 있음.

* **시사점 및 활용 방안**:
  - 실무 에이전트에서 `plan/act/observe` 상태머신 설계의 근거로 유효함.
  - 근거 기반 응답(검색+생성) 시스템에서 디버깅 가능성을 크게 높여줌.
