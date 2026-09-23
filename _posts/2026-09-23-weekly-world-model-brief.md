---
layout: post
title: "9월 넷째 주 월드모델 논문 요약"
date: 2026-09-23 09:30:00 +0900
papers: 8
summary: "WAM이 확실한 주류로 자리 잡은 한 주. 촉각을 월드 상태로 예측하는 DexTacWAM, 느린 계획과 빠른 보정을 나눈 DualWAM, 그리고 생성한 미래를 행동 선택에 어떻게 쓸지 다룬 연구 두 편."
tags: ["WAM", "World Model", "VLA", "Planning", "촉각·힘", "증류"]
---
## 이번 주 흐름

이번 주는 WAM(World Action Model)이 확실한 주류예요. 촉각을 월드 상태로 예측하는 DexTacWAM, 느린 계획과 빠른 보정을 나눈 DualWAM, 인간 영상을 어포던스로 활용하는 AffordanceWAM이 나왔고, '생성한 미래를 실제 행동 선택에 어떻게 쓸까'를 다룬 연구가 두 편 이어졌어요.

- WAM 확장: 촉각(DexTacWAM), 인간 영상·어포던스(AffordanceWAM), 추론 지연 해결(DualWAM)
- 예측 ≠ 결정: WAM 테스트타임 플래닝 분석에서 oracle 79.2%와 실제 선택기의 격차 확인, D-JEPA가 latent 쪽에서 보정
- 월드모델 표현만 VLA로 증류하면 추론 비용 없이 LIBERO 97.9% 달성
- VLA 실용화: 힘 기반 잔차 RL(ForceRFT), 이벤트 트리거 재추론(95% 성공)

## 논문

### [AffordanceWAM: Affordance-Aware Joint World-Action Modeling for Robot Manipulation](https://arxiv.org/abs/2609.22332)
`2609.22332` · cs.RO · WAM · 인간 영상 · Jiadi You, Qize Yu, Yue Chen, Minghong Cai, Zhide Zhong 외
{: .meta}

**생성하는 미래 월드에 어포던스 필드·히트맵을 넣어, 행동 라벨 없는 인간 1인칭 영상으로도 WAM을 학습.**

- **문제:** 로봇 행동 데이터는 비싸고 다양성이 낮고, 인간 영상은 다양하지만 로봇 행동이 없고 embodiment가 다름.
- **방법:** 사전학습 비디오 디퓨전 트랜스포머 위에 World/Action Expert를 Masked Joint Self-Attention으로 결합. 미래 RGB·Scalar Affordance·Affordance Heatmap·연속 행동을 flow matching으로 함께 예측. 인간 영상은 월드 스트림만, 로봇 궤적은 행동까지 지도.
- **결과:** RoboCasa, CALVIN ABC→D, 실세계에서 RGB-only·로봇 데이터 only 대비 일관된 향상. 로봇 데이터 고정 시 어포던스 주석 인간 영상이 늘수록 RoboCasa 성능이 단조 증가.

> **왜 중요한가:** 리타게팅 없이 인간 영상을 WAM에 쓰는 인터페이스로 '어포던스'를 제안. 데이터 스케일링 관점에서 중요.

