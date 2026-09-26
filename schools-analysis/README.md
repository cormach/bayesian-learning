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
- `gcse-school-quality.ipynb` — the GCSE-level model: the six GCSE VA elements (English, Maths, Science, Humanities, Languages, Open) modelled together with a latent **general quality** and a school-level **consistency** (how much a school's elements scatter), each element measured with known error. A regional layer (partially pooled regional means of quality and consistency) is fitted and compared with the baseline; the link to A-level is planned (see `NOTES.md`).
- `gcse-school-tilt.ipynb` — extends the GCSE model with a second school-level factor, a maths-and-science versus English-and-open *tilt*, estimated freely (only Humanities is anchored and the sign fixed). It removes the paired residual structure found in the first notebook and is compared with it on general quality, consistency, $\sigma_s$ and $\rho$.
- `a-level-school-quality.ipynb` — the joint GCSE-to-A-level model: seven pooled A-level subject groups (Maths, Sciences, English, Humanities, Social sciences, Business & Computing, Creative arts) against general GCSE quality, the tilt and regional means, fitted together so uncertainty in the GCSE quantities carries through. Compares a flexible version (group-specific slopes) with a single A-level quality tied to GCSE, and reports how much of each group's school-level variance GCSE explains.
- `a-level-england-shortlist.ipynb` — the England-wide version: all institutions (schools with GCSE results, and colleges, independent schools and other institutions with A-level results only), nine A-level subject groups with Maths, Economics and Psychology separate, regional and local-authority effects. Estimates each institution's true A-level value added per subject with an interval and the probability of being among the best in a chosen area (London by default; set in one cell), and exports the tables to `results/` (CSVs for every institution, plus `london-top50.md`, a readable top 50 per subject for all institutions and for state-funded ones only).
- `a-level-institution-type.ipynb` — adds the register's institution type (independent, academy converter, academy sponsor-led, free school / UTC / studio, LA maintained, college) to the England-wide model, with the seven high-level subject groups. Shows how type relates to GCSE quality, tilt and consistency, to A-level value added beyond GCSE, and to time as an academy, and how much of the converter versus sponsor-led gap runs through the GCSE profile. Association only.
- `a-level-institution-type-london.ipynb` — tests whether the institution-type gaps differ in London, by refitting the type model with a London-specific extra shift for each state type (in general GCSE quality and, per subject group, in A-level value added beyond GCSE). A separate notebook because a second fit does not fit in the first notebook's memory.
- `a-level-school-quality-with-type.ipynb` — repeats `a-level-school-quality.ipynb` with institution type included (same data, seven subject groups, flexible and one-A-level-quality models), with the original results shown alongside, so the effect of adding type on the explained variance, the slopes, the regional effects and the one-quality link can be read directly.
- `a-level-estimates-with-and-without-type.ipynb` — fits the England-wide model twice, without and with the state-type effects, on the seven subject groups, and compares every institution's estimated true value added: scatter of one estimate against the other and of published against estimated (England and London), shifts by type, and how much the top 20 changes. Finds that the estimates barely move.

## Design notes

`NOTES.md` records the reasoning for the next stage: why school-level dispersion across subjects matters, the proposed GCSE-first model, the data facts behind it, regional questions, and the open decisions.

## Data

All files are in `data/`, one row per school (`URN`).

| File | Contents |
| --- | --- |
| `maths-value-add-errors.csv` | Maths only: A-level and GCSE (`P8MEAMAT`) value added with 95% CIs, A-level entries, Progress 8 cohort size, and region (`RGN24NM`). The working dataset for the notebook. |
| `all-value-add-errors.csv` | The same information for every A-level subject. |
| `a-level-gcse-value-add.csv` | Original wide file of A-level VA point estimates by subject, plus KS4 measures. No confidence intervals. |
| `school-names.csv` | Name, type, phase, local authority, town, postcode, sixth-form, gender and admissions policy for every URN in the data, from the public Get Information About Schools register (downloaded 2026-09-20). |

Confidence intervals follow DfE's formula, `1.96 * national SD / sqrt(n)`, so the standard error is recovered as `(upper - lower) / (2 * 1.96)`.

## Running

The repo ships a dev container (`.devcontainer/`). Open a notebook and run all cells; each takes a few minutes because it fits several NUTS models. Paths to the data are relative to this directory.
