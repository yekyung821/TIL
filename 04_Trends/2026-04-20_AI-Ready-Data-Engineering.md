## 📌 Topic: AI-Ready Data Engineering
**Tags:** #DataEngineering #LLMOps #DataGovernance #Reliability

> **Source:** [Data Engineering Weekly #266](https://www.dataengineeringweekly.com/p/data-engineering-weekly-266)
>
> **Date:** 2026-04-20

---

### 1. Context: Why now?

- 최근 데이터 플랫폼의 핵심 Pain Point는 더 이상 "모델 성능"이 아니라 **운영 환경의 불확실성**이다. 실제 장애는 LLM 자체보다 데이터 품질, Context 관리, 거버넌스 부재, 비용 통제 실패에서 훨씬 자주 발생한다.
- Legacy 방식은 데이터 성숙도를 단일 축으로 다뤘다. Analytics 중심 파이프라인은 Aggregation과 안정성에는 강하지만, AI가 필요로 하는 Context completeness, freshness, semantic richness를 잃기 쉽다.
- 생성형 AI와 Agent 도입이 확산되며, 데이터 플랫폼은 단순 BI 백엔드에서 **실시간 의사결정 + 안전한 추론 + 추적 가능한 운영**을 동시에 만족해야 하는 레이어로 이동 중이다.

### 2. Core Mechanism: How it works?

이번 주 원문 묶음에서 반복된 공통 구조는 다음 4개 축으로 요약된다.

1. **Reliability by design**: Idempotency key, deduplication, schema validation, replay-safe recovery  
2. **Context control**: 장기 실행 Agent에서 working memory / journal + critic 기반 검증 계층  
3. **Governance-first**: business glossary, catalog, lineage, semantic layer로 AI query 신뢰성 확보  
4. **Cost-aware orchestration**: Agent 자동화에 guardrail을 두어 고비용 실험을 사전 차단

**Data Flow (요약):**

```
Raw Events
  → Validation / Dedup
  → Dual Storage (Hot / Cold)
  → Feature / Profile / Serving
  → LLM / Agent Inference
  → Eval / Feedback Loop
  → Governance (Lineage / Ownership)
```

**인상적이었던 구현 패턴:**

- **Atlassian Forge Billing**: 대규모 usage event를 deterministic pipeline으로 처리하고 charge traceability를 보장.
- **Whatnot LLM Platform**: velocity / reliability / trust 3축으로 운영, post-exposure A/B logging과 calibration으로 drift 조기 감지.
- **Slack Agentic App**: 장기 Context 오염을 채널 분리(Director / Critic) 구조로 제어.

### 3. DE Insight: Engineering Impact

실무 관점에서 이 흐름이 주는 이점 3가지.

- **신뢰성(Trust) 향상**  
  Non-deterministic한 LLM 출력이라도 logging, evaluation, calibration 계층을 붙이면 운영 품질을 계량화할 수 있다.
- **Latency / Cost 최적화**  
  request-level dedup, profile write-time merge, hot / cold tiering은 Throughput을 유지하면서 인프라 비용을 줄이는 직접적인 수단이다.
- **운영 복구력(Recoverability) 강화**  
  replay-safe 설계, inverse operation, lineage 기반 추적은 장애 시 MTTR 단축과 감사 대응력을 동시에 높인다.

**Modern Data Stack에서의 위치:**  
이 트렌드는 단순 ETL 고도화가 아니라 **Data Platform + LLMOps + Governance** 통합 레이어를 요구한다.  
무게중심이 "warehouse 중심 분석 스택"에서 "AI 서비스 운영 스택"으로 이동하고 있다.

### 4. Critical Thinking: Trade-offs & Limits

- **부작용 / 비용**
  - 관측성, 품질 테스트, lineage, semantic layer까지 모두 갖추면 초기 구축 비용과 운영 복잡도가 급격히 올라간다.
  - Agent 자동화는 실험 속도를 올리지만, 잘못된 self-retry 루프는 클라우드 비용 폭증으로 이어질 수 있다.
- **부적합한 환경**
  - 데이터 볼륨이 작고 변경이 드문 팀에는 과도한 거버넌스 계층이 오히려 개발 속도를 저해할 수 있다.
  - 소규모 팀이 전사급 플랫폼 패턴을 그대로 도입하면 운영 인력 부족으로 "관리되는 복잡성"이 아닌 "방치된 복잡성"으로 귀결된다.

### 5. Action Plan: Link to My Projects

- **오늘 한 일**
  - Data Engineering Weekly #266의 주요 아티클을 읽고, 공통 패턴(신뢰성 · 거버넌스 · 비용 통제) 기준으로 재분류.
  - "모델 개선"보다 "운영 안정성"이 우선이라는 기준을 내 프로젝트 점검 항목으로 정리.
- **다음 적용 항목 (작게 시작)**
  - Steam API 파이프라인: `idempotent upsert` 체크리스트 1페이지 작성.
  - RAG / Agent 프로젝트: model · prompt 변경 시 회귀 테스트 5문항부터 고정.
  - 공통: 데이터 자산 owner / lineage / 품질 지표를 담은 lightweight 운영 문서 초안 작성.
- **한 줄 회고**  
  오늘은 구현보다 구조를 정리한 날. 앞으로는 **"AI-ready 데이터"**와 **"analytics-ready 데이터"**를 분리해 설계하는 기준을 지속적으로 유지할 계획.
