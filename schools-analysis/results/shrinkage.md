# Shrinkage: why the estimated value added is less extreme than the published score

The shortlists and charts in this analysis show two numbers for each institution and subject: the **published** value added (the raw DfE score) and the **estimated** true value added (from the model). The estimate is systematically **closer to the average** than the published score, and by far the most in the tails. That pull toward the centre is called *shrinkage*. This note explains what it is, how large it is across England and every subject group, and how to read it.

Source: `a-level-england-shortlist.ipynb` (England-wide model on 2,747 institutions, nine subject groups, one year of results). Figure: `results/tail-shrinkage-england.png`.

## 1. What shrinkage is

A published score is a **noisy measure** of an institution's true value added. It is based on the pupils who happened to take the subject that year, and for a cohort of 7 or 10 pupils one or two unusual results can move the score a long way. Every published score comes with a standard error that says how noisy it is.

The model treats each published score as *true value added plus noise* and asks what value is most plausible given the score, its noise, and what the model knows about the institution (its GCSE profile, its region and local authority, its other subjects). The answer is a compromise: for a large cohort, mostly the published score; for a small cohort, mostly the model's expectation. Extreme published scores are disproportionately the noisy small-cohort ones, so the extremes are pulled in the most.

This is the standard behaviour of partial pooling, and a slope below 1 is what the method **should** give.

## 2. How large it is: the slope of estimated on published

The slope of the estimate on the published score across all institutions. A slope of 1 would mean the estimate follows the published score; below 1 means it is pulled toward the average. "Spread ratio" is the standard deviation of the estimates divided by that of the published scores.

| Subject group | Slope, England | Slope, London | Spread ratio |
| --- | ---: | ---: | ---: |
| Sciences | 0.70 | 0.74 | 0.75 |
| Psychology | 0.68 | 0.72 | 0.72 |
| Creative arts | 0.67 | 0.65 | 0.70 |
| Economics | 0.66 | 0.68 | 0.71 |
| Maths | 0.64 | 0.67 | 0.70 |
| Other social sciences (Sociology, Politics, Law) | 0.60 | 0.62 | 0.67 |
| Humanities | 0.59 | 0.55 | 0.66 |
| Business & Computing | 0.59 | 0.55 | 0.66 |
| English | 0.55 | 0.53 | 0.60 |

Institutions counted per group in England: 1,413 (Economics) to 2,241 (Maths).

- **It is the same in every group.** The pull is not specific to Maths, Economics or Psychology; those sit in the middle of the range.
- **Sciences is pulled least** because it has the largest cohorts (median 43 entries), so its published scores are the least noisy.
- **English, Humanities, Business & Computing and Other social sciences are pulled most**, because the true spread between schools is narrow relative to the noise in each published score (in English and Business & Computing the cohorts are also small).
- **London gives similar slopes** (0.53 to 0.74).

## 3. The tails

Institutions in the bottom and top 5% of published scores, England:

| Subject group | Bottom 5%: published | estimated | Top 5%: published | estimated |
| --- | ---: | ---: | ---: | ---: |
| Maths | -1.28 | -0.83 | +0.86 | +0.48 |
| Economics | -1.04 | -0.66 | +0.80 | +0.51 |
| Psychology | -1.03 | -0.71 | +0.82 | +0.51 |
| Other social sciences | -0.95 | -0.59 | +0.83 | +0.46 |
| Sciences | -1.07 | -0.69 | +0.72 | +0.48 |
| English | -0.86 | -0.44 | +0.75 | +0.38 |
| Humanities | -0.89 | -0.49 | +0.71 | +0.35 |
| Business & Computing | -0.98 | -0.54 | +0.80 | +0.43 |
| Creative arts | -1.33 | -0.84 | +1.01 | +0.64 |

How much larger the correction is in the tails than in the middle:

| Subject group | Typical size of correction, tails | Typical size of correction, middle half | Median entries, tails | Median entries, all |
| --- | ---: | ---: | ---: | ---: |
| Maths | 0.42 | 0.09 | 14 | 26 |
| Economics | 0.34 | 0.08 | 14 | 20 |
| Psychology | 0.32 | 0.07 | 15 | 23 |
| Other social sciences | 0.37 | 0.08 | 14 | 23 |
| Sciences | 0.31 | 0.07 | 24.5 | 43 |
| English | 0.39 | 0.07 | 11 | 18 |
| Humanities | 0.38 | 0.07 | 14 | 30 |
| Business & Computing | 0.41 | 0.09 | 11 | 21 |
| Creative arts | 0.43 | 0.07 | 9 | 14 |

- **The correction is 4 to 6 times larger in the tails** than in the middle half, in every group.
- **The tails are dominated by small cohorts**: a median of 9 to 25 entries, against 14 to 43 overall.
- **In 73% to 84% of institutions the estimate is closer to zero than the published score** (the estimate is more extreme in 16% to 27%, mostly large cohorts whose score is moved slightly by the model's other information).

## 4. Worked examples (Maths, London)

| Institution | Entries | Published (SE) | Estimated [89% interval] |
| --- | ---: | ---: | --- |
| Enfield Grammar School | 10 | 1.35 (0.39) | 0.69 [0.35, 1.06] |
| Blackfen School for Girls | 7 | 1.03 (0.46) | 0.10 [-0.28, 0.46] |
| Cleeve Park School | 7 | 0.36 (0.46) | -0.37 [-0.77, 0.01] |
| Drayton Manor High School | 53 | 0.80 (0.17) | 0.72 [0.48, 0.95] |
| Brampton Manor Academy | 280 | 0.50 (0.07) | 0.47 [0.36, 0.59] |
| St Paul's School | 175 | 0.43 (0.09) | 0.41 [0.29, 0.55] |

The three small cohorts move a long way (Blackfen from 1.03 to 0.10; Cleeve Park from +0.36 to -0.37, so the model expects far less of this school than its published score suggests). The three large cohorts hardly move.

## 5. How to read it

- **Trust the estimate over the published score for small cohorts.** A high published score on 7 to 15 entries is weak evidence; the estimate says how much of it is likely to be real.
- **A large cohort with a high published score is the strongest finding.** Its estimate stays close to the published score and its interval is narrow.
- **Shrinkage is not a penalty on high scorers.** It applies in both directions: a published score of -1.86 on 12 entries becomes -1.05, and -1.60 becomes -1.22.
- **Rankings do change at the top, because small cohorts move toward the middle.** In London, only 6 to 7 of the published top ten in Maths, Economics and Psychology stay in the estimated top ten, and the published top tens have small cohorts (median 10 to 25 entries) against 14 to 39 for the estimated ones. The gaps between institutions are also compressed.

## 6. Caveats

- **How far the pull goes depends on the model's noise assumptions.** For the subject groups that pool several subjects (Sciences, Humanities, Other social sciences, Business & Computing, Creative arts), the pooled standard errors assume separate cohorts, which is a lower bound. If anything that understates the noise and so the pull.
- **The estimate is a model output, not a measurement.** It depends on what the model expects of an institution from its other information. Adding institution type as a further piece of that information moves estimates very little (see `a-level-estimates-with-and-without-type.ipynb`: correlation 0.999, largest shifts about 0.1).
- **One year of results.** Small cohorts change a great deal from year to year, which is part of why they are shrunk.
