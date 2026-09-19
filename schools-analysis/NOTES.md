# Design notes: from subject notebooks to a school-level model

This records the reasoning behind the next stage of the analysis, so it survives outside any one session. It separates what the notebooks have **established** from what is **proposed** and what is **still open**.

## 1. Where the subject notebooks got to

Each notebook models A-level value added (VA) for a group of subjects against the matching GCSE VA element, with the published confidence intervals treated as known measurement error, and a correlated (LKJ) school-level residual across subjects.

| Group | GCSE predictor | Slopes | Cross-subject correlation of school effects | Regional effects |
| --- | --- | --- | --- | --- |
| Sciences | `SCIVAMEA_PTQ_EE` | 0.21-0.23 | 0.59-0.84 (strong) | small (spreads about 0.03) |
| Languages | `LANVAMEA_PTQ_EE` | Spanish 0.36, French 0.16, German 0.32 (thin) | Spanish-French 0.50; German pairs not learned | none detectable |
| Humanities | `HUMVAMEA_PTQ_EE` | History 0.19, Geography 0.17, Philosophy 0.11 (interval includes 0) | History-Geography 0.39; Philosophy pairs wide | larger (spreads about 0.07); London stands out |

Across subjects, GCSE VA explains a real but small share of A-level VA (roughly 5-15% of latent variance; Spanish about 27%).

The one-factor "shared school effect" model worked for the sciences (most schools have all three subjects) but **did not sample cleanly for humanities** (r_hat up to 1.10, ESS below 100). Most humanities schools have only History and Geography, and two scores cannot separate a shared effect from a subject-specific one. This is the main identifiability lesson: a factor model needs many subjects per school.

## 2. The conceptual issue: dispersion across subjects

Some schools lift pupils across every subject; in others the subjects scatter. The correlated-residuals model gives every school the **same** covariance matrix, so a single correlation blends the two kinds of school together. It cannot tell "most schools are moderately consistent" from "some are very consistent and others very scattered".

A **location-scale** structure fixes this: each school gets a general effect and its own dispersion across subjects. This becomes estimable only with many subjects per school, which is what an all-subjects model provides.

Cautions that apply to any such model:

- **Noise looks like dispersion.** A-level cohorts are small (median 10-17 entries; SE 0.25-0.4 per subject), so most raw scatter across a school's subjects is sampling noise. The known-SE measurement model is essential, and school-specific dispersion will be shrunk hard toward the average.
- **A shared school effect is not teaching quality.** It also absorbs sixth-form intake and selection into subjects.
- **Selection into subjects varies by school** (who is entered for which A-level).

## 3. Proposed structure: put the school effect at GCSE level

Refinement from the discussion: a school is judged "outstanding" on **consistently high GCSE VA**, so the school-level construct should be built from the GCSE side and then linked to A-level.

**GCSE side** (the `gcse-school-quality.ipynb` notebook): six elements (English, Maths, Science, Humanities, Languages, Open), each with known SE, for each school $i$:

$$x_{ie} = \mu_e + \lambda_e g_i + \delta_{ie}, \quad \delta_{ie} \sim \text{Normal}(0, \tau_e s_i), \quad \log s_i = \sigma_s(\rho g_i + \sqrt{1-\rho^2}\, w_i)$$

- $g_i$: general GCSE quality ("consistently high VA")
- $s_i$: consistency (low = consistent); $\rho$: whether higher-quality schools are also more consistent
- $\delta_{ie}$ is marginalised out analytically, as in the factor models.

**A-level side** (not yet built): each A-level subject depends on $g_i$ and on the school's specific strength in the matching GCSE element $\delta_{ie(s)}$, plus a residual school effect. This nests the current notebooks, which lump $g_i$ and $\delta_{ie}$ together by regressing on one element.

Questions this answers:

1. Does general GCSE quality carry into every A-level subject?
2. Does subject-specific GCSE strength add anything beyond it?
3. Do schools with consistent GCSE results also have consistent A-level results?
4. How much of the A-level school effect is left after GCSE quality?

**Why this scales to all subjects.** $g_i$ is a common predictor for all ~40 A-level subjects. Subjects without a matching GCSE element (Psychology, Sociology, Business and so on) load on $g_i$ alone; subjects with one (Maths, sciences, English) also get the element-specific term. Plan: prototype on the subjects already built (about 12) before committing to all of them.

## 4. Data facts that shaped the design

- **Do not include `P8MEA` or `P8MEAEBAC` alongside the elements.** `P8MEA` is almost exactly a weighted sum of English, Maths, EBacc and Open (weights about 0.2, 0.2, 0.3, 0.3), and the EBacc element already contains Science, Humanities and Languages (Science correlates 0.96 with it). Use the six elements.
- **One general factor dominates.** In the 1,808 schools with all six elements, the first principal component holds about 76% of the variance. English, Maths, Science, Humanities and Open correlate 0.77-0.89 across schools; Languages only about 0.45 and is the weakest indicator (SE about 0.21, and it covers only pupils entered for languages).
- **Within-school scatter is mostly real.** Across the four Progress 8 elements the observed within-school spread is about 3.4 times what sampling noise alone gives, so roughly 70% of it is real. (Rough, from the four P8 elements only.)
- **Standard errors are clean sampling noise.** $se\sqrt{n}$ is constant within every measure (SD about 0.01), so the DfE interval formula holds.
- **The GCSE cohort is not the A-level cohort.** As we understand the published data, a school's GCSE VA is for its current Year 11, whereas its A-level students took GCSEs two years earlier and some joined from other schools. So $g_i$ is a school-quality *proxy*, not the A-level students' own prior attainment. To be confirmed against the DfE technical notes.

## 5. Regional factors

- The GCSE model as first built has **no regional term**. Its final section only averages fitted $g_i$ and $s_i$ by region, which is descriptive and not a test (shrinkage is toward the national mean, not the regional one).
- **Raw pattern** (1,808 schools with all six elements): London is well ahead (mean VA about +0.36 across elements); the South East, South West and East of England are modestly positive; the North East is lowest (-0.16); the West Midlands and North West are slightly negative. Region explains about 9% of the variance in a school's mean VA. Within-school **spread** is essentially the same in every region (0.29-0.34; region explains under 1% of it). London leads most on English; the North East is weak on every element, worst on Languages and Humanities.
- **Why it matters:** the humanities notebook found London above the national line at A-level *given* GCSE VA, but treated GCSE VA as a plain predictor. Separating a regional shift in general GCSE quality from a separate regional shift in A-level VA needs region in the GCSE model.
- **Proposed regional layer:** partial pooling of the mean of $g_i$ by region ($g_i \sim \text{Normal}(m_r, 1)$); optionally regional variation in mean log consistency (expected near zero, so mainly a check) and region-by-element deviations.

## 6. Status and open decisions

- **Done:** Maths, English, sciences, languages and humanities notebooks; README updated.
- **In progress:** `gcse-school-quality.ipynb` has been built and its baseline (no regional term) run started. Results are **not yet interpreted**; the notebook still has placeholder cells for that.
- **Open decisions:**
  1. Put the regional layer inside the GCSE notebook (second model, compared with the baseline), or only in the joint GCSE-to-A-level model?
  2. Prototype the joint model on the subjects already built before all subjects: proposed, not yet agreed.
  3. Whether to fit GCSE and A-level jointly (needed to propagate uncertainty in $g_i$) or in stages (simpler, but plugging in point estimates would understate uncertainty).
- **Known limits:** single year of data; PSIS-LOO is not usable for these latent-variable models; interpretations describe association across subjects within a school, not cause.
