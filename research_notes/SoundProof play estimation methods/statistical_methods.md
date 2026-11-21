# Statistical methods for estimating song-play totals and shares from a sampled, imperfect-detection sensor network (SoundProof, Cameroon)

Notation used throughout these notes (my own, for implementation):
- Venue population U, with N venues split into strata h = 1..H (city × venue type × size band). Sample s, with n_h venues in stratum h out of N_h. Inclusion probability π_i, design weight w_i = 1/π_i.
- Song j = 1..J (catalogue), plus explicit "unmatched music" and "live/DJ" buckets.
- y_ij: detected plays of song j at venue i during monitored time. e_i: device-hours actually monitored (net of outages). H_i: venue opening hours in the period.
- p_j (or p_ij): probability that a real play is detected and correctly identified. f: false-positive proportion among detections.
- Pot = 100,000,000 FCFA. Ŝ_j: estimated share of song j.

## 1. Survey sampling design and estimators (stratification, PPS, HT/Hájek, ratio, time sampling, panels, sample size, design effects)

### Takeaway
Treat the venue network as a stratified (optionally PPS) probability panel. Estimate each song's national total with a weighted Horvitz–Thompson expansion of effort- and detection-corrected venue counts. Estimate shares with the ratio (Hájek-type) estimator Ŝ_j = T̂_j / Σ_k T̂_k, which is more stable than HT because the unknown national total cancels. Venue-level clustering of playlists gives a large design effect, so set sample size by effective plays, not raw plays.

