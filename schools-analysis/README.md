# Schools analysis

A Bayesian analysis (PyMC) of how much value a secondary school adds at GCSE (KS4) relates to how much it adds at A-level (KS5).

## Notebooks

- `maths-a-level-gcse.ipynb` — the Mathematics analysis:
  - an errors-in-variables regression of A-level Maths VA on GCSE Maths VA, using each school's published confidence interval as known measurement noise
  - extensions for selection into A-level Maths (entries relative to cohort size) and a hierarchical model with partial pooling by region
  - goodness-of-fit checks and a test of whether the relationship flattens for the strongest GCSE schools
- `english-multilevel.ipynb` — the same model for GCSE English (`P8MEAENG`) and A-level English Literature, as a multilevel model with region-varying intercept and slope.
- `english-language-literature-multilevel.ipynb` — a multilevel model of A-level English Language and English Literature together against GCSE English, with subject-specific slopes and correlated school-level effects across the two subjects.
- `science-multilevel.ipynb` — A-level Physics, Chemistry and Biology together against GCSE science (`SCIVAMEA_PTQ_EE`), with subject-specific slopes, an LKJ correlation matrix of school effects, a one-factor shared-school-effect model, and a regional (partial pooling) extension.
- `language-multilevel.ipynb` — A-level Spanish, French and German together against GCSE languages (`LANVAMEA_PTQ_EE`), with the same correlated-residuals model and a regional extension; the data are thin, so the shared-school-effect model is not fitted.
- `humanities-multilevel.ipynb` — A-level History, Geography and Philosophy (`Logic/ Philosophy` in the data) together against GCSE humanities (`HUMVAMEA_PTQ_EE`), with the correlated-residuals model and a regional extension. A one-factor shared-school-effect model is also fitted but does not sample cleanly (most schools have only two of the three subjects), so the notebook records it as not reliable.
- `gcse-school-quality.ipynb` — the GCSE-level model: the six GCSE VA elements (English, Maths, Science, Humanities, Languages, Open) modelled together with a latent **general quality** and a school-level **consistency** (how much a school's elements scatter), each element measured with known error. Baseline run only so far; the regional layer and the link to A-level are planned (see `NOTES.md`).

## Design notes

`NOTES.md` records the reasoning for the next stage: why school-level dispersion across subjects matters, the proposed GCSE-first model, the data facts behind it, regional questions, and the open decisions.

## Data

All files are in `data/`, one row per school (`URN`).

| File | Contents |
| --- | --- |
| `maths-value-add-errors.csv` | Maths only: A-level and GCSE (`P8MEAMAT`) value added with 95% CIs, A-level entries, Progress 8 cohort size, and region (`RGN24NM`). The working dataset for the notebook. |
| `all-value-add-errors.csv` | The same information for every A-level subject. |
| `a-level-gcse-value-add.csv` | Original wide file of A-level VA point estimates by subject, plus KS4 measures. No confidence intervals. |

Confidence intervals follow DfE's formula, `1.96 * national SD / sqrt(n)`, so the standard error is recovered as `(upper - lower) / (2 * 1.96)`.

## Running

The repo ships a dev container (`.devcontainer/`). Open a notebook and run all cells; each takes a few minutes because it fits several NUTS models. Paths to the data are relative to this directory.
