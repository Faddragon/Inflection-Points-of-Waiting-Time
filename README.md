# Stage-Specific Inflection Points of Waiting Time and Survival in Oral Cavity Cancer

Code and supporting material for the manuscript **"Stage-Specific Inflection Points of Waiting Time and Survival in Oral Cavity Cancer: A Population-Based Cohort Study"** (submitted to *Frontiers in Oncology*).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Faddragon/Inflection-Points-of-Waiting-Time/blob/main/Supplementary_Notebook_Oral_Cancer_Inflection_Points_Academic.ipynb)

Repository: https://github.com/Faddragon/Inflection-Points-of-Waiting-Time

> **Status:** manuscript under submission. A permanent, citable snapshot of this repository will be archived on Zenodo at acceptance (DOI: `TODO`).

## Overview

Long intervals between diagnosis and treatment are associated with worse outcomes in oral cavity cancer, but there is no consensus on the point at which risk becomes clinically relevant. This study uses a grid search over candidate cutpoints, based on maximally selected log-rank statistics, to find the waiting time that best separates survival curves. It does so for overall survival (OS) and recurrence-free survival (RFS), for the whole cohort and stratified by AJCC 7th edition clinical stage.

Three waiting times are analysed (in days):

| Variable | Definition |
|---|---|
| `t_a` | Histopathological diagnosis → specialist consultation |
| `t_b` | Specialist consultation → treatment initiation |
| `t_c` | Histopathological diagnosis → treatment initiation (`t_c = t_a + t_b`) |

## Data

- **Source:** hospital-based cancer registry of the Fundação Oncocentro de São Paulo (FOSP), https://fosp.saude.sp.gov.br
- **Raw extract:** 25,131 patients treated for oral cavity cancer in the State of São Paulo, Brazil, between January 2000 and July 2025.
- **Analytic cohort:** 10,316 patients after cleaning (see `results/figures/Figure_2.png`, patient selection flow diagram).
- **Cleaning criteria:** age < 18 or > 90 years excluded; staging/TNM inconsistent with AJCC guidelines excluded; errors in date entries excluded. Staging standardized to AJCC 7th edition.
- **Privacy and ethics:** the data are public and de-identified. Ethics committee approval and informed consent were not required under Art. 1, sole paragraph, items II and III, of Resolution No. 510/2016 of the Brazilian National Health Council.

- **Analytic dataset in this repository:** `data/df_oral_so_curativo.xlsx` (read by the notebook).

> `TODO`: state here the date of the FOSP download and the filters used, and add a checksum (e.g. SHA-256) of the file. Confirm that the FOSP terms of use allow redistribution of the dataset, and describe which columns it contains (for example, whether exact dates are included).

## Methods in brief

1. **Grid search:** every candidate cutpoint `t` (in days) splits patients into "≤ t" and "> t". The two groups are compared with the log-rank test, giving `Z²(t)`.
2. **Inflection point:** the value of `t` that maximizes `Z²(t)`.
3. **Minimum group size:** 100 patients per group in the overall analysis; 50 per group in the stage-stratified analyses.
4. **Search window:** 15 to 180 days, in 1-day steps.
5. **Outcomes:** time origin is diagnosis. OS ends at last information, with death as the event. RFS ends at recurrence, or at last information if there is none, with recurrence as the event (see Section 2 of the notebook).
6. **Multivariable Cox regression** for OS and RFS, adjusted for age, sex and clinical stage (Section 5 of the notebook).
7. **Stage-stratified analysis:** the grid search is repeated for stages I–IV, with Benjamini–Hochberg (FDR) correction across the tests (Section 7 of the notebook).
8. **P-values:** the p-value at the optimal cutpoint is the nominal log-rank p-value and is not adjusted for the selection of the maximum over the search grid.

The log-rank formulation is shown in Figure 1 of the manuscript.

## Repository structure

```
.
├── README.md
├── Supplementary_Notebook_Oral_Cancer_Inflection_Points_Academic.ipynb
├── data/
│   └── df_oral_so_curativo.xlsx     # analytic dataset
└── results/
    ├── figures/                     # Figure_1.png ... Figure_6.png
    └── tables/                      # Table_1.xlsx ... Table_3.xlsx
```

