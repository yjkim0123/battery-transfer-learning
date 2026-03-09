# project_battery_tl — Progress Report (2026-03-09)

## 논문 컨셉
**"When Does Transfer Learning Help for Battery Life Prediction? An Empirical Study on Cross-Condition Knowledge Transfer"**

## 데이터
- Samsung INR21700-50E, 279셀, 71 aging conditions
- Source: Figshare (Nature Scientific Data, Stroebl et al. 2024)
- 다운로드: ✅ 279/279 완료 (9.6GB)
- 용량 추출: ✅ 213셀 성공 (Calendar 66 + Cycle 147)
- 총 1,532 체크업 포인트

## 실험 결과 요약

### Exp 1: Cross-Temperature Transfer (6쌍)
| Source → Target | Target-only | Source-only | Naive   | Instance-wt | Winner      |
|----------------|-------------|-------------|---------|-------------|-------------|
| 23→35°C        | 0.0027      | 0.0038      | 0.0021  | 0.0025      | 🏆 Naive    |
| 23→45°C        | 0.0019      | 0.0062      | 0.0029  | 0.0028      | 🏆 Target   |
| 35→23°C        | 0.0107      | 0.0110      | 0.0060  | 0.0059      | 🏆 IW       |
| 35→45°C        | 0.0019      | 0.0062      | 0.0030  | 0.0027      | 🏆 Target   |
| 45→23°C        | 0.0107      | 0.0129      | 0.0064  | 0.0062      | 🏆 IW       |
| 45→35°C        | 0.0027      | 0.0055      | 0.0021  | 0.0021      | 🏆 IW       |

**TL wins 4/6, Target-only wins 2/6 (→45°C only)**

### Exp 2: Cross-Stage Transfer
| Source → Target | Target-only | Source-only | Naive  | Winner    |
|----------------|-------------|-------------|--------|-----------|
| Stage 1→2      | 0.0034      | 0.0040      | 0.0026 | 🏆 Naive  |
| Stage 2→1      | 0.0097      | 0.0066      | 0.0046 | 🏆 Naive  |

**TL always wins for cross-stage!**

### Exp 3: LOTO (Leave-One-Temperature-Out)
- →23°C: ✅ TL wins (MAE 0.0096 vs 0.0107)
- →35°C: ❌ Target-only wins (0.0027 vs 0.0040)
- →45°C: ❌ Target-only wins (0.0019 vs 0.0063)

### Exp 4: Domain Adaptation Methods (23→45°C)
1. Target-only (LOO Ridge): MAE=0.0019, R²=0.93
2. Source-only (GBR): MAE=0.0062, R²=0.29
3. Naive combine: MAE=0.0029, R²=0.59
4. Fine-tuning: MAE=0.0036, R²=0.82
5. Instance-weighted: MAE=0.0028, R²=0.53

### Exp 5: Few-Shot Sample Efficiency
- →23°C: TL always wins (up to N=30)
- →35°C: TL always wins (up to N=50)
- →45°C: Target-only catches up at N=5~10
- **핵심**: 23°C가 가장 데이터 부족에 취약 → TL 효과 큼

### Exp 6: Domain Distance (MMD) vs TL Gain
- →23°C: +43~45% gain
- →35°C: +22~24% gain
- →45°C: -41~45% (negative transfer!)

### Exp 7: Feature Importance Shift
- 23°C: norm_cu_1 지배적 (60%)
- 35°C: norm_cu_1 지배적 (69%)
- 45°C: norm_cu_3 지배적 (47%) ← **Feature shift!**

## 킬러 파인딩 🔥
1. **TL is asymmetric**: Works for →23°C/35°C, fails for →45°C
2. **Feature importance shifts across temperatures**: 23/35°C share early-CU dominance, but 45°C relies on later CUs → domain shift
3. **Few-shot crossover**: TL helps most when target data < 10 samples
4. **Cross-stage TL always helps**: Stage knowledge transfers well

## 남은 실험
- [ ] CORAL/TCA domain adaptation
- [ ] Deep learning (LSTM + fine-tuning)
- [ ] Calendar → Cycle transfer
- [ ] DOD/C-rate 기반 transfer
- [ ] Statistical significance tests
- [ ] More cells from incomplete extraction (66 missing)

## 타겟 저널
Journal of Energy Storage (IF 8.9, SCIE Q1, Elsevier hybrid, non-OA 무료)

## 파일 구조
```
~/Documents/project_battery_tl/
├── data/
│   ├── raw/                     # 279 zip files (9.6GB)
│   ├── experiments_meta.csv     # 279-cell metadata
│   ├── capacity_summary_full.csv    # 213-cell capacity summary
│   ├── capacity_trajectories_full.json  # Full trajectories
│   ├── tl_features.csv          # ML feature matrix (139 cells)
│   ├── exp1_cross_temp.csv
│   ├── exp_all_results.csv
│   └── exp5_fewshot.csv
├── figures/
│   ├── fig1_full_trajectories.png
│   ├── fig2_retention_distribution.png
│   ├── fig3_condition_effects.png
│   ├── fig4_mean_by_temp.png
│   ├── fig5_fewshot_curves.png
│   ├── fig6_domain_distance.png
│   └── fig7_feature_importance.png
└── PROGRESS.md
```

---
## 2026-03-09 Late Night Session

### New Experiments
- Exp 5: Few-shot sample efficiency (20 repeats × 8 sample sizes × 6 pairs)
- Exp 6: Domain distance (MMD) vs TL gain
- Exp 7: Feature importance per temperature domain
- Exp 8: Calendar → Cycle transfer (4 pairs, ALL FAIL)
- Full 7-method comparison (Target, Source, Naive, Fine-tune, IW, CORAL, TCA)
- Statistical significance: Wilcoxon signed-rank tests

### Results Update
- 7 methods × 6 temp pairs: TCA wins 2, Target 2, CORAL 1, Naive 1
- Calendar → Cycle: TARGET ALWAYS WINS (fundamental mechanism gap)
- Statistical sig: →23°C (p<0.001), →35°C (p<0.05), →45°C (not sig)
- Feature importance shift: 23/35°C → norm_cu_1 dominant; 45°C → norm_cu_3 dominant

### Paper Draft
- main.tex: 13 pages, 276 lines, 14 references
- references.bib: 14 entries
- Compiles clean with elsarticle template
- Missing: figures, number verification, related work expansion
