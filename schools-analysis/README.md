# Schools analysis

A Bayesian analysis (PyMC) of how much value a secondary school adds at GCSE (KS4) relates to how much it adds at A-level (KS5).

## Notebooks

- `maths-a-level-gcse.ipynb` — the Mathematics analysis:
  - an errors-in-variables regression of A-level Maths VA on GCSE Maths VA, using each school's published confidence interval as known measurement noise
  - extensions for selection into A-level Maths (entries relative to cohort size) and a hierarchical model with partial pooling by region
  - goodness-of-fit checks and a test of whether the relationship flattens for the strongest GCSE schools
- `english-multilevel.ipynb` — the same model for GCSE English (`P8MEAENG`) and A-level English Literature, as a multilevel model with region-varying intercept and slope.

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
