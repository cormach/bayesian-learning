# Independent versus state schools at A-level: what the data do and do not show

This note is about one question: *what do you get at A-level from an independent (fee-paying) school compared with a state school?* The short answer is that the data show a **difference in school averages**, not the **benefit to a pupil** of moving. This note sets out the difference, how large it is, and why it cannot be read as a benefit.

Source: DfE published A-level value added by school and subject (one year), with institution types from the public Get Information About Schools register. Analysis in `a-level-institution-type.ipynb` (model-based gaps) and a direct comparison of published scores (below). Nothing here is causal.

## 1. What value added is

Value added compares a school's A-level results with those of pupils nationally who had similar GCSE results. Zero is average. On the DfE scale I understand one point to be about one grade per subject entry, so a score of +0.2 is roughly a fifth of a grade higher per A-level. **This scale has not been verified against the DfE technical guidance; check it before quoting grade equivalents.**

The subject groups are the ones used throughout the analysis: Maths, Sciences (Biology, Chemistry, Physics), English, Humanities, Social sciences (including Psychology, Sociology, Economics, Politics and Law), Business & Computing, and Creative arts.

## 2. The difference in published results

Schools with at least 15 entries in the subject, so that sampling noise is smaller. "Independent" is fee-paying independent schools with published A-level value added; "state" is every other institution (state schools, academies, free schools, colleges).

| Group | Independent (mean) | State (mean) | Difference | Independents above the state median | State schools above the independent median |
| --- | ---: | ---: | ---: | ---: | ---: |
| Maths | +0.11 | -0.05 | **+0.17** | 69% | 36% |
| Sciences | +0.13 | -0.08 | **+0.21** | 76% | 27% |
| English | +0.18 | -0.00 | **+0.18** | 72% | 24% |
| Humanities | +0.26 | -0.03 | **+0.29** | 86% | 14% |
| Social sciences | +0.18 | -0.06 | **+0.24** | 76% | 21% |
| Business & Computing | +0.30 | -0.02 | **+0.32** | 82% | 15% |
| Creative arts | +0.37 | -0.07 | **+0.45** | 90% | 15% |

The number of institutions behind each row is 106 to 318 independent and 599 to 1,728 state.

**The sectors overlap a great deal.** The school-to-school spread within each sector (standard deviation about 0.25 to 0.45) is as large as or larger than the gap between them. In Maths, 54% of state schools do better than the bottom quarter of independent schools; in Sciences and English about half do. The gap is widest in Creative arts, Business & Computing and Humanities.

## 3. The same gap after allowing for region, local authority and GCSE profile

In the England-wide model (`a-level-institution-type.ipynb`), independent schools sit above every state type in every subject group, with all intervals clear of zero. Value-added points; independent minus each state type:

| Group | vs academy converter | vs academy sponsor-led | vs free school / UTC / studio | vs LA maintained |
| --- | ---: | ---: | ---: | ---: |
| Maths | +0.12 | +0.22 | +0.35 | +0.12 |
| Sciences | +0.15 | +0.27 | +0.35 | +0.21 |
| English | +0.18 | +0.24 | +0.14 | +0.21 |
| Humanities | +0.26 | +0.33 | +0.27 | +0.27 |
| Social sciences | +0.19 | +0.23 | +0.17 | +0.19 |
| Business & Computing | +0.21 | +0.35 | +0.28 | +0.22 |
| Creative arts | +0.42 | +0.53 | +0.42 | +0.44 |

These gaps are larger than the gaps between the state types themselves (converter versus sponsor-led is 0.04 to 0.14 points beyond the GCSE profile). Independent schools have no GCSE results in the data, so their gap **cannot be split into a part explained by GCSE profile and a part beyond it**: it contains whatever their GCSE profile would have been.

## 4. Why this is not the benefit of going private

1. **Selection.** Value added adjusts for each pupil's own GCSE results. It does not adjust for family circumstances, motivation, tutoring, or who chooses to enter a subject or stay for sixth form. Pupils at independent schools differ on all of these.
2. **Which schools are counted.** Only independent schools with published A-level value added are included (576 institutions). They are not a random sample of all independent schools.
3. **No same-pupil comparison.** The figures compare schools, not the same pupil in two settings, which is the comparison the question needs.
4. **No fees.** Fees are not in the data, so nothing here says what any difference would cost.
5. **One year of results.** Small cohorts move a lot from year to year.

## 5. How to use the numbers

- **Treat the gap as an average tendency between sectors, not a prediction for a pupil.** A 0.2 difference in school averages does not mean 0.2 for any one child, and the overlap means many state schools outperform many independent ones.
- **Compare specific schools, not sectors.** Within Maths, Sciences and English the choice of school matters about as much as the sector. For example, in London Economics the strongest state school in the analysis (Ark Isaac Newton Academy, estimated value added 0.60) is level with the strongest independent one (Latymer Upper School, 0.60). The London shortlists are in `results/london-top50.md`.
- **Prefer larger cohorts and narrow intervals** (see `results/london-*-top50.png`); a high score on 10 entries is weak evidence.

## 6. What would answer the question properly

- **Pupil-level data** (the National Pupil Database) comparing pupils with the same GCSE results and similar backgrounds who did and did not attend independent schools. Selection would remain difficult to remove even then.
- **Fees**, to express any difference as a cost per year of sixth form. If you give me the fee figure, I can add a cost-against-difference table for a specific shortlist, framed as a school-average difference per year and not a benefit to one pupil.
