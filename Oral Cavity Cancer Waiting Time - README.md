# Stage-Specific Inflection Points of Waiting Time and Survival in Oral Cavity Cancer

Code and supporting material for the manuscript **"Stage-Specific Inflection Points of Waiting Time and Survival in Oral Cavity Cancer: A Population-Based Cohort Study"** (submitted to *Frontiers in Oncology*).

> **Status:** manuscript under submission. A permanent, citable snapshot of this repository will be archived on Zenodo at acceptance (DOI: `TODO`).

## Overview

Long intervals between diagnosis and treatment are associated with worse outcomes in oral cavity cancer, but there is no consensus on the point at which risk becomes clinically relevant. This study uses a grid search over candidate cutpoints, based on maximally selected log-rank statistics, to find the waiting time that best separates survival curves. It does so for overall survival (OS) and recurrence-free survival (RFS), for the whole cohort and stratified by AJCC 7th edition clinical stage.

Three waiting times are analysed (in days):

| Variable | Definition |
| --- | --- |
| `t_a` | Histopathological diagnosis → specialist consultation |
| `t_b` | Specialist consultation → treatment initiation |
| `t_c` | Histopathological diagnosis → treatment initiation (`t_c = t_a + t_b`) |

## Data

- **Source:** hospital-based cancer registry of the Fundação Oncocentro de São Paulo (FOSP), [https://fosp.saude.sp.gov.br](https://fosp.saude.sp.gov.br)
- **Raw extract:** 25,131 patients treated for oral cavity cancer in the State of São Paulo, Brazil, between January 2000 and July 2025.
- **Analytic cohort:** 10,316 patients after cleaning (see `results/figures/Figure_2.png`, patient selection flow diagram).
- **Cleaning criteria:** age \< 18 or > 90 years excluded; staging/TNM inconsistent with AJCC guidelines excluded; errors in date entries excluded. Staging standardized to AJCC 7th edition.
- **Privacy and ethics:** the data are public and de-identified. Ethics committee approval and informed consent were not required under Art. 1, sole paragraph, items II and III, of Resolution No. 510/2016 of the Brazilian National Health Council.

> `TODO`: state here whether the raw FOSP file is redistributed in `data/raw/` or must be downloaded by the user. If it is not redistributed, add the download date, the exact export or filters used, and a checksum (e.g. SHA-256) of the file used in the analysis.

## Methods in brief

1. **Grid search:** every candidate cutpoint `t` (in days) splits patients into "≤ t" and "> t". Kaplan–Meier curves of the two groups are compared with the log-rank test, giving `Z²(t)`.
2. **Inflection point:** the value of `t` that maximizes `Z²(t)`.
3. **Minimum group size:** 100 patients per group in the overall analysis; 50 per group in the stage-stratified analyses.
4. **Search window:** `TODO` confirm against the code (Figures 5 and 6 report a 15–180 day window).
5. **Multivariable Cox regression** for OS and RFS, with Benjamini–Hochberg (FDR) correction of p-values (Table 2).
6. **Stage-stratified analysis:** the grid search is repeated for stages I–IV, with FDR correction across the tests (Table 3).

The log-rank formulation is shown in Figure 1 of the manuscript.

## Repository structure

> Proposed layout. Adjust the paths to match the final code.

```
.
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── data/
│   ├── raw/                 # FOSP extract, or instructions to obtain it
│   └── processed/           # cleaned analytic dataset (n = 10,316)
├── notebooks/
│   └── analysis.ipynb       # TODO: name of the final notebook
├── src/                     # TODO: optional helper modules
└── results/
    ├── figures/             # Figure_1.png ... Figure_6.png
    └── tables/              # Table_1.xlsx ... Table_3.xlsx
```

## How to reproduce

```
# 1. Clone
git clone https://github.com/<USER>/<REPO>.git
cd <REPO>

# 2. Create an environment (Python 3.12.1)
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. Place the data in data/raw/ (see the Data section)

# 4. Run the analysis
jupyter nbconvert --to notebook --execute notebooks/analysis.ipynb   # TODO: confirm the name
```

**Software** (as reported in the manuscript): Python 3.12.1 with `pandas`, `numpy`, `matplotlib`, `seaborn` and `lifelines`. `TODO`: pin the exact versions in `requirements.txt` (`pip freeze`) and add any other package used (for example `statsmodels` or `scipy` for the FDR correction, `openpyxl` for the Excel tables).

Set the random seed (if any step is stochastic) at the top of the notebook: `TODO`.

## Mapping manuscript items to outputs

| Manuscript item | Content | File |
| --- | --- | --- |
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

- Francisco Araújo Dias (corresponding author), [franciscoadias@gmail.com](mailto:franciscoadias@gmail.com)
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