# South Australia Education Attendance: 2016 vs 2021 Census

An analysis of how many people in South Australia attended educational institutions in 2016 and 2021, broken down by institution type, sex, and postcode. The project covers exploratory analysis, a 2016 vs 2021 comparison with confidence intervals and hypothesis tests, and a discussion of what changed.

**Stack:** Python · pandas · SciPy · matplotlib
**Data:** Australian Bureau of Statistics (ABS) Census of Population and Housing, 2016 and 2021, South Australia by postcode (POA).

---

## Key findings

- **Attendance grew 2.8%**, from 483,361 people in 2016 to 496,860 in 2021.
- **Tertiary attendance grew the most as a share of the total**, from 22.7% to 24.3% (+1.6 percentage points, 95% CI 1.4 to 1.8). University and higher education drove most of this.
- **Preschool share rose** from 4.2% to 5.6% (+1.4 pp, 95% CI 1.3 to 1.5), which is consistent with growing enrolment at the early-childhood end.
- **Secondary share rose** from 19.3% to 20.6% (+1.4 pp, 95% CI 1.2 to 1.5).
- **Female share slipped slightly**, from 51.1% to 50.8% (−0.25 pp, 95% CI −0.45 to −0.05, p = 0.014). This is statistically significant but small.
- **Typical postcode grew.** The mean change per postcode was +40 people (95% CI +20 to +60, paired t-test p < 0.001). The result is robust to a non-parametric check (Wilcoxon p = 0.039), though the distribution is heavily skewed. 178 postcodes grew and 160 shrank.
- **Attendance is concentrated.** The 10 largest postcodes hold 17.3% of all 2021 attendance, and the average postcode is far larger than the median (1,457 vs 417 people).

> **Caveat on data quality.** The share of records with "type of institution not stated" fell sharply, from 21.8% to 16.5%. Part of the apparent growth in every other category is mechanical, because records moved out of "not stated" and into known categories. Changes in shares should be read with this in mind. Absolute counts are less affected.

---

## Analysis

| Question | Method | Notebook/script section |
|---|---|---|
| What does attendance look like in each year? | Totals by level, sex split, postcode distribution (mean, median, IQR, max) | `src/analysis.py`: descriptive section |
| Did the share of each institution type change? | Two-proportion z-tests, with 95% CIs for the difference | `src/analysis.py`: proportion tests |
| Did attendance per postcode change? | Paired t-test and Wilcoxon signed-rank test on the 340 postcodes present in both years | `src/analysis.py`: paired tests |
| Which levels changed, after multiple-testing correction? | Paired t-tests per level, Holm step-down correction | `src/analysis.py`: level changes |

**Why paired tests on postcodes?** The same postcode is observed in both censuses, so differences within a postcode are the natural unit. This removes the variation between postcodes, which is large.

**Why a Wilcoxon test as well?** Postcode totals are strongly right-skewed (a few very large postcodes), so the t-test is sensitive to outliers. The Wilcoxon test is a rank-based check of the same question.

**Postcode mismatch.** The two files cover 341 postcodes each, but they are not identical. POA5960 appears only in 2016 and POA5611 only in 2021, likely due to ABS boundary changes. Paired tests use the 340 common postcodes; the statewide totals use all records.

---

## Figures

| | |
|---|---|
| ![Statewide by level](figures/01_statewide_by_level.png) | ![Postcode distribution](figures/02_postcode_distribution.png) |
| ![Top 10 postcodes](figures/03_top10_postcodes_2021.png) | ![Share change with CI](figures/04_share_change_ci.png) |
| ![Postcode 2016 vs 2021](figures/05_postcode_2016_vs_2021.png) | ![Female share by level](figures/06_female_share_by_level.png) |

---

## Repository structure

```
.
├── data/
│   ├── census_2016_sa.csv        # ABS 2016 postcode-level counts (SA)
│   ├── census_2021_sa.csv        # ABS 2021 postcode-level counts (SA)
│   └── column_descriptions.csv   # Meaning of each column code
├── src/
│   └── analysis.py               # Full pipeline: tables, tests, figures
├── outputs/                      # CSV tables and results.json (generated)
├── figures/                      # PNG charts used above (generated)
├── requirements.txt
└── README.md
```

## Reproduce

```bash
pip install -r requirements.txt
python src/analysis.py
```

The script regenerates everything in `outputs/` and `figures/` from the raw CSVs.

---

## Limitations and next steps

- **Census is a full count, not a sample.** The confidence intervals treat the observed counts as draws from a wider process, which is the usual approach for comparing censuses over time. A reader who prefers a purely descriptive view can treat the intervals as indicating the size of change relative to noise.
- **Postcodes are not fixed areas.** Boundary changes between censuses mean some "change" in a postcode can reflect a changed area. Matching by postcode is a pragmatic choice, not a perfect one.
- **Next steps:** adjust shares excluding "type not stated" as a sensitivity check; join postcodes to SA2/SA3 regions and map the changes; add age-group breakdowns (15–24 vs 25+) to separate school-leaver effects from mature-age study.

## Data source

Australian Bureau of Statistics, Census of Population and Housing, 2016 and 2021 (Educational institution attendance, by postcode, South Australia). Please refer to the ABS website for the official data and terms of use.
