# [논문 스터디] Deep Residual Learning for Image Recognition (ResNet)

> - **작성일**: 2026.01.28
> - **Institution**: Microsoft Research
> - **Year/Venue**: 2015 / CVPR 2016
> - **Paper**: [arXiv:1512.03385](https://arxiv.org/abs/1512.03385)
> - **Code**: [torchvision ResNet](https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py)
> - **한줄평**: 깊어질수록 학습이 어려워지는 문제를 Residual Connection으로 해결해, 현대 비전 백본의 표준을 만든 기념비적인 논문.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - 네트워크를 깊게 만들면 표현력은 늘어나야 하지만, 실제로는 학습이 더 어려워져 오히려 훈련 오차가 증가하는 **Degradation Problem**이 발생함.
  - 이는 단순한 과적합(Overfitting) 문제가 아니라, 최적화 자체가 어려워지는 문제임.
  - 저자들은 층이 직접 원하는 함수 `H(x)`를 학습하는 대신, 입력 대비 잔차 `F(x)=H(x)-x`를 학습하도록 설계해 최적화를 쉽게 만듦.

* **모델 진화/비교**:

| 비교 항목 | Plain Deep Net | ResNet |
|---|---|---|
| **깊이 증가 시 학습 안정성** | 깊어질수록 불안정, 훈련 오차 증가 가능 | 상대적으로 안정적, 깊은 모델에서도 최적화 용이 |
| **블록 구조** | Conv-BN-ReLU 순차 적층 | Conv 블록 + Identity Shortcut(잔차 연결) |
| **핵심 학습 대상** | 직접 함수 `H(x)` | 잔차 함수 `F(x)` |
| **실무 영향** | 깊이 확장 한계 존재 | 다양한 백본(Detection/Segmentation) 표준으로 확장 |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Residual Learning 제안** | `y = F(x) + x` 형태의 Shortcut 연결로 깊은 네트워크 최적화 난이도 완화 |
| **초심층 네트워크 실증** | ImageNet에서 152-layer 모델을 안정적으로 학습, 성능과 효율 동시 입증 |
| **범용 백본화** | 분류를 넘어 COCO Detection/Segmentation에서 큰 성능 향상 확인 |

![Figure 1](./images/1512.03385/figure%20(1).png)
*Figure 1 | (좌) Plain block 대비 (우) Residual block. 입력을 우회하는 identity shortcut이 핵심.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **아키텍처 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Basic Block (18/34층)** | 3x3 Conv 2개 + Shortcut. 채널/해상도 변환 시 projection shortcut 사용 |
| **Bottleneck Block (50/101/152층)** | 1x1(축소) - 3x3 - 1x1(확장) 구조로 계산량 대비 깊이 확장 |
| **Shortcut Connection** | 가능한 경우 identity 사용, 차원 불일치 시 1x1 projection으로 정합 |
| **Global Average Pooling + FC** | 마지막 분류 헤드로 파라미터 효율성 확보 |

* **훈련 과정**:

| 단계 | 특징 |
|---|---|
| **초기 전처리** | 표준 데이터 증강(랜덤 크롭/수평 반전) 기반 학습 |
| **최적화** | SGD + 모멘텀, 학습률 스케줄링으로 수렴 안정화 |
| **깊이 확장 실험** | 18/34/50/101/152층 비교로 깊이-성능 관계 실증 |
| **전이 실험** | ImageNet 사전학습 가중치를 COCO 태스크에 전이 |

> **💡 추가 해설: 왜 Residual이 최적화를 쉽게 하는가**
> - 이상적으로는 깊은 모델이 얕은 모델을 포함(Identity 매핑)할 수 있어야 함.
> - 하지만 plain 네트워크는 이 단순 해조차 찾기 어려워 훈련 오차가 증가함.
> - Residual 연결은 "최소한 identity는 쉽게 전달"하게 만들어, 추가 층이 성능을 해치지 않도록 도와줌.

![Figure 2](./images/1512.03385/figure%20(2).png)
*Figure 2 | Plain-34와 ResNet-34의 학습/검증 오차 비교. 같은 깊이에서도 ResNet이 최적화에 유리함.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | 디테일 |
|---|---|
| **모델군** | ResNet-18/34/50/101/152 |
| **비교 기준** | VGG 계열 대비 깊이/복잡도/정확도 |
| **핵심 설정** | 잔차 블록 반복 스택 + stage별 다운샘플링 |

* **벤치마크 결과 (SOTA 달성)**:

| 평가 항목 | 결과 요약 |
|---|---|
| **ImageNet Classification** | ResNet-152가 ILSVRC 2015 분류 과제에서 최상위 성능 달성 |
| **모델 효율성** | 매우 깊은 구조(152층)임에도 VGG 대비 파라미터/복잡도 효율 우수 |
| **COCO 전이 성능** | Detection/Segmentation에서 기존 대비 큰 폭의 상대 향상 보고 |

* **정성적 결과 (Qualitative Results)**:
  - 깊이가 늘어날수록 항상 좋아져야 한다는 기대를, residual 설계가 실제로 가능하게 만든 사례임.
  - 이후 다양한 비전 태스크에서 "ResNet 계열 백본 + 태스크 헤드" 조합이 표준 파이프라인으로 자리잡음.

* **절제 연구 (Ablation Studies)**:
  - Plain vs Residual 비교에서 동일 깊이에서도 residual이 더 낮은 훈련 오차를 보임.
  - Shortcut 타입(identity vs projection)과 깊이에 따른 성능 변화를 통해 설계 타당성 검증.

![Figure 3](./images/1512.03385/figure%20(3).png)
*Figure 3 | ImageNet에서 네트워크 깊이에 따른 성능 비교. Residual 설계가 깊이 확장의 이점을 실제 성능으로 연결함.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - ResNet은 "깊은 모델이 항상 학습하기 어렵다"는 병목을 residual 학습으로 해결하여, 초심층 네트워크의 시대를 연 핵심 아키텍처임.
  - 분류뿐 아니라 탐지/분할 등 다운스트림 태스크의 표준 백본으로 확산되며 컴퓨터비전 전반의 성능 상향을 이끎.

* **한계점 (Limitations)**:
  - 깊이 확장 자체는 해결했지만, 연산량/메모리 비용 문제는 여전히 커서 경량화 설계가 별도로 필요함.
  - 매우 깊은 구조는 추론 지연(latency)과 배포 비용 측면에서 제약이 있어, 이후 EfficientNet류의 효율 중심 연구가 이어짐.

* **시사점 및 활용 방안**:
  - 실무에서는 `ResNet + FPN + Task head` 조합이 오랫동안 강력한 기본선(baseline)으로 작동함.
  - 잔차 연결 개념은 비전뿐 아니라 NLP/음성/시계열 모델의 안정적 학습 설계에도 폭넓게 적용 가능함.
