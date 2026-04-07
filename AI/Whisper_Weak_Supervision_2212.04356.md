# Whisper: Robust Speech Recognition via Large-Scale Weak Supervision (arXiv:2212.04356)

Whisper 원논문을 다시 읽고, 이전 요약에서 생략했던 데이터 파이프라인/학습/평가/한계까지 포함해서 상세 메모로 재정리했다.

- **원문:** [arXiv:2212.04356](https://arxiv.org/abs/2212.04356) / [PDF](https://arxiv.org/pdf/2212.04356)
- **제목:** *Robust Speech Recognition via Large-Scale Weak Supervision*
- **핵심 키워드:** large-scale weak supervision, zero-shot transfer, multilingual ASR, speech translation

---

## 1) 문제의식: 왜 Whisper가 필요했는가

- 기존 ASR는 벤치 내(in-distribution) 성능은 높아도, 실제 환경(OOD)으로 나가면 급격히 성능이 떨어지는 경우가 많았다.
- 비지도/자기지도 사전학습이 발전했지만, 실서비스에서는 여전히 도메인별 파인튜닝 비용이 컸다.
- Whisper는 질문을 바꿨다:  
  **“웹 스케일의 noisy한 라벨 오디오를 그냥 크게 모아 supervised로 학습하면, 파인튜닝 없이도 robust한 zero-shot ASR이 가능한가?”**

---

## 2) 데이터: 680,000시간 약지도 학습 셋 구성

### 데이터 규모/구성
- 총 **680,000시간** 라벨 오디오
- 영어 약 **438,000시간**
- 비영어 약 **117,000시간** (다수 언어)
- 번역(X->en) 약 **125,000시간**

### 파이프라인 철학
- 복잡한 전처리 체인을 최소화하고, seq2seq 모델이 직접 `audio -> text` 매핑을 학습하도록 설계
- 오디오는 **30초 세그먼트** 단위로 분할
- 무음 구간도 포함해 `nospeech` 예측 학습에 사용

### 데이터 정제(중요)
- 기계 자막 스타일(문장부호 결여/전체 대문자/소문자 고정 등) 필터링
- 텍스트 언어 감지 + 오디오 언어 감지 비교로 불일치 샘플 제거
- fuzzy de-duplication으로 중복 제거
- 고오류 소스 수동 점검 및 정렬 불량 샘플 제거
- 평가셋 contamination 방지를 위한 중복 제거 수행

### 스케일링 관찰
- 데이터량(log)과 WER(log) 사이 강한 상관 보고: **r^2 ~= 0.83**
- 경험적으로 **데이터 16배 증가 시 WER 절반 수준 감소** 경향

---

## 3) 입력 표현/모델 아키텍처

### 오디오 전처리
- 16kHz 리샘플링
- 25ms window / 10ms hop
- **80-channel log-Mel spectrogram** 사용 (후속 버전 일부는 128 채널)

### 모델 구조
- 표준 **Encoder-Decoder Transformer**
- 인코더 앞단에 CNN stem으로 시간축 다운샘플링
- 디코더는 autoregressive 생성 + encoder 출력에 cross-attention
- BPE 토크나이저 사용, 다국어 분절(fragmentation) 완화를 위한 vocabulary 구성 최적화

### 모델 크기 계열(논문/공개 라인업 맥락)
- Tiny, Base, Small, Medium, Large 계열로 확장
- 큰 모델일수록 다국어/멀티태스크에서 음의 전이보다 양의 전이가 두드러짐

---

## 4) 멀티태스크 포맷: 토큰 기반 제어

Whisper는 전사/번역/언어 식별/타임스탬프를 단일 디코더 토큰 시퀀스로 제어한다.

예시 흐름:
`<|startoftranscript|> -> 언어토큰 -> task토큰(transcribe/translate) -> timestamp 옵션 -> 텍스트 -> <|endoftranscript|>`

추가로:
- 무음 구간은 `<|nospeech|>` 예측
- 타임스탬프는 양자화된 토큰으로 텍스트와 함께 생성
- 일부 확률로 이전 문맥 텍스트를 넣어 장문 전사 안정화

---

## 5) 학습 설정(요약)

- 옵티마이저: AdamW + grad clipping
- warmup 이후 LR decay
- FP16, dynamic loss scaling, activation checkpointing 등 대규모 학습 최적화 기법 사용
- 데이터 다양성이 커서 과도한 추가 regularization/augmentation 의존 없이도 일반화 확보 방향

---

## 6) 실험 결과: 왜 “robust”라고 부르는가

### (A) 영어 ASR + OOD 일반화
- zero-shot Whisper의 LibriSpeech clean 성능 자체도 강하지만,
- 더 중요한 점은 **OOD 벤치에서 기존 supervised 기준 대비 큰 폭의 WER 개선**을 보인다는 것
- 즉, in-distribution SOTA 경쟁보다 **distribution shift 내성**이 Whisper의 핵심 가치

### (B) 잡음 강건성
- SNR이 높은 깨끗한 환경에서는 기존 특화 모델이 강할 수 있음
- 하지만 소음이 커질수록(특히 실제 환경 소음 분포) Whisper의 강건성이 두드러짐

### (C) 다국어 ASR
- 고자원/유사 언어군에서 성능이 빠르게 개선
- 언어별 데이터량과 성능의 상관이 뚜렷함
- 반면 고유 스크립트/저자원/언어적 거리 큰 언어는 성능 편차가 큼

### (D) 음성 번역(X->en)
- zero-shot 세팅에서 의미 있는 BLEU를 확보하며, ST에서도 foundation-style 전이 가능성 제시
- 다만 번역 품질은 언어 식별 오류/데이터 노이즈 영향에 민감

---

## 7) Long-form 전사 안정화 휴리스틱 (실무적으로 중요)

30초 윈도우를 슬라이딩하면서 전사할 때, 논문/구현에서 다음 전략이 중요하다.

- beam search 사용
- temperature fallback 스케줄
- 조건부 이전 텍스트 사용(`condition_on_previous_text`)
- `nospeech` 확률 + 평균 로그확률 임계 결합
- 초기 timestamp 제약

이 조합으로 반복 루프/환각/구간 누락을 완화한다.

---

## 8) 한계점 (논문 메시지 + 실사용 관찰)

- 무음 구간 환각(hallucination)
- 반복 문장 루프
- 저자원/비인도유럽/고유문자 언어 성능 편차
- 화자 정보 환각(오디오만으로 확정 불가한 정보 생성)

즉, “강건해졌다”와 “무결점이다”는 다르다.  
실무에선 디코딩 파라미터/후처리/도메인 파인튜닝을 함께 설계해야 한다.

---

## 9) 내가 정리한 핵심 인사이트

1. Whisper의 임팩트는 “신규 아키텍처”보다 **데이터 스케일과 학습 목표 단순화**에 있다.
2. STT를 벤치 점수 경쟁에서 **zero-shot robust transfer** 관점으로 재정의했다.
3. 다국어+멀티태스크는 작은 모델에선 부담이 되지만, 충분히 큰 모델에선 오히려 전이 이득을 준다.
4. foundation model 관점에서 음성도 텍스트/비전과 비슷한 스케일 법칙을 따른다는 강한 근거를 제공했다.