## How to reproduce

**Option A: Google Colab.** Click the "Open in Colab" badge at the top and choose *Runtime → Run all*. The notebook downloads the dataset from this repository when it is not found locally.

**Option B: local.**

```bash
git clone https://github.com/Faddragon/Inflection-Points-of-Waiting-Time.git
cd Inflection-Points-of-Waiting-Time
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install lifelines statsmodels openpyxl pandas numpy matplotlib seaborn jupyter
jupyter nbconvert --to notebook --execute Supplementary_Notebook_Oral_Cancer_Inflection_Points_Academic.ipynb
```

**Software:** Python 3.12.1 with `pandas`, `numpy`, `matplotlib`, `seaborn`, `lifelines`, `statsmodels` (Benjamini–Hochberg correction) and `openpyxl` (Excel input and output). The notebook prints the version of each package at the start of the run. The analysis has no stochastic step.

> `TODO`: pin the exact versions in a `requirements.txt` (`pip freeze`).

## Mapping manuscript items to outputs

| Manuscript item | Content | File |
|---|---|---|
| Figure 1 | Log-rank statistic formulation | `results/figures/Figure_1.png` |
| Figure 2 | Patient selection flow diagram | `results/figures/Figure_2.png` |
| Figure 3 | OS and RFS by clinical stage | `results/figures/Figure_3.png` |
| Figure 4 | Grid search curves, overall (`t_a`, `t_b`, `t_c`) | `results/figures/Figure_4.png` |
| Figure 5 | Stage-stratified inflection points, OS | `results/figures/Figure_5.png` |
| Figure 6 | Stage-stratified inflection points, RFS | `results/figures/Figure_6.png` |
| Table 1 | Cohort characteristics (N = 10,316) | `results/tables/Table_1.xlsx` |
| Table 2 | Multivariable Cox regression (OS and RFS) | `results/tables/Table_2.xlsx` |
| Table 3 | Stage-specific inflection points | `results/tables/Table_3.xlsx` |

## Main findings

- **Overall:** OS inflection at 15 days for `t_a` (Z² = 16.36) and 24 days for `t_b` (Z² = 9.25). RFS inflection at 15 days for `t_a` (Z² = 16.22) and 17 days for `t_b` (Z² = 17.11).
- **By stage:** tolerance to delay decreased as stage advanced. For `t_a`, the OS inflection point fell from 73 days in stage I to 20 days in stage IV; for RFS in stage IV it was 15 days.
- **Interpretation:** these are observational associations, not individual safe deadlines. See the limitations below.

## Limitations

- **Immortal time bias:** with diagnosis as the time origin, patients with longer `t_a` must have survived that interval. This probably explains the apparently protective association of `t_a` in the Cox model, and it is not a biological benefit of waiting.
- **Severity bias:** more advanced tumors tend to be prioritized and treated sooner.
- **Staging:** it was recorded at diagnosis, not at the time of treatment.
- **Confounding by indication** cannot be excluded.
- **Data quality:** about 59% of the raw records were excluded during cleaning (25,131 → 10,316).

## How to cite

If you use this code, please cite the manuscript and the archived release:

```
Araújo Dias F, De Cicco R, Pereira de Souza R, Briaunyz Milan T.
Stage-Specific Inflection Points of Waiting Time and Survival in Oral Cavity Cancer:
A Population-Based Cohort Study. Front Oncol. [year]; [volume]: [pages]. doi: TODO
Code: https://doi.org/TODO (Zenodo)
```

## Authors

- Francisco Araújo Dias (corresponding author), franciscoadias@gmail.com
- Rafael De Cicco
- Rafael Pereira de Souza
- Tabata Briaunyz Milan

Head and Neck Department, Instituto do Câncer Dr. Arnaldo Vieira de Carvalho, São Paulo, Brazil.

## Acknowledgments

We thank the Fundação Oncocentro de São Paulo (FOSP) for access to the hospital-based cancer registry data, and the Instituto do Câncer Arnaldo Vieira de Carvalho for institutional support.

## License

- Code: `TODO` (MIT is a common choice for analysis code).
- Figures and tables: `TODO` (e.g. CC BY 4.0; check the journal's copyright policy first).
- Data: subject to the FOSP terms of use.
