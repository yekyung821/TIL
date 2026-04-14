# Today I Learned (TIL)

매일매일 새롭게 배운 지식을 기록하고 꾸준히 성장하기 위한 저장소입니다.

## 📊 논문 요약 한눈에 보기

| 논문 발표일 | 분야 | 논문명 | 핵심 아이디어 | 주요 성과 | 시사점 | 한계점 |
|---|---|---|---|---|---|---|
| 2021.03 | Vision-Language | [CLIP (arXiv:2103.00020)](./AI/CLIP_Natural_Language_Supervision_2103.00020.md) | 웹 스케일의 방대한 '이미지-텍스트 쌍'을 대조 학습하여 범용적 시각 모델 구축 | 자연어 프롬프트를 활용해 파인튜닝 없이도 다양한 태스크에서 Zero-shot SOTA 달성 | 자연어 지도가 강력한 사전학습 신호임을 증명하며 멀티모달의 새로운 패러다임 제시 | 세밀한 추론 및 생성형 태스크에는 취약함 |
| 2022.12 | Speech | [Whisper (arXiv:2212.04356)](./AI/Whisper_Weak_Supervision_2212.04356.md) | 68만 시간의 노이즈 섞인 다국어 약지도 오디오 데이터를 대규모로 학습 | 특정 벤치마크 특화 없이 다양한 OOD 환경에서 견고한 Zero-shot 음성 인식 달성 | 음성 분야에서도 데이터 스케일과 학습 목표 단순화가 혁신적임을 증명 | 무음 구간 환각 및 디코딩 반복 루프의 한계 존재 |
| 2024.10 | Multimodal | [Janus (arXiv:2410.13848)](./AI/Janus_Decoupling_Visual_Encoding_2410.13848.md) | 다중모달 이해와 생성을 위해 단일 인코더 대신 '시각 인코더 분리(Decoupling)' 방식 제안 | 기존 통합 모델의 한계(Trade-off)를 극복하고 단일 LLM으로 전용 모델을 능가 | 이해와 생성의 충돌을 해결하며 유연한 확장성을 지닌 범용 모델의 청사진 제시 | 고해상도 이미지 처리에 한계가 있음 |
| 2025.01 | Multimodal | [Janus-Pro (arXiv:2501.17811)](./AI/Janus_Pro_Unified_Multimodal_2501.17811.md) | 전작 Janus의 디커플링 구조에 7B 파라미터와 대규모 데이터를 결합하여 스케일업 | 이해와 생성 양쪽 모두에서 기존 통합 모델 및 전용 모델들을 압도 (SOTA 달성) | 완벽한 아키텍처와 압도적 체급의 시너지를 증명한 차세대 범용 멀티모달의 해답 | 고해상도 생성 및 3D/영상 모달리티 확장은 여전히 과제 |
| 2025.02 | LLM Reasoning | [Latent Reasoning (arXiv:2502.05171)](./AI/Latent_Reasoning_Test_Time_Compute_2502.05171.md) | 토큰 생성을 늘리는 대신 잠재공간(Latent space)에서 반복(Recurrent) 연산으로 테스트타임 컴퓨팅 확장 | 수학 및 코딩 등 복잡한 과제에서 반복 횟수를 늘릴수록 성능이 유의미하게 향상됨 | 긴 CoT 없이 잠재공간에서 연산만 늘려 추론 능력을 키우는 새로운 스케일링 축 제시 | 아직 PoC 단계로 스케줄링 및 최적화 등 개선 필요 |
| 2026.01 | Agentic AI | [Just-in-Time World Modeling (arXiv:2601.14514)](./AI/Just_In_Time_World_Modeling_2601.14514.md) | 환경의 모든 디테일을 모델링하지 않고, 시뮬레이션 중 필요할 때만 객체를 인코딩하는 JIT 접근법 | 최소한의 연산량으로 인간의 행동 데이터와 유사한 고효율/고적중률 예측 및 계획 수행 달성 | 방대한 컨텍스트 주입보다 "무엇을 무시하고 인코딩할지" 동적으로 결정하는 것이 중요함을 시사 | 요소를 파악하지 못할 위험 존재 |
| 2026.02 | Agentic AI | [Auton Agentic AI Framework (arXiv:2602.23720)](./AI/Auton_Agentic_AI_Framework_2602.23720.md) | 확률적 LLM과 결정론적 인프라의 불일치를 해결하기 위해 인지와 런타임을 분리하는 프레임워크 | 제약 매니폴드 및 계층적 메모리를 통해 런타임 단에서 환각을 통제하고 응답성 향상 | 에이전트의 성공이 안전성과 성능을 보장하는 백엔드 인프라 설계에 달렸음을 시사 | 도입 시 아키텍처 설계 비용 및 복잡도 증가 |
| 2026.03 | Agentic AI | [Training-Free Agentic AI (arXiv:2603.13256)](./AI/Training_Free_Agentic_AI_2603.13256.md) | 추가 학습 없이 톰슨 샘플링과 반성(Reflection)을 통해 멀티 에이전트 간 확률적 작업 위임 최적화 | 토큰 사용량 감소, 에이전트 호출 최소화, 성공까지 걸리는 시간 단축 등 라우팅 효율 극대화 | 똑똑한 오케스트레이션 로직이 비용과 성능을 결정하는 핵심임을 증명 | 개별 에이전트 성능 자체를 근본적으로 돌파하지는 못함 |

## 📝 최근 기록
- 2026-04-14: [Janus-Pro: 데이터 및 모델 스케일링을 통한 통합 다중모달 이해 및 생성 (arXiv:2501.17811)](./AI/Janus_Pro_Unified_Multimodal_2501.17811.md)
- 2026-04-11: [Janus 논문 정리: 통합 멀티모달 이해·생성을 위한 시각 인코딩 분리 (arXiv:2410.13848)](./AI/Janus_Decoupling_Visual_Encoding_2410.13848.md)
- 2026-04-08: [CLIP 원논문 상세 정리: 자연어 지도 기반 전이 학습 (arXiv:2103.00020)](./AI/CLIP_Natural_Language_Supervision_2103.00020.md)
- 2026-04-06: [Whisper 원논문: 대규모 약지도 음성 인식 (arXiv:2212.04356)](./AI/Whisper_Weak_Supervision_2212.04356.md)
- 2026-04-05: [논문 요약: 잠재공간 테스트타임 추론 (Latent Reasoning, arXiv:2502.05171)](./AI/Latent_Reasoning_Test_Time_Compute_2502.05171.md)
- 2026-04-04: [에이전틱 AI 프레임워크 아키텍처: The Auton Agentic AI Framework](./AI/Auton_Agentic_AI_Framework_2602.23720.md)
- 2026-04-02: [에이전틱 AI 멀티에이전트 논문: Training-Free Agentic AI](./AI/Training_Free_Agentic_AI_2603.13256.md)
- 2026-04-01: [에이전틱 AI 논문 리뷰: Just-in-Time World Modeling](./AI/Just_In_Time_World_Modeling_2601.14514.md)
- 2026-03-31: [LangGraph 개념 및 심화 RAG(Agentic RAG) 학습](./AI/Tech_LangGraph_Agentic_RAG.md)
- 2026-03-19: 개발 환경 점검 및 TIL 저장소 리드미 구조 개선
- 2026-03-16: 이력서 작성 전략 수립 및 TIL 저장소 생성
- 2026-03-19: Git 커밋 히스토리 추적 원리 학습 및 올바른 TIL 작성 방향성 고민