## 📌 Topic: AI-Ready Data Engineering (LLM Platform, Governance, Reliability) #DataEngineering #LLMOps #DataGovernance
> **Source:** https://www.dataengineeringweekly.com/p/data-engineering-weekly-266
> **Date:** 2026-04-20

---

### 1. Context: Why now?
- 핵심 Pain Point는 "모델 성능"이 아니라 **운영 환경의 불확실성**이다. 실제 장애는 LLM 자체보다 데이터 품질, Context 관리, 거버넌스 부재, 비용 통제 실패에서 더 자주 발생한다.
- Legacy 방식의 한계는 단일 데이터 성숙도 관점이었다. 기존 Analytics 중심 파이프라인은 Aggregation과 안정성에는 강하지만, AI가 필요로 하는 Context completeness, freshness, semantic richness를 소실시키는 경우가 많다.
- 생성형 AI/Agent 도입이 늘어나며 데이터 플랫폼은 단순 BI 백엔드가 아니라, **실시간 의사결정 + 안전한 추론 + 추적 가능한 운영**을 동시에 만족해야 하는 단계로 이동했다.

### 2. Core Mechanism: How it works?
- 이번 원문 묶음에서 반복된 공통 구조는 다음 4개 축이다.
  1) **Reliability by design**: Idempotency key, deduplication, schema validation, replay-safe recovery  
  2) **Context control**: 장기 실행 Agent에서 working memory/journal + critic-based 검증 계층  
  3) **Governance-first**: business glossary, catalog, lineage, semantic layer로 AI query 신뢰성 확보  
  4) **Cost-aware orchestration**: Agent 자동화에 guardrail을 넣어 고비용 실험을 사전 차단

- Data Flow (요약):

  `Raw Events -> Validation/Dedup -> Dual Storage(Hot/Cold) -> Feature/Profile/Serving -> LLM/Agent Inference -> Eval/Feedback Loop -> Governance(Lineage/Ownership)`

- 특히 인상적인 구현 패턴:
  - Atlassian Forge Billing: 대규모 usage event를 deterministic pipeline으로 처리하고 charge traceability를 보장
  - Whatnot LLM Platform: velocity/reliability/trust 축으로 운영, post-exposure A/B logging과 calibration으로 drift 조기 감지
  - Slack Agentic App: 장기 컨텍스트 오염을 채널 분리(Director/Critic)로 제어

### 3. DE Insight: Engineering Impact
- 실무 이점 1: **신뢰성(Trust) 향상**  
  Non-deterministic LLM 출력 환경에서도 logging, evaluation, calibration 계층을 붙이면 운영 품질을 계량화할 수 있다.
- 실무 이점 2: **Latency/Cost 최적화**  
  request-level dedup, profile write-time merge, hot/cold tiering은 Throughput을 유지하면서 인프라 비용을 줄이는 직접 수단이다.
- 실무 이점 3: **운영 복구력(Recoverability) 강화**  
  replay-safe 설계, inverse operation, lineage 기반 추적은 장애 시 MTTR 단축과 감사 대응력을 동시에 높인다.

- Modern Data Stack에서의 위치:
  - 이 트렌드는 단순 ETL 고도화가 아니라, **Data Platform + LLMOps + Governance**의 통합 레이어를 요구한다.
  - 즉, "warehouse 중심 분석 스택"에서 "AI 서비스 운영 스택"으로 무게중심이 이동 중이다.

### 4. Critical Thinking: Trade-offs & Limits
- 부작용/비용:
  - 관측성(Observability), 품질 테스트, lineage, semantic layer까지 갖추면 초기 구축 비용과 운영 복잡도가 급격히 증가한다.
  - Agent 자동화는 실험 속도를 올리지만, 잘못된 self-retry 루프가 클라우드 비용 폭증으로 이어질 수 있다.
- 부적합한 환경:
  - 데이터 볼륨이 작고 변경이 드문 팀에는 과도한 거버넌스 계층이 오히려 개발 속도를 늦출 수 있다.
  - 소규모 팀이 전사급 플랫폼 패턴을 그대로 도입하면 운영 인력 부족으로 "관리되는 복잡성"이 아니라 "방치된 복잡성"이 된다.

### 5. Action Plan: Link to My Projects
- Steam API 수집 파이프라인에 적용:
  - 수집 단계에 schema contract + idempotent upsert를 넣어 중복/재수집 이슈를 구조적으로 제거
  - hot/cold 분리 저장(최근 인기 지표는 hot, 원본 이벤트는 cold)로 분석 속도와 비용을 동시에 관리
- 정책지원금 RAG/Agent 프로젝트에 적용:
  - Context 채널 분리(작업 메모리 vs 검증 메모리)와 answer credibility score를 도입해 hallucination을 정량 관리
  - prompt/model 버전별 회귀 테스트 세트를 두어 변경 시 품질 저하를 사전 차단
- 공통 운영 개선:
  - 데이터 자산별 owner, lineage, 품질 지표를 명시한 lightweight governance 문서부터 시작
  - "AI-ready 데이터"와 "analytics-ready 데이터"를 분리 설계해 소비자(사람/모델)별 최적 경로를 운영