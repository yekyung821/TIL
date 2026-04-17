# [논문 스터디] EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks

> - **작성일**: 2026.04.17
> - **Institution**: Google Research, Brain Team
> - **Year/Venue**: 2019 / ICML
> - **Paper**: [arXiv:1905.11946](https://arxiv.org/pdf/1905.11946)
> - **Code**: [TensorFlow TPU EfficientNet](https://github.com/tensorflow/tpu/tree/master/models/official/efficientnet)
> - **한줄평**: CNN 성능 향상에서 "깊이만/너비만/해상도만" 키우는 비효율을 넘어서, 세 축을 균형 있게 함께 키우는 compound scaling의 기준을 제시한 논문.

---

## 1. 도입 및 배경 (Introduction)
* **논문 요약 및 배경**:
  - 기존 CNN 스케일업은 보통 depth, width, resolution 중 한 축만 키우는 방식이었고, 계산량 대비 성능 효율이 떨어지는 경우가 많았음.
  - 모델이 커질수록 어떤 축을 얼마나 키워야 가장 효율적인지에 대한 체계적 기준이 부족했음.
  - EfficientNet은 **신경망 스케일링을 하나의 최적화 문제**로 보고, 세 축을 동시에 조절하는 compound scaling 방법을 제안함.

* **모델 진화/비교**:

| 비교 항목 | 단일 축 스케일링(기존 관행) | EfficientNet Compound Scaling |
|---|---|---|
| **스케일링 방식** | depth 또는 width 또는 resolution 단독 확장 | depth/width/resolution 동시 확장 |
| **효율성** | 일부 구간에서 성능 포화 혹은 비용 급증 | 동일 FLOPs에서 더 높은 정확도 달성 |
| **설계 원칙** | 경험적/휴리스틱 중심 | 작은 baseline에서 계수 탐색 후 체계적 확장 |
| **실무 장점** | 태스크별 재탐색 필요 | B0~B7 계열로 성능-비용 trade-off 선택 용이 |

* **핵심 개선점/기여도**:

| 핵심 기여 | 설명 |
|---|---|
| **Compound Scaling 공식화** | `depth = α^φ`, `width = β^φ`, `resolution = γ^φ`로 확장, 제약 `α·β^2·γ^2 ≈ 2` 사용 |
| **EfficientNet-B0 설계** | NAS 기반 baseline(B0)을 만든 뒤 동일 규칙으로 B1~B7 확장 |
| **SOTA 정확도-효율성** | ImageNet에서 높은 정확도와 파라미터/연산 효율을 동시에 달성 |

![Figure 1](./images/1905.11946/figure%20(1).png)
*Figure 1 | 단일 축 스케일링 대비 compound scaling 개념도. 세 축을 균형 있게 늘릴 때 효율이 높아짐.*

---

## 2. 아키텍처 및 훈련 전략 (Architecture & Training)
* **아키텍처 구조**:

| 구성 요소 | 역할 및 특징 |
|---|---|
| **Baseline (EfficientNet-B0)** | NAS로 찾은 mobile inverted bottleneck(MBConv) 중심 구조 |
| **MBConv + SE** | 깊이별 분리 합성곱(depthwise conv)과 Squeeze-and-Excitation 결합으로 효율적 표현 학습 |
| **Compound Coefficients** | α(깊이), β(너비), γ(해상도)를 작은 그리드 탐색으로 결정 |
| **Model Family (B0~B7)** | 동일 규칙으로 확장해 다양한 배포 환경(모바일~서버) 대응 |

* **훈련 과정**:

| 단계 | 특징 |
|---|---|
| **1단계: Baseline 탐색** | 제한된 자원 하에서 B0를 먼저 탐색 |
| **2단계: 스케일링 계수 탐색** | 고정 φ=1 기준으로 α, β, γ를 탐색 |
| **3단계: 균형 확장** | 목표 자원에 맞춰 φ를 키워 B1~B7 생성 |
| **정규화/증강** | AutoAugment, Dropout, Stochastic Depth 등으로 일반화 성능 보강 |

> **💡 추가 해설: 왜 width와 resolution은 제곱 항이 붙을까**
> - width가 늘면 채널 수가 증가해 연산량이 대략 제곱 비율로 커지고,
> - resolution이 늘면 spatial 크기(HxW)가 커져 역시 제곱 비율로 연산량이 증가함.
> - 그래서 FLOPs 제약을 맞추려면 `β^2`, `γ^2`를 함께 고려해야 함.

![Figure 2](./images/1905.11946/figure%20(2).png)
*Figure 2 | EfficientNet-B0의 기본 블록 구성 및 stage 구조 예시.*

---

## 3. 실험 및 결과 (Experiments)
* **구현 디테일**:

| 항목 | 디테일 |
|---|---|
| **평가 태스크** | ImageNet 분류, 이후 다양한 전이 학습 벤치마크 |
| **모델군** | EfficientNet-B0 ~ B7 |
| **비교군** | ResNet, DenseNet, GPipe, AmoebaNet 등 당시 강력한 베이스라인 |

* **벤치마크 결과 (SOTA 달성)**:

| 평가 항목 | 결과 요약 |
|---|---|
| **ImageNet 정확도** | 동일/유사 FLOPs 대비 기존 모델보다 높은 Top-1 정확도 달성 |
| **파라미터 효율성** | 더 적은 파라미터로 높은 성능을 달성해 배포 친화성 개선 |
| **전이 성능** | CIFAR-100, Flowers, Cars 등 전이 태스크에서도 강한 성능 확인 |

* **정성적 결과 (Qualitative Results)**:
  - 단순히 "큰 모델"이 아니라 "효율적으로 큰 모델"이 중요하다는 기준을 제시함.
  - 이후 실무에서 모바일/엣지 환경 모델 설계 시 파라미터-FLOPs-정확도 균형을 보는 관점을 정착시킴.

* **절제 연구 (Ablation Studies)**:
  - depth-only, width-only, resolution-only 스케일링과 compound scaling을 비교해 compound 방식의 일관된 우위를 보임.
  - B0 baseline 품질과 스케일링 규칙의 결합이 성능에 핵심임을 확인함.

![Figure 3](./images/1905.11946/figure%20(3).png)
*Figure 3 | 모델별 정확도-파라미터/연산량 비교. EfficientNet 계열이 Pareto 관점에서 우수함.*

---

## 4. 요약 및 시사점 (Conclusion)
* **요약**:
  - EfficientNet은 CNN 스케일링 문제를 체계적으로 공식화해, 정확도와 효율의 균형을 크게 개선한 아키텍처 패밀리임.
  - 이후의 다양한 Efficient 계열(EfficientDet, EfficientNetV2 등) 연구의 직접적인 기반이 됨.

* **한계점 (Limitations)**:
  - NAS + 대규모 학습 자원이 필요해 초기 설계 비용이 높음.
  - 하드웨어/런타임에 따라 실제 latency는 FLOPs와 완전히 일치하지 않아, 배포 시 별도 최적화가 필요함.
  - 극단적으로 고해상도 입력에서는 메모리/입출력 병목이 다시 부각될 수 있음.

* **시사점 및 활용 방안**:
  - 모델 성능 논의 시 단순 Top-1 수치뿐 아니라 파라미터, FLOPs, latency를 함께 봐야 함을 보여줌.
  - 실무에서는 요구사항(모바일 추론 속도, 서버 비용, 정확도 목표)에 맞춰 B0~B7을 선택하는 전략이 유효함.