[arXiv](https://arxiv.org/abs/2609.22332) · [PDF](https://arxiv.org/pdf/2609.22332)

### [Think Like a World Model, Act Like a VLA: Distilling World-Model Representations into Compact Robot Policies](https://arxiv.org/abs/2609.24682)
`2609.24682` · cs.RO · VLA · World Model · 증류 · Trung Dao, Sankalp Yamsani, Jaden Park, Joohyung Kim, Yong Jae Lee
{: .meta}

**월드모델의 미래 생성은 버리고 내부 표현만 VLA에 정렬 손실로 증류해, 추론 비용 증가 없이 성능을 올림.**

- **문제:** VLA는 세계가 어떻게 반응하는지에 대한 목적함수가 없어 데이터 커버리지에 묶이고, 월드모델은 미래 롤아웃에 초 단위가 걸려 제어 루프에 못 씀.
- **방법:** 고정된 월드모델을 학습 프레임에 한 번 돌려 feature를 캐시하고, VLA 학습에 feature-alignment 항 하나만 추가. 학습 중 teacher 로딩 없음, projector는 배포 시 제거.
- **결과:** 0.8B 학생이 LIBERO 97.9%, RoboCasa-GR1 48.2→50.5%. RTX 5090에서 32ms·1.86GB로 베이스라인과 동일 비용. 단일팔·양팔 실로봇에서도 효과, 스케일·백본·teacher를 바꿔도 유지.

> **왜 중요한가:** '월드모델의 지식은 생성이 아니라 표현에 있다'는 관점. 월드모델을 배포하지 않고도 그 prior를 쓰는 가장 저렴한 방법 중 하나.

[arXiv](https://arxiv.org/abs/2609.24682) · [PDF](https://arxiv.org/pdf/2609.24682)

### [Beyond Visual Quality: A Study of Test-Time Planning with World Action Models](https://arxiv.org/abs/2609.24745)
`2609.24745` · cs.RO · WAM · Planning · 분석 · Jianhao Yuan, Yu Yuan, Benjamin Ramtoula, Lukas Vierling, Paul Newman 외
{: .meta}

**WAM이 상상한 미래로 여러 행동 후보 중 하나를 고르는 테스트타임 플래닝이 실제로 얼마나 통하는지 실증 분석.**

- **문제:** WAM은 행동과 그 결과 영상을 함께 생성하지만, 상상한 미래를 행동 선택에 어떻게 써야 하는지는 불분명함.
- **방법:** 같은 상태에서 후보를 여러 개 샘플링해 실제 결과 기준 oracle 상한을 추정하고, 시각 품질·물리 일관성·과제 진척도 기반 선택기를 통제 실험으로 비교. 반사실 분기로 차이가 예측에 드러나는지 분석.
- **결과:** oracle 선택 시 성공률 68.9%(무작위)→79.2%. 하지만 테스트한 선택기들은 이득이 들쭉날쭉하고 기회의 대부분을 회수하지 못함. 선택 기회는 소수의 결정 시점에 몰려 있음.

> **왜 중요한가:** '영상이 예쁘면 좋은 월드모델'이라는 가정을 반박. WAM 평가를 시각 품질이 아니라 의사결정 유용성으로 해야 한다는 근거.

[arXiv](https://arxiv.org/abs/2609.24745) · [PDF](https://arxiv.org/pdf/2609.24745)

### [DualWAM: Dual-System World Action Models for Asynchronous Global Planning and Local Refinement](https://arxiv.org/abs/2609.24868)
`2609.24868` · cs.RO · WAM · 실시간·효율 · Yixin Zheng, Jiangran Lyu, Yuntian Deng, Kai Liu, Yizhou Zhou 외
{: .meta}

**WAM을 느린 전역 계획과 빠른 손목 카메라 기반 국소 보정으로 나눠, 같은 디노이징 궤적 위에서 비동기로 돌림.**

- **문제:** 미래 영상 예측은 비싸서 WAM은 긴 action chunk로 추론 비용을 나누는데, 그만큼 폐루프 반응성이 떨어짐.
- **방법:** System 2가 주기적으로 넓은 구간의 world-action을 고노이즈 단계까지 디노이징해 전역 계획을 세우고, 손목 전용 System 1이 그 중간 상태에서 짧은 구간을 뽑아 최신 손목 관측으로 저노이즈 보정을 마무리.
- **결과:** Franka·Galbot 제로샷 조작에서 최강 베이스라인 대비 평균 +4.5%p, critical path 16.6배 가속. 역할이 맞는 egocentric·UMI 데이터로 +14%p. 엣지-클라우드 분산 배포에 유리.

> **왜 중요한가:** WAM의 가장 큰 실용 병목인 추론 지연을 구조적으로 푸는 설계. 디노이징 단계 공유라는 아이디어가 다른 WAM에도 이식 가능.

[arXiv](https://arxiv.org/abs/2609.24868) · [PDF](https://arxiv.org/pdf/2609.24868)

### [DexTacWAM: A Visuo-Tactile World-Action Model for Dexterous Manipulation](https://arxiv.org/abs/2609.24976)
`2609.24976` · cs.RO · WAM · 촉각·힘 · Dexterous · Haoran Yuan, Zekai Wang, Boning Shao, Haoran Lu, Trevor Darrell 외
{: .meta}

**비전 위주였던 WAM에 손끝 촉각을 월드 상태로 함께 예측하게 해 접촉이 많은 양손 조작 성능을 크게 올림.**

- **문제:** 정교한 조작은 비전만으로는 부분적으로만 보이는 접촉 동역학에 좌우되는데, 기존 WAM은 비전 중심이라 이를 직접 모델링하지 못함.
- **방법:** 손끝별 촉각 인코딩 → 손가락·포즈 인식 촉각 압축기로 집계 → 비디오 디퓨전 월드모델에 촉각 latent를 주입해 시각·촉각을 함께 예측. 사전학습 vision VAE는 고정.
- **결과:** 22-DoF 양손 플랫폼 6개 과제 모두 1위(평균 70.6 vs 최강 베이스라인 38.0). 촉각 월드모델링 제거 시 74.7→26.6. 과제당 약 100개 시연으로 확장, 시각 예측 품질은 0.5dB 이내 유지.

> **왜 중요한가:** 촉각을 '조건'이 아니라 '예측할 월드 상태'로 넣어야 효과가 난다는 걸 ablation으로 보여줌. 사전학습 비디오 prior를 적은 데이터로 새 감각에 확장하는 레시피.

[arXiv](https://arxiv.org/abs/2609.24976) · [PDF](https://arxiv.org/pdf/2609.24976)

### [D-JEPA: A Decision-Aligned Latent World Model](https://arxiv.org/abs/2609.24749)
`2609.24749` · cs.RO · World Model · Planning · JEPA · Shuaijun Liu, Chengyu Wu, Qifu Wen, Feiyang You, Chenglong Zhang 외
{: .meta}

**latent 월드모델에서 '목표에 가깝게 예측된 후보'가 실제로는 더 나쁠 수 있는 문제를, 실행 결과로 결정 관계를 학습해 보정.**

- **문제:** 예측이 정확해도 latent 거리가 어떤 후보가 성공할지를 반영하지 않음(결정 국소 예측 격차).
- **방법:** 목표 상대 예측 특징과 순서(ordinal) 증거를 함께 추론하는 permutation-equivariant 연산자로 사전학습 예측 기하를 결정이 중요한 지점에서 보정. JEPA 호환 표현이라 기존 latent-distance 플래닝으로 그대로 배포.
- **결과:** PushT 87.89%, RoboTwin 평균 +15.04점, 실로봇 과제 +17점. 자율주행에도 적용.

> **왜 중요한가:** 위 WAM 테스트타임 플래닝 분석 논문과 같은 문제(예측 vs 결정의 괴리)를 latent 월드모델 쪽에서 해법으로 제시.

[arXiv](https://arxiv.org/abs/2609.24749) · [PDF](https://arxiv.org/pdf/2609.24749)

### [React When You Need To: Event-Triggered Asynchronous Inference for VLA Policies](https://arxiv.org/abs/2609.22587)
`2609.22587` · cs.RO · VLA · 실시간·효율 · Yansong Wu, Huaqing Li, Tianding Hou, Lingyun Chen, Alois Knoll
{: .meta}

**장면 변화량에 따라 VLA 재추론 간격을 동적으로 조절하는 이벤트 트리거 비동기 추론.**

- **문제:** action chunk 기반 VLA는 실행 중 환경 변화에 반응이 늦고, 기존 비동기 추론은 고정 간격을 씀.
- **방법:** 직전 추론 이후 관측된 장면 변화로 추론 간격을 적응적으로 결정해 동작 일관성과 반응성을 동시에 확보.
- **결과:** 정적·동적 실세계 환경에서 평균 95% 성공, 최강 베이스라인 대비 +55%p.

> **왜 중요한가:** DualWAM과 같은 '청크 vs 반응성' 문제를 VLA 쪽에서 가볍게 푸는 접근. 비교해서 보면 좋음.

[arXiv](https://arxiv.org/abs/2609.22587) · [PDF](https://arxiv.org/pdf/2609.22587)

### [ForceRFT: Refining VLA Actions through Force-Guided Residual Reinforcement Learning](https://arxiv.org/abs/2609.22840)
`2609.22840` · cs.RO · VLA · 촉각·힘 · RL · Yichen Wang, Chaoyang Zhang, Xuqi Su, Jun Ma, Haiyue Zhu, Xiaocong Li
{: .meta}

**고정된 SmolVLA 위에 손목 힘/토크 기반 잔차 RL 정책을 얹어, 접촉 과제에서 시연 이상으로 회복 동작을 학습.**

- **문제:** 시연만으로 학습한 힘 조건 VLA는 회복 동작이 시연 커버리지에 묶이고 배포 결과에서 배우지 못함.
- **방법:** SmolVLA 기반 prior가 action chunk를 내고, 경량 잔차 actor가 실행 중 손목 wrench로 EE 포즈를 보정. 사람 보정은 actor 지도, 자율 구간 전이는 twin critic 학습에 사용(개입 경계 넘어 TD 부트스트랩 차단).
- **결과:** 플러그 삽입, 링-페그 조립, 화이트보드 닦기 실로봇에서 시연 학습·잔차 모방 베이스라인보다 높은 자율 성공률.

> **왜 중요한가:** 월드모델은 아니지만, 대형 VLA를 고정한 채 접촉 피드백으로 현장 미세조정하는 실용 패턴.

[arXiv](https://arxiv.org/abs/2609.22840) · [PDF](https://arxiv.org/pdf/2609.22840)

---

*arXiv 신규 논문 중 로봇 매니퓰레이션 월드모델·WAM·VLA 관련 논문을 골라, 초록을 바탕으로 요약했습니다. 세부 내용은 원문을 확인해 주세요.*
