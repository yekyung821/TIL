# 📚 LangGraph 개념 완전 정복 및 Agentic RAG (심화 RAG) 학습 정리

## 1. 개요
* **날짜**: 2026-03-31
* **주제**: LangGraph의 핵심 개념 이해 및 이를 활용한 심화 RAG (Agentic RAG) 구축 파이프라인
* **출처**: [🔥 #LangGraph 개념 완전 정복 몰아보기(3시간) 🔥](https://www.youtube.com/watch?v=W_uwR_yx4-c&t=9867s)

기존 RAG(Retrieval-Augmented Generation)는 단순히 문서를 찾아 LLM에게 던져주고 답변을 생성하게 하는 단방향 구조(Naive RAG)였습니다. 그러나 문서 검색 실패나 환각(Hallucination) 등의 치명적인 한계가 있었습니다. 이를 극복하기 위해 LangGraph를 도입하여 스스로 판단하고 보완하는 '에이전트 워크플로우(Agentic RAG)'로 진화시키는 방법을 학습했습니다.

---

## 2. LangGraph의 핵심 구성 요소
LangGraph는 복잡한 LLM 애플리케이션(에이전트)을 상태 머신(State Machine) 형태로 제어할 수 있게 해주는 라이브러리입니다.

* **State (상태)**: 그래프를 도는 동안 전역적으로 유지되고 업데이트되는 데이터 객체. 딕셔너리나 Pydantic 모델로 정의됩니다. (예: `{"question": "...", "documents": [...], "generation": "..."}`)
* **Node (노드)**: 실제 특정 작업을 수행하는 파이썬 함수. (예: 문서 검색 노드, 문서 평가 노드, 답변 생성 노드)
* **Edge (엣지)**: 노드와 노드를 연결하는 흐름. 한 노드 작업이 끝나면 다음 노드로 넘어갑니다.
* **Conditional Edge (조건부 엣지)**: 현재 상태에 따라 다음 경로를 동적으로 결정하는 논리 분기점. (예: 문서가 관련 없으면 "웹 검색" 노드로, 관련 있으면 "답변 생성" 노드로 이동)

---

## 3. 심화 RAG (Self-RAG / CRAG) 워크플로우

단순히 검색(Retrieve) $\to$ 생성(Generate)에서 끝나는 것이 아니라, 여러 단계의 **검증과 순환(Cycle) 구조**를 가집니다.

### ① 문서 검색 (Retrieve)
사용자의 질문(`question`)을 바탕으로 Vector DB에서 관련 문서를 검색합니다.

### ② 문서 적합성 평가 (Grade Documents)
* **내용**: 검색된 문서가 질문에 답변하기에 충분하고 관련성이 높은지(Relevant) 평가합니다.
* **판단 (Conditional Edge)**:
  * 관련성 높음 (yes) $\to$ 답변 생성(Generate) 단계로 이동
  * 관련성 낮음 (no) $\to$ 문서가 부족하므로 질문을 재작성(Rewrite) 하거나 웹 검색(Web Search) 단계로 우회

### ③ 웹 검색 (Web Search) 및 질문 재작성 (Rewrite)
* 부족한 정보를 보완하기 위해 쿼리를 다듬고, Tavily 등의 검색 도구를 활용하여 외부 인터넷에서 최신 정보를 가져와 `documents` 상태에 추가합니다.

### ④ 답변 생성 (Generate)
* 수집된(필터링 및 보완된) 문서들을 바탕으로 LLM이 최종 답변(`generation`)을 생성합니다.

### ⑤ 환각 검사 (Hallucination Check) 및 정답 평가
* **환각 검사**: 생성된 답변이 검색된 문서(Fact)에 기반하고 있는지 점검합니다. 만약 문서에 없는 내용을 지어냈다면(Hallucination), 다시 답변 생성(Generate) 단계로 돌아갑니다.
* **정답 평가**: 답변이 사용자의 원본 질문을 완벽히 해결했는지 확인합니다.
  * 해결됨 (useful) $\to$ 사용자에게 최종 답변 반환 (END)
  * 해결안됨 (not useful) $\to$ 질문 재작성(Rewrite) 노드로 돌아가 전체 과정을 다시 시작.

---

## 4. 왜 LangGraph인가? (LangChain LCEL과의 차이)
* **순환(Cycle) 구조의 지원**: 기존 LCEL(LangChain Expression Language)은 단방향 파이프라인(DAG) 형태라서 루프를 도는 구조를 만들기가 매우 까다로웠습니다. 
* **체계적인 제어력 보장**: LangGraph는 에러가 발생하거나 검증에 실패했을 때 이전 노드로 되돌아가는(Loop) 과정을 `Conditional Edge`로 아주 우아하게 처리할 수 있습니다.
* **휴먼 인 더 루프 (Human-in-the-loop)**: 특정 노드에서 실행을 일시 정지하고, 사람이 직접 개입(승인/수정)한 뒤 진행할 수 있는 제어 기능도 매우 강력합니다.

---

## 5. 💡 오늘의 인사이트
LangGraph를 통해 RAG를 구축하면, LLM이 단순히 생성기 역할을 넘어서서 "스스로 검색 결과를 검열하고, 정보가 부족하면 인터넷을 뒤져서라도 알아오고, 자기가 쓴 답변을 스스로 팩트 체크하는" 훌륭한 연구원(Agent)처럼 동작하게 만들 수 있습니다.

단순 RAG 파이프라인을 구축해본 경험에서 머무르지 않고, 앞으로는 이런 **자기 수정(Self-Correction)**이 가능한 Agentic RAG 구조로 고도화하는 것이 실무에서 AI의 신뢰성을 높이는 핵심 기술이 될 것 같습니다.
