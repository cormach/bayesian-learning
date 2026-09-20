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
- **Done (baseline):** `gcse-school-quality.ipynb` has been run and interpreted (no regional term). One general factor carries 82-87% of between-school variance in every element except Languages (37%); schools differ in consistency ($\sigma_s \approx 0.36$); general quality and consistency are only weakly related ($\rho \approx -0.19$). The residual check shows a Maths-Science / English-Open pairing that the single factor misses, so part of "consistency" is a subject-group split. Sampling is clean apart from $\mu_e$ (r_hat 1.02, ESS about 230).
- **Done (sensitivity to Open):** refitting without Open leaves general quality essentially unchanged (correlation 0.988 with the six-element $g_i$) but changes consistency (rank correlation 0.88; $\sigma_s$ 0.36 to 0.31) and removes the link between the two ($\rho$ from -0.19 to -0.03, interval spans 0). So $\rho$ is tentative. The extra scatter of the least consistent schools tilts towards Open and English. The idea that Open (which includes music, art and drama) carries something about a school's breadth is a reading of these results, not something they establish: Open is a single mixed score.
- **Done (second factor, `gcse-school-tilt.ipynb`):** the baseline's residuals were correlated in pairs (per-draw residual correlations +0.27 Maths-Science and +0.26 English-Open against a null range of about ±0.04). A second school-level factor, the *tilt*, fitted with only Humanities anchored at 0 and Maths' sign fixed, loads Maths +0.105 and Science +0.154, English -0.077, Open -0.079, Languages -0.038. It removes the pairing (residual correlations about 0 to 0.07), leaves general quality unchanged (0.999 correlation of average expected VA), takes a quarter to two fifths of the element-specific variance in English, Maths and Open and most of Science's, and is unrelated to quality and consistency (posterior means correlate 0.04). $\sigma_s$ rises to 0.39 and $\rho$ strengthens to -0.26 [-0.33, -0.19], so the earlier concern that $\rho$ rested on Open is not repeated once the pairing is modelled (it still disappears when Open is dropped). Caveats: sampling of $\tau_{Science}$ is poor (r_hat 1.15; the posterior runs to near zero because the tilt absorbs Science's specific scatter); the tilt is only moderately determined per school (posterior SD 0.63); loadings are relative to the Humanities anchor.
- **Open decisions:**
  0. ~~Second factor for the Maths-Science / English-Open structure?~~ **Done** (see above). Next: carry $g_i$, $h_i$, $s_i$ into the A-level model; ask whether the tilt predicts the corresponding pattern at A-level beyond general quality.
  1. ~~Put the regional layer inside the GCSE notebook?~~ **Done.** It is in the GCSE notebook, compared with the baseline. Regions explain about 10% of the variance in $g_i$ ($\sigma_m \approx 0.32$; London +0.59, North East -0.36), consistency barely varies by region ($\sigma_c \approx 0.07$), and nothing else in the model or at school level moves. Keep it for the A-level link; the baseline is enough for GCSE alone.
  2. Prototype the joint model on the subjects already built before all subjects: proposed, not yet agreed.
  3. Whether to fit GCSE and A-level jointly (needed to propagate uncertainty in $g_i$) or in stages (simpler, but plugging in point estimates would understate uncertainty).
- **Done (A-level, `a-level-school-quality.ipynb`):** seven subject groups (Maths alone, Sciences, English, Humanities, Social sciences, Business & Computing, Creative arts) fitted jointly with the GCSE model (regional means, tilt, consistency). GCSE explains 28% of the true school-level variance in Maths and Sciences, 19% Humanities, 17% English, 12% Social sciences, 8% Business & Computing, 4% Creative arts. General quality reaches every group equally (slope 0.08-0.10 per SD); the tilt reaches Maths (+0.18) and Sciences (+0.15) and little else, and for those two explains more than general quality does (about 20% against 7-8%). A shared A-level quality that GCSE does not explain carries 45-55% of the variance for most groups (19% English, 7% Creative arts). One A-level quality tied to GCSE (your original idea) gives a correlation of 0.42 with general GCSE quality but cannot reproduce the group-specific tilt slopes, and the flexible model fits the cross-group residuals much better. Regional differences beyond GCSE are small (sigma_psi 0.14; up to about 0.04 points; clear only for East of England and West Midlands). Modelling lesson: a sign constraint on the tilt (Maths > 0) pushed the mirror-image solution onto a boundary and one chain landed there (r_hat 1.5); the fix is to leave the sign free and align each chain afterwards. Next: matched-element deltas, A-level scatter against GCSE consistency.
- **Done (England-wide shortlist model, `a-level-england-shortlist.ipynb`):** one model fitted on all 2,747 institutions in England, with the read-out (best institutions for a subject in a chosen region or local authority) as a filter at the end. Nine A-level groups with Maths, Economics and Psychology as separate subjects. Institutions with A-level but no GCSE results (864: independent schools, colleges, 16-19 academies) are included without an invented GCSE profile: they get the typical GCSE quality for their region and local authority, no tilt, their own scatter and a mean shift by type (independent about +0.15 to +0.3, college about -0.12 to -0.26). Lesson: an earlier version gave them a random GCSE profile, which inflated the GCSE slopes for everyone (GCSE explaining 35% of Maths variance instead of 29%); it was not a missing-geography problem, since adding a local-authority layer left the shares unchanged. Geography below region is real, though: local authorities differ in GCSE quality by SD 0.31 beyond region (regions: 0.35), and in A-level shared quality by SD 0.19; the regional A-level spread shrinks to 0.08 once authorities are in. Sampling: 0 divergences; the overall GCSE level mu mixes worst (r_hat 1.10, ESS 51), which does not affect institution estimates. Outputs: per-institution estimated true value added with intervals and P(top fifth in area / in England); full tables exported to `results/`. Names come from the public GIAS register (`data/school-names.csv`). Limits: value added is a school average for one year, cohorts are small, and the data say nothing about sixth-form entry requirements or whether places are open to external applicants.
- **A-level groups (decided so far).** The A-level side uses a short list of subject groups, not every subject. Decided: **Mathematics** on its own (Further Maths is excluded, since its pupils also take Maths and would be counted twice), and **Creative arts** = Art & Design (all types), Music, Music Technology, Drama & Theatre Studies and Dance, pooled by entries (974 of the 1,883 GCSE-model schools, median 14 entries, median SE 0.28). The pooled SE assumes the subjects' cohorts are separate, so it is a lower bound where pupils take two art subjects (37% of schools with any art subject have two or more types). Music alone is too thin to use (82 schools, median 7 entries, SE 0.40). *Proposed, not yet agreed:* Sciences (Biology, Chemistry, Physics), English (Literature, Language, Language & Literature), Humanities (History, Geography, Religious Studies, Philosophy), Social sciences (Psychology, Sociology, Economics, Politics, Law), Business & Computing. A subject's VA needs at least 6 entries, so a school only appears where its cohort reaches that.
- **Known limits:** single year of data; PSIS-LOO is not usable for these latent-variable models; interpretations describe association across subjects within a school, not cause.
