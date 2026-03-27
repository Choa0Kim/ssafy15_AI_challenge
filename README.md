# 15th AI Challenge: VQA Optimization Project (Qwen2.5-VL)

이 프로젝트는 **Qwen2.5-VL-3B-Instruct** 모델을 활용하여 시각적 질의응답(VQA) 성능을 극대화하기 위한 단계별 최적화 과정을 담고 있습니다.

## 🚀 프로젝트 목표
- **Baseline Score**: 0.75720
- **Target Score**: **0.90 이상** 달성
- **Hardware**: RTX 4050 (6GB VRAM) 로컬 환경 최적화

## 📊 성능 평가 결과 (Performance)

| 단계 | 노트북 파일명 | 전략 요약 | 점수 (Public LB) |
| :--- | :--- | :--- | :--- |
| **Phase 1** | `baseline_desktop5060ti.ipynb` | 기초 LoRA + 4-bit 양자화 파이프라인 | 0.75720 |
| **Phase 2** | `test_desktop5060ti_v2.ipynb` | 로컬 환경 검증 및 설정 변경 | 0.74382 |
| **Phase 3** | `high_perf_colab_t4_v3.ipynb` | 초기 Label Masking (정답 부분 Loss 집중) | **0.82973** |
| **Phase 4** | `test_vlm_v4.ipynb` | **Precise Masking + Logit-based Inference** | **0.90+ (Target)** |

## 📈 프로젝트 발전 과정 (Evolution)

### 1. Baseline Establishment (Phase 1 & 2)
- 로컬 GPU 환경에서 대형 멀티모달 모델(VLM)을 학습하기 위한 기초 환경을 구축했습니다.
- PEFT(LoRA)와 4-bit 양자화를 적용하여 8GB 이하 VRAM에서도 학습이 가능함을 확인했습니다.

### 2. Performance Boost (Phase 3)
- 단순히 전체 문장을 학습하는 대신, 질문 뒤에 붙는 **'정답(a, b, c, d)' 부분에 대해서만 Loss를 계산하는 Label Masking**을 도입했습니다.
- 이 변화를 통해 점수가 **0.75에서 0.82**로 크게 상승하며 전략의 유효성을 증명했습니다.

### 3. Final Optimization (Phase 4 - Current)
- **과적합 해결**: 학습 Loss가 0.00에 수렴함에도 점수가 정체되는 문제를 해결하기 위해 Dropout(0.1) 및 Weight Decay(0.05)를 강화했습니다.
- **추론 정밀도 향상**: `model.generate` 대신 **Logits(확률값) 직접 비교** 방식을 도입하여 텍스트 파싱 오류를 제거하고 0.9 이상의 점수를 목표로 합니다.

## 🛠 핵심 최적화 기법 (Current Version)
1. **Precise Label Masking**: 토크나이저를 이용해 `assistant` 응답 시작 지점을 정확히 찾아 이전 토큰을 완벽 마스킹.
2. **Logit-based Inference**: 'a', 'b', 'c', 'd' 토큰 확률을 직접 비교하여 가장 높은 확률의 옵션 선택.
3. **Memory Optimization**: Gradient Checkpointing 및 Accumulation(16)을 통해 6GB VRAM 최적화.

## 📁 파일 구성
- `test_vlm_v4.ipynb`: 최신 최적화 학습 노트북.
- `train.csv` / `test.csv`: 데이터셋.
- `README.md`: 프로젝트 리포트.

---
*본 프로젝트는 SSAFY 15기 AI 챌린지의 일환으로 진행되었습니다.*