### Cited Findings
- HT total: Ŷ_HT = Σ_{i∈s} Y_i/π_i. HT mean = (1/N)·Ŷ_HT, and the mean is unbiased for the population mean. For complex/post-stratified designs, variance is "not straightforward" and resampling (bootstrap, jackknife) gives consistent variance estimates. — [Wikipedia: Horvitz–Thompson estimator](https://en.wikipedia.org/wiki/Horvitz%E2%80%93Thompson_estimator)
- Neyman allocation: n_h = n·N_h·S_h / Σ_h N_h·S_h. Stratified mean variance: Var(ȳ_st) = Σ_h (N_h²/N²)(1 − f_h) S_h²/n_h, with f_h = n_h/N_h. Cost-optimal variants exist when per-stratum costs differ. (The fetched page rendered the variance formula garbled; it was reconstructed from the text, but it is the standard textbook form.) — [Wikipedia: Neyman allocation](https://en.wikipedia.org/wiki/Neyman_allocation)
- Design effect: Deff = var(θ̂ under design) / var(θ̂ under SRSWOR). Kish unequal-weighting Deff = n·Σw_i²/(Σw_i)² = 1 + CV²(w). This assumes uncorrelated, equal-variance outcomes and weights that are uncorrelated with y. Cluster Deff = 1 + (n̄* − 1)ρ, where n̄* is the average cluster size (conservative when sizes vary) and ρ is the intraclass correlation. n_eff = n/Deff; for weights, n_eff = (Σw)²/Σw². — [Wikipedia: Design effect](https://en.wikipedia.org/wiki/Design_effect)
- The weighted mean is described as a ratio estimator: an estimated total divided by an estimated population size, i.e. the Hájek form. — [Wikipedia: Design effect](https://en.wikipedia.org/wiki/Design_effect)
- Kish's Deff is reasonable when equal weighting is near-optimal, but "does not reveal efficiencies that may accrue from sampling with varying probabilities" (i.e. PPS). — [Statistics Canada, Survey Methodology 2015](https://www150.statcan.gc.ca/n1/pub/12-001-x/2015002/article/14236/05-eng.htm)
- Gabler, Haeder & Lahiri give a model-based justification of the combined weighting × clustering design effect (an upper bound). The exact expression could not be verified from the snippet. — [Statistics Canada 1999](https://www150.statcan.gc.ca/n1/pub/12-001-x/1999001/article/4718-eng.pdf)
- Nielsen analogy:
  - Nielsen measures TV audiences with a probability panel and people meters, and weights the panel to universe estimates. One source notes a demographic was underrepresented by 2% against universe estimates before weighting. — [EBSCO research starter](https://ebsco.com/research-starters/communication-and-mass-media/understanding-nielsen-ratings); [NextTV: Nielsen adds weight to U.S. sample](https://nexttv.com/news/nielsen-adds-weight-us-sample-100469)
  - Sources disagree on panel size: ~9,000 national households (older EBSCO account) vs ~45,000 households (2023 AAPOR handout). — [EBSCO](https://ebsco.com/research-starters/communication-and-mass-media/understanding-nielsen-ratings); [AAPOR handout](https://aapor.org/wp-content/uploads/2023/01/HANDOUT_Using-Designed-Data-to-Correct-for-Errors-in-Big-Data.pdf)
  - Nielsen says thousands of TV shows have audiences "too small to be detected by panel-based measurement" even with 100,000+ viewers. It now blends panel data with big data and reports lower average relative error in the New York DMA. — [Nielsen 2025 insight](https://www.nielsen.com/insights/2025/improve-data-quality-big-data-people-panels)
  - Comscore argues no panel is truly random, so selection bias persists beyond sampling error. — [Comscore blog 2021](https://www.comscore.com/Insights/Blog/Relative-Errors-in-Television-Audience-Measurement-The-Future-is-Now)
- Collecting-society precedent (PRS for Music, UK):
  - PRS prefers census, then a "representative sample of actual performances", then "analogy" where neither is feasible. — [PRS distribution policy](https://www.prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/prs-distribution-policy/2prspolicydistributioncyclesandconceptsoct-22.pdf)
  - Pub background music falls to analogy because a statistically representative sample was judged not cost-effective. — [PRS distribution policy](https://www.prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/prs-distribution-policy/2prspolicydistributioncyclesandconceptsoct-22.pdf)
  - The public-performance revenue split cited is census 29% / sample 18% / analogy 53% (Oct 2022 policy, may be dated). — [PRS distribution policy](https://www.prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/prs-distribution-policy/2prspolicydistributioncyclesandconceptsoct-22.pdf)
  - For licensed venues, PRS researchers collect data from "a statistically valid sample of UK venues" and match it against broadcast data, which is then used to calculate payments. — [PRS: music played in public royalties](https://prsformusic.com/royalties/music-played-in-public-royalties)
  - This means radio is used as a proxy (analogy) for venue play. SoundProof's direct venue measurement improves on that, and radio-only allocation is the natural baseline to beat.

### Inferences
The following are standard textbook results (Lohr, *Sampling: Design and Analysis*; Cochran, *Sampling Techniques*) and my derivations for this setting. The formula forms were not separately web-verified in this session unless cited above.

**Per-venue corrected count.** ỹ_ij = [y_ij(1 − f̂) / p̂_ij] × (H_i / e_i). The detection correction is Section 2 and the effort/outage correction is Section 3.

**Total estimator (stratified HT).**
- T̂_j = Σ_h (N_h/n_h) Σ_{i∈s_h} ỹ_ij.
- With PPS on a size measure x_i (e.g. capacity or nightly opening hours), use π_i = n_h·x_i / X_h.

**Share estimator (ratio / Hájek form).**
- Ŝ_j = T̂_j / T̂_·, where T̂_· = Σ_k T̂_k (include the unmatched and live buckets in the denominator if they are paid; exclude them if not).
- Because numerator and denominator share the same weights, the estimator is invariant to errors in N_h and in the venue-frame size.
- It is the right estimator when the pot is fixed and only relative plays matter.

**Linearized variance of a share.**
- Define u_ij = w_i(ỹ_ij − Ŝ_j·ỹ_i·)/T̂_·.
- Then v(Ŝ_j) ≈ Σ_h (1 − f_h)·n_h/(n_h − 1)·Σ_{i∈s_h}(u_ij − ū_hj)².
- This is the "ultimate cluster" with-replacement approximation.
- Implement it in numpy. In R, `survey::svyratio` is a reference implementation for cross-checking.

**Stratification variables.** City (Douala, Yaoundé, regional towns, rural), venue type (bar/snack-bar, nightclub, maquis/restaurant, hotel), size/activity band, and grid-power reliability (because it drives outages). Stratifying on power reliability makes outage missingness closer to MAR within strata.

**Time sampling.** Phones record continuously, so time is a census within the monitored device-hours rather than a sample. Time matters in two places:
- Outages leave unmonitored windows, so post-stratify effort by hour-block × weekday (e.g. 6 blocks × 2 day-types).
- Costly human audits (ground truth, false-positive checks) should be time-sampled with a systematic random start across hour-blocks. Marques et al. did this for false positives (see Section 2).

**Panel design (Nielsen-like).**
- Use a fixed rotating panel: e.g. replace 1/4 of venues per quarter. This gains period-to-period correlation, which makes change estimates precise, while limiting panel fatigue and venue-gaming.
- Keep venue identities confidential to deter manipulation (playlist stuffing).
- Weight to known universe totals if a licensing registry exists (raking or calibration on city × type counts).

**Sample size for share precision.**
- Under SRS of plays, an effective sample of n_eff ≈ z²(1 − S)/(S·r²) plays is needed for relative margin r on a share S.
- Example: S = 1%, r = 20%, z = 1.96 gives n_eff ≈ 9,500 plays.
- Play counts are clustered by venue (same DJ, same playlist), so multiply by Deff = 1 + (m̄ − 1)ρ.
- With m̄ = 2,000 plays per venue per month and even ρ = 0.01, Deff ≈ 21, which needs ~200k plays, i.e. ~100 venue-months.
- So the number of venues, not the hours per venue, drives precision for mid-sized songs. Estimate ρ per song from pilot data with a one-way ANOVA across venues.
- Practical design: more venues with fewer hours each beats few heavily instrumented venues.

**Radio.** Radio monitoring of streams is a census of monitored stations, with detection near 1 on clean digital streams. Keep radio as its own distribution section with its own pot share (like PRS), or as an auxiliary variable for a ratio/regression estimator of venue plays. Do not pool raw radio and venue counts.

### Gaps
- No Cameroon-specific venue frame size, venue-type distribution, or playlist intraclass-correlation values were found. ρ and m̄ above are illustrative and must come from a pilot.
- Nielsen's published sampling-error tables and exact weighting variables were not retrieved. Panel-size figures conflict across sources (9k vs 45k households).
- The exact formula for the combined Kish clustering × weighting Deff (Gabler et al.) was not verified.

## 2. Imperfect detection (calibration/planted plays, occupancy, N-mixture, capture–recapture with two phones, passive acoustic monitoring practice)

### Takeaway
Estimate detection probability empirically and locally:
- Planted known plays ("trials with known sources") are the primary tool.
- Two co-located phones (double-observer capture–recapture) are a secondary, in-production check.
- False positives come from a human-audited random subsample.

This follows the passive-acoustic-monitoring "cue counting" template: plays = detections × (1 − f) / p, then scale up for coverage. Capture–recapture with two phones is biased low if both phones fail on the same plays (shared noise or distance), so use it as a diagnostic, not the sole correction.

### Cited Findings
- **Canonical estimator (Marques et al. 2013).** The general passive-acoustic density estimator uses n (detected cues or objects), f (false-positive proportion), p (detection probability within covered area a) and r (a multiplier converting cues to animals, e.g. a call rate).
  - In cue counting, density of cues is divided by an independently estimated cue rate.
  - For fixed sensors the surveyed area is Kπw² (e.g. K = 82 hydrophones, w = 8 km in Marques et al. 2009).
  - Source: Marques et al., "Estimating animal population density using passive acoustics", *Biological Reviews* 88(2):287–309, doi:10.1111/brv.12001. — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)
- **How p is estimated.** In Marques et al. 2009, DTAG-tagged whales provided known emission times, so detection by hydrophones was modelled with a GAM and averaged by Monte Carlo. Synthetic playback and captive animals at known distances are alternatives with drawbacks. Sonar-equation propagation models are also possible, but the authors prefer empirical measurement because model assumptions may fail. — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)
- **Effective area.** p is the average of the detection function g(y) over the distance distribution π(y), truncated at w. The effective area a_e = p·a is where, on average, animals equal detections. — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)
- **False positives and local estimation.** f was estimated by manually inspecting a systematic random sample of the monitoring data. "The most reliable estimates of f, p and r will come from data collected at the same time and region as n." — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)
- **Mark–recapture formulas.**
  - Lincoln–Petersen: N̂ = nK/k.
  - Chapman: N̂_C = (n+1)(K+1)/(k+1) − 1, which is less biased in small samples (truncate, do not round).
  - Log-based CI: bounds = K + n − k − 0.5 + [(K−k+0.5)(n−k+0.5)/(k+0.5)]·exp(±z·σ̂), with σ̂ = √[1/(k+0.5) + 1/(K−k+0.5) + 1/(n−k+0.5) + (k+0.5)/((n−k+0.5)(K−k+0.5))].
  - Assumptions: a closed population, and equal capture probability for all individuals that is independent between occasions. With two samples this cannot be tested.
  - Source: [Wikipedia: Mark and recapture](https://en.wikipedia.org/wiki/Mark_and_recapture)
- **Occupancy (MacKenzie et al. 2002, *Ecology* 83:2248–2255).** A likelihood model separates occupancy ψ from detection p using repeat surveys. It is generally unbiased when p > 0.3. In the anuran example, naive occupancy was 44% lower than the estimate (0.49 for American toad). — [USGS pubs](https://pubs.usgs.gov/publication/5224176); [Idaho F&G abstract](https://idfg.idaho.gov/species/node/1494568)
- **N-mixture (Royle 2004, *Biometrics* 60:108–115).** n_it ~ Binomial(N_i, p), N_i ~ Poisson(λ) (or negative binomial), from spatially replicated counts. — [USGS](https://pubs.usgs.gov/publication/5224798); [Royle 2004 PDF](https://cals.arizona.edu/classes/wfsc578/Royle%202004.%20N-mixture%20models%20for%20estimating%20population%20size.pdf)
- **N-mixture caveat.** N-mixture models are equivalent to multivariate Poisson/NB models and "can produce unrealistically large estimates of abundance", especially with low detection or few visits (Dennis, Morgan & Ridout 2014). — [PMC4406156](https://pmc.ncbi.nlm.nih.gov/articles/PMC4406156/)

### Inferences

**Map to SoundProof (cue counting).**
- Song j's plays in venue-period: N̂_ij = y_ij(1 − f̂_j)/p̂_ij.
- The national total then uses Section 1 weights. No r multiplier is needed because the cue is the play.
- The "covered area" analogue is the fraction of venue-hours monitored (Section 3).

**Calibration protocol (planted plays).**
- During installation and periodically (e.g. monthly), have staff play a known randomized set of catalogue tracks through the venue system at normal volume. Randomize volume, time of night, crowd level, track genre/loudness, and play duration (full vs 30–60 s snippets).
- Record detected ⇔ planted. Fit a logistic detection model: logit p = β0 + β·(SNR proxy, ambient dB from the phone mic, distance/placement, genre, track loudness/LUFS, play duration, device model, venue random effect).
- Predict p for every real play window from the same covariates, which the phone logs continuously.
- Use a Beta(1,1) prior per cell or a hierarchical logistic model to stabilize p̂.

**Error propagation for 1/p̂.** By the delta method, Var(N̂) ≈ y²·Var(p̂)/p⁴ + N·(1 − p)/p (binomial thinning). Low p explodes variance, so set an operational floor: windows with p̂ < 0.3 are treated as unmonitored rather than up-weighted. This mirrors MacKenzie's >0.3 regime and Dennis et al.'s instability warning.

**Two phones as capture–recapture.**
- Fingerprint matches carry timestamps, so match each play across phones A and B within a ±T-second window. n_A, n_B and m (detected by both) give the Chapman estimate N̂.
- Implied p_A = m/n_B and p_B = m/n_A.
- Also standard (Seber 1982, from memory, not verified this session): Var(N̂_C) = (n_A+1)(n_B+1)(n_A−m)(n_B−m)/[(m+1)²(m+2)].
- **Main bias.** Detection heterogeneity across plays (quiet tracks, crowd noise, short plays) is shared by both phones, giving positive dependence, so N̂ is biased low.
- **Mitigations:**
  - Place phones at different positions (one near the speaker, one far).
  - Stratify by covariates (Huggins-style conditional likelihood on SNR/genre).
  - Prefer planted plays for the absolute level and use two-phone recapture to monitor drift.

**Occupancy vs N-mixture applicability.**
- Occupancy (ψ = probability a song is played at least once at venue i in week t; repeat "surveys" = nights) answers reach questions ("how many venues play X"). It is useful for dashboards and fraud checks, not for royalty totals.
- N-mixture needs a closed N across repeated counts. Here simultaneous multi-phone counts of the same night meet closure, but this reduces to the capture–recapture case. N-mixture adds little beyond that and is unstable, so I do not recommend it for the money calculation.

**False positives.**
- Fingerprinting rarely mislabels at high confidence, but low-confidence matches, covers, remixes and DJ edits do occur.
- Draw a systematic random sample of matches (e.g. 1–2% stratified by confidence band). A human listener labels each as correct, wrong song, or a live/DJ version. Estimate f per confidence band and apply (1 − f̂).
- Matches below a confidence threshold go into an "unmatched" bucket.

**Live/DJ versions.** Treat these as a separate cue class with its own p (fingerprinting often fails on live renditions). Options:
- (a) Pay them from a separate analogy-based pool.
- (b) Have human audits estimate the proportion of live plays per song from the audit sample.
- (c) Use cover-song/melody matching as a second detector, with its own p.

### Gaps
- No published detection-rate figures for commercial audio fingerprinting (Shazam-style) in noisy bars were found. They must be measured in the pilot.
- The Blainville's beaked whale numeric inputs and the exact equation layouts in Marques et al. were not extracted (equations were images).
- The Chapman variance formula was not verified from a fetched source (stated from Seber, standard).

## 3. Handling device downtime (effort-based rates, missing data)

### Takeaway
Model plays as a rate per monitored device-hour and expand over the venue's operating hours by hour-block. Do not treat outage hours as zero plays. Outages (power cuts) are not missing completely at random: they cluster by city, time and grid quality. Impute within venue × hour-block × weekday cells, or with a Poisson model that uses log(effort) as an offset.

### Cited Findings
- Passive acoustic density estimation divides counts by monitoring time T and covered area. In the Moretti et al. Blainville's beaked whale example, n_d dive starts over period T in area A were combined with a dive rate r_d from tags and a group size from visual observations, i.e. counts are always normalized by effort. — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)
- HT variance under complex designs (like nonresponse or post-stratification adjustments) is best obtained via bootstrap or jackknife. — [Wikipedia: Horvitz–Thompson estimator](https://en.wikipedia.org/wiki/Horvitz%E2%80%93Thompson_estimator)

### Inferences

**Effort logging.** Each phone emits heartbeats (e.g. every 60 s) recording power/battery state, mic level and app alive. Effort e_ib = minutes of valid monitoring in venue i, hour-block b. Opening hours O_ib come from the venue survey or are inferred from audio activity on fully-monitored days.

**Rate expansion.** ỹ_ij = Σ_b (y_ijb / e_ib)·O_ib. Expansion is only valid within the block, because song mix changes by hour: early-evening pop and late-night coupé-décalé/makossa/bikutsi differ.

**Model-based alternative.**
- y_ijb ~ Poisson or NegBin(μ_ijb), with log μ_ijb = log(e_ib) + log(p_ijb) + α_i + γ_b + θ_j + (θ×γ)_jb.
- Predict for the unmonitored hours O_ib − e_ib and add the predictions to the observed counts.
- Fit with statsmodels GLM with an offset, or PyMC/NumPyro for the hierarchical version.

**Missingness rules.**
- Blocks with e_ib/O_ib below 25% get imputed from the venue's other days in the same block (donor or hot-deck), not expanded.
- Venues with more than 50% of the period missing are dropped and their weight redistributed within stratum. This is the classic weighting-class nonresponse adjustment: w_i* = w_i × (Σ_{h} w)/(Σ_{h, responding} w).
- Report effort coverage (% of venue-hours monitored) per stratum as a transparency metric.

**MNAR risk.** If power cuts coincide with generator-powered nights that play different music, imputation from grid-powered nights is biased. Log generator vs grid status (phone charging state) as a covariate.

### Gaps
- No source on Cameroon power-outage patterns was retrieved for this topic.
- No literature on downtime imputation specifically for acoustic sensor networks was fetched. The above is standard survey nonresponse practice (Lohr ch. on nonresponse).

## 4. Shares vs totals (when detection cancels; song/genre-specific bias and correction)

### Takeaway
For a fixed pot, only relative plays matter. Any detection factor common to all songs within a venue-time cell (venue loudness, phone placement, outage fraction) cancels in Ŝ_j, as long as it does not correlate with song mix. Song-specific detection p_j does not cancel: share bias is proportional to p_j / p̄. Correct it by dividing by song- or genre-level p̂ from calibration. Errors in the overall detection level then still cancel, which makes the share far more robust than the total.

### Cited Findings
- The Hájek/ratio form divides an estimated total by an estimated size, so common multiplicative factors cancel. — [Wikipedia: Design effect](https://en.wikipedia.org/wiki/Design_effect)
- Imperfect detection that is not accounted for biases occupancy and abundance estimates (MacKenzie 2002; reviewed in *J. Plant Ecology* 2012). — [J Plant Ecol](https://academic.oup.com/jpe/article/5/1/22/1296465)
- Calibration data should come from the same time and region as the counts. — [PMC3743169](https://pmc.ncbi.nlm.nih.gov/articles/PMC3743169/)

### Inferences

**Derivation.**
- Suppose E[y_ij] = c_i·p_j·N_ij, where c_i is the venue/time factor and p_j is the song factor.
- Then the naive share S_j^naive = Σ_i w_i c_i p_j N_ij / Σ_k Σ_i w_i c_i p_k N_ik.
- If p_j ≡ p, it equals the c-weighted true share. This is unbiased if c_i is uncorrelated with venue song mix, or if you first correct c_i per venue.
- If p_j varies, S_j^naive ∝ p_j·S_j, so the corrected share Ŝ_j = (y_j/p̂_j) / Σ_k (y_k/p̂_k).
- Only relative p_j/p_k is needed, so calibration that misestimates absolute p by a constant factor still yields correct shares.

**Which songs have different p?** Likely drivers to test in the calibration GLM:
- Bass-heavy or noisy genres vs acoustic tracks.
- Tracks often played partially (DJ cuts).
- Remixes or edits not in the reference database.
- Very new releases whose fingerprints were added late.

Estimate p at genre level (enough data), with song-level random effects shrunk toward the genre mean.

**Venue-mix confounding.** If loud nightclubs (low c_i) play more of genre G, c does not cancel. Correct per venue (divide by p̂_i from that venue's planted plays) before aggregation.

**Bias sensitivity.**
- If genre G has true share 30% and p_G/p_other = 0.8, then naive Ŝ_G = 0.3·0.8/(0.3·0.8 + 0.7) = 25.5%.
- This is a 4.5-point bias worth 4.5M FCFA. It shows why genre-level correction matters more than total-level correction.

**Uncatalogued songs.** The fingerprinter can flag "music present but unmatched". The unmatched share of music-time is directly measurable, which is a better estimator than Good–Turing for catalogue-gap mass.
- Policy choice: (a) hold the unmatched share in escrow until tracks are registered, then pay retroactively from stored fingerprints/hashes; or (b) redistribute pro-rata.
- Option (a) is fairer and defensible ("we kept the audio fingerprints, register and claim").

### Gaps
- No empirical study was found on genre-specific fingerprinting detection rates. A pilot calibration is essential.

## 5. Long tail and rare songs (Good–Turing, Chao1, small-area estimation, shrinkage, minimum guarantees)

### Takeaway
- Most catalogue songs will have 0–2 detections per period, so their direct estimates are extremely noisy. Use empirical-Bayes (Gamma–Poisson or Dirichlet–multinomial) shrinkage toward artist- or genre-level rates to stabilize payments while keeping the total fixed.
- Use Good–Turing (N1/N) to estimate the play mass of catalogue songs never detected in the sample, and Chao1 to estimate how many such songs exist.
- Fund any per-song minimum guarantee by a transparent top-slice, not by ad-hoc reallocation.

### Cited Findings
- **Good–Turing.**
  - Missing mass p0 = N1/N, where N1 is the number of species seen exactly once and N the total observations.
  - Adjusted count r* = (r+1)N_{r+1}/N_r, and per-species probability r*/N.
  - Simple Good–Turing smooths N_r by regressing log Z_r on log r, with Z_r = N_r/(0.5(t − q)) using neighbouring nonzero indices.
  - Use raw N_r for small r and smoothed values for large r. Public-domain code exists.
  - Source: [Wikipedia: Good–Turing frequency estimation](https://en.wikipedia.org/wiki/Good%E2%80%93Turing_frequency_estimation)
- **Chao1.**
  - Classic: S = S_obs + f1²/(2f2), which needs f2 > 0.
  - Bias-corrected: S = S_obs + f1(f1−1)/(2(f2+1)), always computable (Chao 1987); scikit-bio defaults to this form.
  - Variance (f1, f2 > 0): f2[½(f1/f2)² + (f1/f2)³ + ¼(f1/f2)⁴].
  - Chao1 is a lower bound on richness.
  - Sources: [scikit-bio chao1 docs](https://scikit.bio/docs/dev/generated/skbio.diversity.alpha.chao1.html); [spacc vignette](https://ftp.fau.de/cran/web/packages/spacc/vignettes/richness-estimation.html); [Gotelli & Chao ch. 4](https://www.uvm.edu/%7Engotelli/manuscriptpdfs/Chapter%204.pdf)
- **Small-area estimation.** Fay–Herriot is a random-effects model used for small domains. Standard references are Rao, *Small Area Estimation* (2003), and Jiang & Lahiri (2006). The Wikipedia page gives no formulas. — [Wikipedia: Small area estimation](https://en.wikipedia.org/wiki/Small_area_estimation)
- **Small audiences in panels.** Nielsen states that small audiences are undetectable by panels even of 100k viewers, motivating blending with other data sources. — [Nielsen 2025](https://www.nielsen.com/insights/2025/improve-data-quality-big-data-people-panels)

### Inferences

The following are standard results; the formulas were not web-verified this session.

**Gamma–Poisson empirical Bayes.**
- Model: corrected counts ỹ_j ~ Poisson(λ_j·E), with λ_j ~ Gamma(α_g, β_g) per genre g.
- Posterior mean: λ̂_j = (ỹ_j + α_g)/(E + β_g).
- Fit α_g and β_g by method of moments or a NegBin marginal likelihood across songs in g.
- Shrinkage weight toward the genre mean: E/(E + β_g). It is large for heavily-played songs and small for rare ones.

**Dirichlet–multinomial for shares.** Ŝ_j = (ỹ_j + a·m_j)/(Ñ + a), where m_j is the prior mean share (e.g. genre or radio-informed) and a is the concentration. Estimate a by marginal likelihood across venues (the DM overdispersion also captures venue clustering).

**Fay–Herriot for strata/regions.**
- Model: θ̂_d = θ_d + e_d with e_d ~ N(0, ψ_d), and θ_d = x_d'β + v_d with v_d ~ N(0, σ_v²).
- EBLUP: γ_d θ̂_d + (1 − γ_d) x_d'β̂, with γ_d = σ_v²/(σ_v² + ψ_d).
- Use it if regional reporting is needed (e.g. plays in Bafoussam). Radio plays or streaming counts can serve as the covariate x.

**Hierarchical Bayes full model (prototype in PyMC/NumPyro/Stan).**
- y_ijb ~ NegBin(exp(log e_ib + log p_ijb + α_i + θ_j + γ_b), φ).
- θ_j ~ N(μ_genre(j) + δ_artist(j), σ²).
- p from the calibration logistic sub-model, fit jointly so detection uncertainty propagates.
- Posterior shares from draws: S_j^(d) = T_j^(d) / Σ_k T_k^(d).

Fairness note: shrinkage moves money from apparently-high rare songs to apparently-zero rare songs within the same genre. It is defensible as "best estimate given evidence" but must be explained. Keep the total fixed (renormalize after shrinkage).

**Long-tail split.**
- Good–Turing's p0 = N1/N estimates the share of plays belonging to catalogue songs with zero detections. Pay that mass as a "long-tail pool", distributed by a prior (e.g. registration, radio plays, or equal per active registered song) rather than to nobody.
- If N1/N is large (e.g. >10%), the sample is too small to resolve the tail, which is itself a design signal.
- Chao1 on detected song IDs tells how many catalogue songs are being played at all, and so how many to expect in the long-tail pool.

**Minimum guarantee.**
- Reserve a top-slice (e.g. 2–5% of the pot) for a floor per registered and verified-active song or artist, so that per-song payments are not dominated by sampling noise.
- Show it as a separate line item to regulators.
- An alternative is a threshold rule: songs whose 90% CI includes 0 receive the long-tail pool rate rather than a direct estimate.

### Gaps
- No published music-royalty application of Good–Turing or Chao1 was found.
- Fay–Herriot formulas were not verified from a fetched source (Wikipedia lacks them). Use Rao (2003) / Rao & Molina (2015).

## 6. Uncertainty quantification and money allocation under uncertainty

### Takeaway
- Report every share with an interval from a stratified cluster (venue-level) bootstrap or the posterior. Multinomial intervals that treat plays as independent understate uncertainty badly because of venue clustering.
- Allocate by the point (expected) share, which is the unbiased, loss-minimizing choice for a fixed pot under squared or absolute error.
- Manage the risk of noisy small payments with a hold-back reserve, multi-period smoothing, and a transparent reconciliation/carry-forward rule.

### Cited Findings
- **Simultaneous multinomial intervals.**
  - Methods include Quesenberry–Hurst (1964, *Technometrics* 6:191–195), Goodman (1965, *Technometrics* 7:247–254), which is shorter than Q-H at usual levels, and Sison–Glaz (1995, *JASA* 90:366–369).
  - R implementations: `DescTools::MultinomCI` (Sison–Glaz code translated from May & Johnson 2000, *J Stat Software*) and `CoinMinD`, which also has Dirichlet Bayesian intervals.
  - Sources conflict on whether Q-H/Goodman intervals are symmetric; SAS says they are asymmetric and stay within [0,1].
  - Sources: [Goodman 1965 PDF](https://www.stat.cmu.edu/technometrics/59-69/VOL-07-02/v0702247.pdf); [DescTools MultinomCI](https://search.r-project.org/CRAN/refmans/DescTools/html/MultinomCI.html); [CoinMinD](https://cran.r-project.org/web/packages/CoinMinD/refman/CoinMinD.html); [SAS blog](https://blogs.sas.com/content/iml/2017/02/15/confidence-intervals-multinomial-proportions.html)
- Bootstrap/jackknife give consistent variance estimates for HT-type estimators under complex designs. — [Wikipedia: Horvitz–Thompson estimator](https://en.wikipedia.org/wiki/Horvitz%E2%80%93Thompson_estimator)
- PRS operates distributions in cycles and reconciles via its distribution policy (census/sample/analogy sections). — [PRS distribution policy guide](https://www.prsformusic.com/-/media/files/prs-for-music/royalties/prs-distribution-policy-guide)

### Inferences

**Bootstrap recipe (Rao–Wu rescaling, standard).** For b = 1..B (B ≥ 1000):
- Within each stratum h, resample n_h − 1 venues with replacement.
- Rescale weights w_i* = w_i × (n_h/(n_h − 1)) × (times venue i drawn).
- Also redraw the calibration data (planted-play trials) to propagate p̂ uncertainty, and the false-positive audit sample.
- Recompute all Ŝ_j^(b).
- Report the 2.5–97.5 percentiles and the FCFA equivalent (Pot × Ŝ_j).
- Venue-level resampling preserves within-venue clustering, which multinomial CIs ignore.

**Communicating to non-statisticians.**
- "Song X: estimated 1.8M FCFA (90% range 1.4M–2.3M)."
- Rank bands: "very likely top 10" if P(rank ≤ 10) > 0.9 in the bootstrap or posterior.
- Show the "money at risk" = Pot × 0.5 × E[Σ_j |Ŝ_j − S_j|], estimated from the bootstrap spread around the point estimate.

**Allocation rule.** Pay_j = Pot × Ŝ_j, using the posterior mean or the design-based point estimate. It sums exactly to the pot and is unbiased in expectation, so over many periods every rights-holder receives their true share on average.

**Variance management:**
- (a) Hold-back: pay 85–90% now and 10–15% at an annual reconciliation using pooled 12-month data, which has much narrower CIs.
- (b) Smoothing: pay on a rolling 3-period average of Ŝ (reduces variance about 3× for stable songs; lags hits — pair with the hold-back).
- (c) Carry-forward: payments below a de-minimis amount (e.g. 5,000 FCFA) accrue to the next period rather than being dropped.
- (d) Disputes: a rights-holder can trigger targeted audit monitoring. Results feed the next period, never re-weighting a single venue retroactively, to avoid gaming.

**Avoid "pay only significant songs".** It is biased against the tail. Use the shrinkage and long-tail pool instead.

### Gaps
- The Sison–Glaz formula itself and its coverage under overdispersion were not extracted.
- No published reconciliation/hold-back percentages from collecting societies were retrieved. The numbers above are design suggestions.

## 7. Validation (simulation against ground truth, metrics)

### Takeaway
Build a synthetic "digital twin" of Cameroon's venue universe with known true play logs. Simulate sampling, detection, outages, the catalogue gap and live versions, then run the full estimator pipeline and compare against truth. Supplement with a small set of fully ground-truthed venues (human logs or direct mixer feed). Judge estimators primarily by misallocated money (½·Pot·Σ|Ŝ_j − S_j|), plus per-artist error fairness (Gini of absolute errors) and interval coverage.

### Cited Findings
- Royle (2004) validated the N-mixture estimator by simulation, comparing bias and CI coverage against the Carroll–Lombard estimator. Simulation-based bias/coverage checks are standard practice in this literature. — [USGS: Royle 2004](https://pubs.usgs.gov/publication/5224798)
- MacKenzie et al. (2002) used simulation to show the occupancy estimator is generally unbiased for p > 0.3. — [USGS pubs](https://pubs.usgs.gov/publication/5224176)
- Nielsen evaluates its panel+big-data method by average relative error against reference audiences. — [Nielsen 2025](https://www.nielsen.com/insights/2025/improve-data-quality-big-data-people-panels)

### Inferences

**Simulator components.**
1. Venue universe: N venues by stratum, with lognormal activity.
2. Song popularity: Zipf/power-law, with genre × city × hour-block interactions and venue-specific playlist affinity (to produce realistic ρ).
3. Plays: Poisson per venue-hour.
4. Detection: logistic in noise, genre, duration, device.
5. Outages: Markov on/off process correlated by city and evening.
6. Catalogue gap: X% of plays unregistered.
7. Live/DJ fraction by venue type.
8. False positives at rate f by confidence band.

**Metrics per run (repeat R = 500 Monte Carlo replicates).**
- Misallocated FCFA = ½·Pot·Σ_j|Ŝ_j − S_j|, the headline number for judges. This is the total-variation distance × pot.
- Relative share error per song |Ŝ_j − S_j|/S_j. Report the median and 90th percentile by popularity decile. MAPE is unstable for tiny S_j, so use weighted MAPE (Σ|err|/ΣS) or sMAPE.
- Rank: Spearman ρ and Kendall τ overall, plus top-20 precision/recall.
- Fairness: Gini coefficient of |payment error| across artists, and signed bias by genre, region and language (systematic under-payment of a genre is the regulator's concern).
- Interval coverage: fraction of songs whose nominal 90% interval contains the truth (target ~0.9).
- Bias vs variance decomposition across replicates.

**Ablations to show judges.**
- Naive raw counts.
- With weights.
- With effort correction.
- With detection correction (genre-level).
- With shrinkage.

Each step's misallocated-FCFA reduction makes the value of the method concrete.

**Field validation.** In 5–10 venues, obtain a full ground-truth log (DJ software export, staff log, or mixer line-in recording). Compare phone-derived counts to estimate real p and f, and check the simulator's assumptions.

### Gaps
- No published validation benchmarks for venue audio-fingerprinting royalty systems were found.
- No source was found that uses the Gini of allocation errors; this is a proposed metric.
