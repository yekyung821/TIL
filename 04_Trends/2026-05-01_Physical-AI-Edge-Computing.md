## 📌 Topic: 피지컬 AI(Physical AI) & 엣지 컴퓨팅 - KB금융 휴머노이드 공개가 보여준 전환점
**Tags:** #PhysicalAI #EdgeComputing #Robotics #OnDeviceAI #RealTimeInference

> **Source 1:** [KB Financial unveils humanoid robot 'physical AI care' (DigitalToday, 2026-04-30)](https://www.digitaltoday.co.kr/en/view/52025/kb-financial-unveils-humanoid-robot-physical-ai-care-expands-senior-care)
>
> **Source 2:** [KB금융, '휴머노이드 로봇' 시니어 케어 서비스 공개 (뉴시스, 2026-05-01)](https://www.newsis.com/view/NISX20260430_0003613955)
>
> **Source 3:** [NVIDIA Jetson Thor Unlocks Real-Time Reasoning (NVIDIA Blog)](https://blogs.nvidia.com/blog/jetson-thor-physical-ai-edge/)
>
> **Source 4:** [Build Next-Gen Physical AI with Edge‑First LLMs (NVIDIA Technical Blog)](https://developer.nvidia.com/blog/build-next-gen-physical-ai-with-edge%E2%80%91first-llms-for-autonomous-vehicles-and-robotics/)
>
> **Date:** 2026-05-01

<!-- 노트: 피지컬 AI는 추론 최적화와 안전/규제 논의가 동시에 따라붙는 영역임 -->

---

### 1. Context: Why now?

- 최근 KB금융이 금융권 최초로 휴머노이드 기반 시니어 돌봄 서비스를 공개하며, AI 도입의 무게중심이 "챗봇 UI"에서 "물리적 실행(embodied action)"으로 이동하고 있다.
- 특히 복약 안내, 스케줄 관리 같은 정보형 상호작용을 넘어, 매니퓰레이터를 활용한 약 전달/재활 보조까지 시연했다는 점은 **AI가 실제 환경에서 행동하는 단계**로 넘어갔다는 신호다.
- 동시에 피지컬 AI는 클라우드 호출 중심 구조로는 지연(latency)과 신뢰성 문제를 피하기 어렵다. 사람-로봇 상호작용은 ms 단위 반응성과 안정성이 핵심이기 때문에, 온디바이스/엣지 추론이 사실상 필수 아키텍처가 된다.

### 2. Core Mechanism: How it works?

피지컬 AI의 실제 동작은 "인지-추론-행동" 루프를 **엣지에서 닫는 것**이 핵심이다.

```
Multi-sensor input (camera / mic / lidar / imu / wearable)
  → Perception (CV, sensor fusion, state estimation)
  → Real-time Reasoning (LLM/VLM/VLA on edge runtime)
  → Planning (trajectory / task decomposition / safety constraints)
  → Actuation (robot hand, mobility, assistive action)
  → Feedback loop (next sensor frame in milliseconds)
```

기술적으로 중요한 포인트:

- **컴퓨터 비전 + 센서 융합이 선행조건**
  - 로봇은 텍스트만 처리하는 것이 아니라, 시공간(spatio-temporal) 정보를 지속적으로 해석해야 한다.
  - 결국 CV 품질(객체 인식, 자세 추정, 상황 분할)과 센서 융합 안정성이 추론 성능의 상한을 결정한다.
- **엣지 추론 런타임 최적화가 병목 해소**
  - 엣지 장비에서는 전력/메모리/지연 예산이 제한되어 있으므로, C++ 기반 런타임, 경량화, 양자화, MoE routing 같은 최적화가 필요하다.
  - "모델 크기"보다 "실시간 응답 가능한 추론 경로"가 더 중요해진다.
- **클라우드-엣지 하이브리드 구조가 기본값**
  - 학습/대규모 재학습은 클라우드, 즉시 판단과 제어는 엣지에서 수행하는 분리가 현실적이다.

### 3. DE Insight: Engineering Impact

데이터/ML 엔지니어링 관점에서의 실무적 영향:

- **MLOps가 RoboOps로 확장**
  - 배포 단위가 API 서버에서 디바이스 플릿(robot fleet)으로 이동한다.
  - 모델 버전뿐 아니라 펌웨어, 센서 캘리브레이션, 안전 정책 버전까지 함께 관리해야 한다.
- **데이터 파이프라인의 핵심이 이벤트 로그에서 시공간 로그로 전환**
  - 이미지/비디오/멀티센서 시퀀스가 기본 데이터가 되며, labeling/재현/리플레이 인프라가 중요해진다.
- **Latency SLO가 기능 품질보다 우선하는 구간 등장**
  - "정확한 답"보다 "안전한 즉시 반응"이 더 중요한 업무가 늘어난다.
  - 실시간 추론 최적화(quantization, pruning, runtime kernel tuning)는 선택이 아니라 제품 요구사항이다.

### 4. Critical Thinking: Trade-offs & Limits

- **도입 효과**
  - 고령화/돌봄 현장처럼 인력 공백이 큰 영역에서, 반복 업무 자동화와 서비스 접근성 개선 효과가 크다.
  - 사용자 접점에서 "대화형 AI + 물리 보조"가 결합되면 체감 가치가 급격히 증가한다.
- **현실적 제약**
  - 안전성 검증(오작동, 충돌 회피, 취약계층 대응), 규제, 책임소재 체계가 기술 속도를 따라가기 어렵다.
  - 엣지 하드웨어 제약으로 인해 모델 성능과 응답속도 사이의 trade-off가 상시 발생한다.
- **부적합한 접근**
  - 클라우드 API 중심 프로토타입을 그대로 실환경에 배포하는 방식.
  - CV/센서 데이터 품질 관리 없이 LLM 성능만 올리려는 방식.

### 5. Action Plan: Link to My Projects

- **오늘 한 일**
  - KB금융의 휴머노이드 공개 사례를 기준으로 피지컬 AI가 어떤 실서비스 문제를 푸는지 정리했다.
  - 엣지 추론 최적화 이슈를 NVIDIA의 최신 Physical AI/Edge-LLM 사례와 연결해 기술적 맥락을 보강했다.
- **내 프로젝트 적용 항목**
  - 컴퓨터 비전 프로젝트에서 모델 정확도 KPI 외에 `end-to-end latency`, `on-device memory`, `fallback rate`를 필수 지표로 추가.
  - 추론 파이프라인을 클라우드 추론 단일 구조에서 엣지 우선 + 클라우드 보조(hybrid) 구조로 재설계.
  - 임베디드 배포를 전제로 양자화/지연 프로파일링 자동화 스크립트(모델별 FPS, P95 latency) 템플릿 구축.
- **한 줄 회고**
  - 피지컬 AI 시대에는 "모델이 똑똑한가"보다 **"현장에서 안전하고 즉시 동작하는가"**가 경쟁력의 기준이 된다.
