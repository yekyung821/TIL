# Today I Learned (TIL)

매일매일 새롭게 배운 지식을 기록하고 꾸준히 성장하기 위한 저장소입니다.

## 👥 AI 논문 스터디

- 스터디 아카이브(Notion): [AI Article Study](https://www.notion.so/AI-Article-Study-304fa8548e4a80129609e7fd6fa88c5d?source=copy_link)
- 시작일: 2025-10-30
- 진행 방식: 매주 1편의 논문을 읽고, 서로 궁금한 부분/이해가 어려운 부분을 중심으로 질문과 토론 진행
- 학습 토픽: Computer Vision, Deep Learning, ResNet, EfficientNet, NLP, Prompt Engineering, LLM, RAG, Multimodal, Speech Processing
- 스터디 인사이트: 논문을 더 비판적으로 읽고, 혼자 볼 때는 지나치기 쉬운 의문점을 함께 짚으며 깊이 있는 해석 역량을 강화

## 📊 논문 요약 한눈에 보기

> 정렬 규칙: **논문 발표일 기준 내림차순(최신 → 과거)** 으로 작성

| 논문 발표일 | 분야 | 논문명 | 핵심 아이디어 | 주요 성과 | 시사점 | 한계점 |
|---|---|---|---|---|---|---|
| 2026.03 | Agentic AI | [Training-Free Agentic AI (arXiv:2603.13256)](./Training_Free_Agentic_AI_2603.13256.md) | 추가 학습 없이 톰슨 샘플링과 반성(Reflection)을 통해 멀티 에이전트 간 확률적 작업 위임 최적화 | 토큰 사용량 감소, 에이전트 호출 최소화, 성공까지 걸리는 시간 단축 등 라우팅 효율 극대화 | 똑똑한 오케스트레이션 로직이 비용과 성능을 결정하는 핵심임을 증명 | 개별 에이전트 성능 자체를 근본적으로 돌파하지는 못함 |
| 2026.02 | Agentic AI | [Auton Agentic AI Framework (arXiv:2602.23720)](./Auton_Agentic_AI_Framework_2602.23720.md) | 확률적 LLM과 결정론적 인프라의 불일치를 해결하기 위해 인지와 런타임을 분리하는 프레임워크 | 제약 매니폴드 및 계층적 메모리를 통해 런타임 단에서 환각을 통제하고 응답성 향상 | 에이전트의 성공이 안전성과 성능을 보장하는 백엔드 인프라 설계에 달렸음을 시사 | 도입 시 아키텍처 설계 비용 및 복잡도 증가 |
| 2026.01 | Agentic AI | [Just-in-Time World Modeling (arXiv:2601.14514)](./Just_In_Time_World_Modeling_2601.14514.md) | 환경의 모든 디테일을 모델링하지 않고, 시뮬레이션 중 필요할 때만 객체를 인코딩하는 JIT 접근법 | 최소한의 연산량으로 인간의 행동 데이터와 유사한 고효율/고적중률 예측 및 계획 수행 달성 | 방대한 컨텍스트 주입보다 "무엇을 무시하고 인코딩할지" 동적으로 결정하는 것이 중요함을 시사 | 요소를 파악하지 못할 위험 존재 |
| 2025.02 | LLM Reasoning | [Latent Reasoning (arXiv:2502.05171)](./Latent_Reasoning_Test_Time_Compute_2502.05171.md) | 긴 토큰 생성(CoT) 대신, 잠재공간(Latent space)에서 반복(Recurrent) 연산만으로 테스트타임 컴퓨팅 스케일링 | 수학 및 코딩 등 복잡한 과제에서 반복 횟수를 늘릴수록 성능이 유의미하게 향상됨 입증 | 차세대 에이전틱 모델의 연산 효율성을 극대화할 새로운 스케일링 축과 방향성 제시 | 아직 3.5B 규모의 PoC 단계로 최적화 및 스케일업 등 고도화 과제가 남음 |
| 2025.01 | Multimodal | [Janus-Pro (arXiv:2501.17811)](./Janus_Pro_Unified_Multimodal_2501.17811.md) | 전작 Janus의 디커플링 구조에 7B 파라미터와 대규모 데이터를 결합하여 스케일업 | 이해와 생성 양쪽 모두에서 기존 통합 모델 및 전용 모델들을 압도 (SOTA 달성) | 완벽한 아키텍처와 압도적 체급의 시너지를 증명한 차세대 범용 멀티모달의 해답 | 고해상도 생성 및 3D/영상 모달리티 확장은 여전히 과제 |
| 2024.10 | Multimodal | [Janus (arXiv:2410.13848)](./Janus_Decoupling_Visual_Encoding_2410.13848.md) | 다중모달 이해와 생성을 위해 단일 인코더 대신 '시각 인코더 분리(Decoupling)' 방식 제안 | 기존 통합 모델의 한계(Trade-off)를 극복하고 단일 LLM으로 전용 모델을 능가 | 이해와 생성의 충돌을 해결하며 유연한 확장성을 지닌 범용 모델의 청사진 제시 | 고해상도 이미지 처리에 한계가 있음 |
| 2024.07 | Prompt Engineering | [A Survey of Prompt Engineering Methods in LLMs (arXiv:2407.12994)](./Prompt_Engineering_Survey_2407.12994.md) | 최근 프롬프트 기법을 태스크별로 분류/비교해 선택 기준을 체계화 | 44편 문헌, 39개 방법, 29개 NLP 태스크를 구조화해 실무 적용성을 높임 | "최고 기법 1개"가 아니라 조건별 최적 전략이 필요함을 보여줌 | 빠른 분야 변화로 최신 기법 반영 시차가 존재 |
| 2023.10 | RAG | [Self-RAG: Retrieve, Generate, and Critique (arXiv:2310.11511)](./Self_RAG_Learning_to_Retrieve_Generate_and_Critique_2310.11511.md) | reflection token으로 검색 필요성 판단과 자기 비평을 통합 | QA/검증/장문 생성에서 factuality 및 citation 정확도 개선 | 적응형 retrieval이 고정형 RAG 대비 품질-비용 균형에 유리함을 제시 | reflection 신호 품질과 데이터 설계에 민감 |
| 2023.05 | Prompting | [Plan-and-Solve Prompting (arXiv:2305.04091)](./Plan_and_Solve_Prompting_2305.04091.md) | 계획 수립 후 해결하는 2단계 zero-shot CoT 프롬프트 제안 | 10개 데이터셋에서 Zero-shot-CoT 대비 일관된 성능 향상 | 추론 길이보다 추론 구조화가 중요하다는 점을 실증 | 프롬프트 길이 증가에 따른 토큰 비용 부담 |
| 2022.12 | Speech | [Whisper (arXiv:2212.04356)](./Whisper_Weak_Supervision_2212.04356.md) | 68만 시간의 노이즈 섞인 다국어 약지도 오디오 데이터를 대규모로 학습 | 특정 벤치마크 특화 없이 다양한 OOD 환경에서 견고한 Zero-shot 음성 인식 달성 | 음성 분야에서도 데이터 스케일과 학습 목표 단순화가 혁신적임을 증명 | 무음 구간 환각 및 디코딩 반복 루프의 한계 존재 |
| 2022.10 | Agentic Reasoning | [ReAct: Synergizing Reasoning and Acting (arXiv:2210.03629)](./ReAct_Synergizing_Reasoning_and_Acting_2210.03629.md) | Thought-Action-Observation 루프로 추론과 행동을 통합 | QA/결정 태스크에서 환각 완화 및 성공률 향상 | 에이전트형 LLM 워크플로우의 표준 패턴을 제시 | trajectory가 길어져 비용/실패 누적 위험 존재 |
| 2021.03 | Vision-Language | [CLIP (arXiv:2103.00020)](./CLIP_Natural_Language_Supervision_2103.00020.md) | 웹 스케일의 방대한 '이미지-텍스트 쌍'을 대조 학습하여 범용적 시각 모델 구축 | 자연어 프롬프트를 활용해 파인튜닝 없이도 다양한 태스크에서 Zero-shot SOTA 달성 | 자연어 지도가 강력한 사전학습 신호임을 증명하며 멀티모달의 새로운 패러다임 제시 | 세밀한 추론 및 생성형 태스크에는 취약함 |
| 2020.05 | RAG | [Retrieval-Augmented Generation (arXiv:2005.11401)](./Retrieval_Augmented_Generation_2005.11401.md) | 파라메트릭 모델과 외부 검색 메모리를 결합한 생성 프레임워크 | Open-domain QA에서 SOTA를 달성하며 RAG 패러다임 정착 | 지식 최신성/근거성 확보를 위한 검색 결합의 표준을 제시 | retriever 품질이 전체 성능 병목으로 작동 |
| 2020.04 | Information Retrieval | [ColBERT: Late Interaction over BERT (arXiv:2004.12832)](./ColBERT_Late_Interaction_2004.12832.md) | 문서 사전 인코딩 + token-level late interaction으로 정밀 검색 구현 | BERT 기반 정확도와 대규모 검색 효율을 동시에 확보 | RAG 시대 retriever 품질 향상의 핵심 기반 모델로 자리잡음 | 토큰 벡터 저장으로 인덱스 메모리 부담이 큼 |
| 2019.05 | Computer Vision | [EfficientNet: Rethinking Model Scaling for CNNs (arXiv:1905.11946)](./EfficientNet_Rethinking_Model_Scaling_1905.11946.md) | depth/width/resolution을 함께 확장하는 compound scaling으로 CNN 스케일링을 체계화 | ImageNet에서 정확도-효율(Pareto) 균형을 크게 개선하며 당시 SOTA 수준 성능 달성 | "어떻게 크게 만들 것인가"를 공식화해 이후 효율 중심 아키텍처 연구의 기준을 제시 | NAS/대규모 학습 자원 의존도가 높고 실제 latency는 하드웨어 최적화에 민감 |
| 2017.06 | NLP Foundation | [Attention Is All You Need (arXiv:1706.03762)](./Attention_Is_All_You_Need_1706.03762.md) | 순환 구조 없이 Self-Attention만으로 시퀀스 변환을 수행하는 Transformer 제안 | WMT 번역 과제에서 SOTA를 달성하며 학습 병렬성과 성능을 동시에 입증 | 현대 LLM/멀티모달 모델의 표준 아키텍처 출발점이 됨 | Attention의 $O(n^2)$ 복잡도로 긴 컨텍스트 비용이 큼 |
| 2015.12 | Computer Vision | [Deep Residual Learning for Image Recognition (arXiv:1512.03385)](./Deep_Residual_Learning_for_Image_Recognition_1512.03385.md) | 깊은 네트워크의 최적화 난제를 residual learning(`F(x)+x`)으로 해결하는 ResNet 제안 | ImageNet 분류 및 COCO 전이 태스크에서 초심층 네트워크의 성능/안정성을 입증하며 SOTA 달성 | 현대 CV 백본의 표준을 확립하고 다양한 비전 아키텍처의 기본 설계 원칙이 됨 | 깊이 증가에 따른 연산/메모리 비용 부담은 여전히 커 효율화 연구가 필요 |

## 📝 최근 기록
- 2026-04-14: [[Janus-Pro] 데이터 및 모델 스케일링을 통한 통합 다중모달 이해 및 생성 (arXiv:2501.17811)](./Janus_Pro_Unified_Multimodal_2501.17811.md)
- 2026-04-13: [[Janus] 통합 멀티모달 이해·생성을 위한 시각 인코딩 분리 (arXiv:2410.13848)](./Janus_Decoupling_Visual_Encoding_2410.13848.md)
- 2026-04-08: [[CLIP] 자연어 지도 기반 전이 학습 (arXiv:2103.00020)](./CLIP_Natural_Language_Supervision_2103.00020.md)
- 2026-04-06: [[Whisper] 대규모 약지도 음성 인식 (arXiv:2212.04356)](./Whisper_Weak_Supervision_2212.04356.md)
- 2026-04-05: [[Latent Reasoning] 잠재공간 테스트타임 추론 (arXiv:2502.05171)](./Latent_Reasoning_Test_Time_Compute_2502.05171.md)
- 2026-04-04: [[Auton Framework] 에이전틱 AI 프레임워크 아키텍처 (arXiv:2602.23720)](./Auton_Agentic_AI_Framework_2602.23720.md)
- 2026-04-02: [[Training-Free Agentic AI] 멀티 에이전트 시스템의 확률적 제어와 협력 (arXiv:2603.13256)](./Training_Free_Agentic_AI_2603.13256.md)
- 2026-04-01: [[Just-in-Time World Modeling] 효율적인 추론 및 계획 모델링 (arXiv:2601.14514)](./Just_In_Time_World_Modeling_2601.14514.md)
- 2026-02-26: [[Plan-and-Solve Prompting] Zero-shot 추론 구조화 (arXiv:2305.04091)](./Plan_and_Solve_Prompting_2305.04091.md)
- 2026-02-19: [[ColBERT] Late Interaction 기반 고효율 Passage Search (arXiv:2004.12832)](./ColBERT_Late_Interaction_2004.12832.md)
- 2026-02-12: [[ReAct] Reasoning과 Acting의 결합 (arXiv:2210.03629)](./ReAct_Synergizing_Reasoning_and_Acting_2210.03629.md)
- 2026-02-05: [[Self-RAG] Retrieve-Generate-Critique 자기반성형 RAG (arXiv:2310.11511)](./Self_RAG_Learning_to_Retrieve_Generate_and_Critique_2310.11511.md)
- 2026-01-29: [[RAG] Retrieval-Augmented Generation 원전 (arXiv:2005.11401)](./Retrieval_Augmented_Generation_2005.11401.md)
- 2026-01-22: [[Prompt Engineering Survey] LLM 프롬프트 기법 종합 (arXiv:2407.12994)](./Prompt_Engineering_Survey_2407.12994.md)
- 2025-11-27: [[Attention Is All You Need] Transformer 아키텍처의 출발점 (arXiv:1706.03762)](./Attention_Is_All_You_Need_1706.03762.md)
- 2025-11-20: [[EfficientNet] CNN 스케일링의 체계화 (arXiv:1905.11946)](./EfficientNet_Rethinking_Model_Scaling_1905.11946.md)
- 2025-10-30: [[Deep Residual Learning] ResNet으로 깊은 네트워크 최적화 문제 해결 (arXiv:1512.03385)](./Deep_Residual_Learning_for_Image_Recognition_1512.03385.md)