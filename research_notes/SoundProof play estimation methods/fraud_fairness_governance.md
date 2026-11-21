# Manipulation resistance, fairness, and legal/governance acceptability of sample-based, sensor-measured royalty distribution (SoundProof / SONACAM)

Research date: 2026-11-21. About 22 tool calls. Items marked **[unverified this session]** come from background knowledge or from a search snippet whose underlying page was not opened. Treat them as leads, not confirmed facts.

## Manipulation threats and real cases (streaming fraud, payola, venue gaming, device tampering, Sybil)

### Takeaway
Fraud follows the money wherever a play count turns into royalties. The clearest recent cases are bot farms streaming mass-produced AI tracks (Michael Smith, guilty plea in March 2026) and airplay traded for something of value (the FCC's 2025 iHeartMedia payola probe). A sampled bar panel shrinks the attack surface to the venues and devices in the panel. That makes each panel member, and each phone, a high-value target: whoever knows which bars are monitored can loop a song there at little cost.

### Cited Findings
- **Michael Smith case.** Smith, a North Carolina man, pleaded guilty on March 19, 2026 in the Southern District of New York to one count of conspiracy to commit wire fraud. He used AI to generate hundreds of thousands of tracks and had bots stream them billions of times. He agreed to forfeit $8,091,843.64 and faces a maximum of 5 years; the original charges carried a combined 60 years. Reports describe it as the first US federal criminal case of AI-assisted streaming fraud. — [US Attorney SDNY press release](https://justice.gov/usao-sdny/pr/north-carolina-man-pleads-guilty-music-streaming-fraud-aided-artificial-intelligence-0); [Music Business Worldwide](https://musicbusinessworldwide.com/streaming-fraud-man-who-pocketed-8m-using-hundreds-of-thousands-of-ai-songs-streamed-billions-of-times-by-bots-pleads-guilty)
- **Smith case details.** The scheme reportedly ran 2017–2024 and used about 1,000 bot accounts. The bots spread streams thinly across a huge catalogue so that no single track looked anomalous. — [TechSpot](https://www.techspot.com/news/111787-man-who-used-1000-bots-stream-ai-songs.html); [Help Net Security](https://www.helpnetsecurity.com/2026/03/20/ai-music-streaming-fraud-guilty-plea/). The "spread thinly" mechanism comes from the 2024 indictment as summarised by press **[indictment not opened this session]**. Sources disagree on his age (54 vs 52) and on total proceeds ($8M vs about $10M). A sentencing date of July 29 is reported only by [Vice](https://www.vice.com/en/article/north-carolina-man-must-pay-back-the-millions-he-acquired-in-first-ever-streaming-fraud-case/) and is unconfirmed.
- **Deezer fraud statistics.** Up to 85% of streams on fully AI-generated tracks were fraudulent in 2025, depending on the month. Fraud across the whole catalogue was about 8% of streams. AI music accounted for 1–3% of total streams. By April 2026, AI tracks made up 44% of new daily uploads (about 75,000 per day). These are Deezer's own figures, not independently audited. — [TechCrunch, Apr 2026](https://www.techcrunch.com/2026/04/20/deezer-says-44-of-songs-uploaded-to-its-platform-daily-are-ai-generated/); [Music Week](https://www.musicweek.com/public_html/index.php/digital/read/inside-the-industry-s-streaming-fraud-response-as-deezer-now-logs-75-000-ai-based-tracks-per-day/093982)
- **Deezer, earlier figure and response.** In September 2025 the fraud share on AI tracks was "up to 70%." Deezer demonetises streams it detects as fraudulent and keeps AI-tagged tracks out of algorithmic and editorial recommendations. — [Digital Music News, Sep 2025](https://www.digitalmusicnews.com/2025/09/11/deezer-ai-tracks-daily-stat/); [Vice](https://www.vice.com/en/article/how-deezer-is-fighting-fraudulent-streams-as-ai-music-uploads-reach-millions-per-month/)
- **Payola, US 2025–26.** On January 30, 2025, Senator Blackburn flagged a practice in which stations trade extra airplay for free artist performances at station events. On February 6, 2025 the FCC Enforcement Bureau issued an advisory on "covert manipulation of airplay" and opened an inquiry into iHeartMedia over the iHeartCountry Festival. A consent decree closed the probe without a fine. iHeart admitted the factual findings but not a violation of law, and agreed to disclosure measures. The decree is dated July 8, apparently 2026; the year is inferred. — [FCC DA-26-683A1](https://docs.fcc.gov/public/attachments/DA-26-683A1.pdf); [Digital Music News](https://www.digitalmusicnews.com/2025/02/25/fcc-probes-iheartradio-on-alleged-payola-violations/); [Radio Ink](https://radioink.com/?p=105122)
- **Cameroon structural context: weak, contested data.** The Cameroon Tribune has reported that only CRTV "really" pays copyright among audiovisual media. — [Cameroon Tribune](https://www.cameroon-tribune.cm/article.html/76554/en.html/collecte-du-droit-dauteur-une-assiette-dusagers-elargir)
- **Cameroon: bar levy channel.** The bar/drinks-outlet levy is collected through the Brasseries du Cameroun by withholding it from rebates ("retenue sur les ristournes"). A 2009 decision of the Minister of Culture set the rate and collection terms for bars. The music CMO acted as mandatary for the other CMOs, each of which receives a share under a key. — [Cameroon Tribune](https://cameroon-tribune.cm/article.html/35250/fr.html/details_2); [237online](https://www.237online.com/droit-dauteur-la-collecte-des-fonds-facilite-auprs-des-tenanciers-des-bars/). The article is undated in the snippet and names "Socam," the predecessor of SONACAM.

### Inferences
Threat model for SoundProof, mapped by analogy from the cases above:
- **Panel-targeted looping** is the venue analogue of bot farms. An artist, label, DJ or promoter who learns which bars are sampled pays the owner or DJ to loop tracks there. Every extra play inside the panel is multiplied by the inverse sampling fraction. For example, if 1% of bars are sampled, one bought play is worth about 100 estimated plays.
- **Bar-owner capture.** Owners may be paid, much like payola, to keep the phone near the speaker for certain artists, or to unplug or move it during others.
- **Device tampering and emulation:**
  - playing audio straight into the microphone or line-in, or replaying files with no real audience;
  - running the app on emulators or rooted phones and forging fingerprints or logs;
  - GPS spoofing, which lets one device claim to be in another venue;
  - clock manipulation.
- **Sybil attacks:** fake "venues" or duplicate devices registered to inflate the panel, or to give one physical bar several weights.
- **Radio-stream gaming:**
  - stations airing paid rotations, i.e. payola, at off-peak hours;
  - small or "pirate" stations streaming loops purely to be monitored;
  - a stream URL that differs from what is actually broadcast over the air.
- **Insider and operator threats:** SoundProof staff or SONACAM board members altering weights, panel selection or the fingerprint catalogue. The Merkle log mitigates log edits but not upstream selection bias.
- **Catalogue attacks.** The Smith tactic transfers here: registering near-duplicate or soundalike works, or claiming ownership of popular public-domain or folklore recordings, so that fingerprints match. Folklore is particularly relevant in Cameroon because Law 2000/011 art. 5 vests it in the national cultural heritage.

### Gaps
- I found no published case of fraud against a sampled venue-monitoring panel (BMAT, Soundmouse, PRS background-music sampling, etc.). This should be flagged as a known unknown rather than evidence that it does not happen.
- I did not find the Smith indictment's exact description of the evasion tactics.
- I did not find documented payola or airplay-for-cash cases in Cameroonian or Central African radio, although anecdotal "gombo" practices are widely discussed **[unverified this session]**.

## How streaming fraud detection works, and platform policies

### Takeaway
Platforms combine behavioural anomaly detection, economic deterrents (penalties charged to distributors, minimum thresholds), demonetisation and catalogue tagging. Penalties fall on the party that can control upstream behaviour.

### Cited Findings
- **Spotify penalties.** Since April 1, 2024, Spotify charges labels and distributors a per-track fee when "flagrant" artificial streaming is detected, applied only at very high rates of artificial streaming per track. Flagged streams earn no royalties and are removed from public counts and charts. Tracks may be removed from playlists, and distributors may warn, suspend or remove content. Spotify runs "daily cleaning" of artificial streams and is a member of the Music Fights Fraud Alliance. The fee amount is not published on the pages found. — [Spotify for Artists: artificial streaming](https://artists.spotify.com/en/artificial-streaming); [Spotify help article](https://artists.spotify.com/help/article/third-party-services-that-guarantee-streams)
- **Spotify 2024 royalty changes.** These included a minimum annual stream threshold before a track earns royalties and minimum lengths for "noise" tracks, explicitly framed as anti-gaming. — [Spotify: Modernizing our royalty system](https://artists.spotify.com/en/blog/modernizing-our-royalty-system). The 1,000-stream figure and the noise-track details are from my background knowledge; the page was not opened this session **[unverified this session]**.
- **Deezer** detects and tags fully AI-generated tracks, demonetises fraudulent streams, and excludes AI tracks from recommendations. — [Vice](https://www.vice.com/en/article/how-deezer-is-fighting-fraudulent-streams-as-ai-music-uploads-reach-millions-per-month/); [Routenote](https://routenote.com/blog/deezer-highlights-growing-fraud-streaming-linked-to-ai-music/)
- **The CNM 2021 study (France)** explicitly assessed the effect of each payment model on "the fight against fraud." Under user-centric payment, a fraudster's streams can only redistribute the subscription money of the accounts they control. — [CNM study page](https://cnm.fr/en/studies/impact-of-online-music-streaming-services-adopting-the-ucps/); [detailed report PDF](https://cnm.fr/wp-content/uploads/2021/03/1_CNM_UCPS_Detailed_report_January2021.pdf)

### Inferences
- The Spotify fee is analogous to SONACAM charging or sanctioning the party responsible for anomalous concentration (for example, withholding a flagged rights-holder's share pending review). Design rules must spell this out in advance, so that sanctions are not ad hoc and can withstand court challenge.
- A minimum-threshold rule (no payout below N estimated plays) reduces dust-fraud but hurts the long tail; see the fairness section. Any threshold must be disclosed in the distribution rules.
- Fraud checks commonly used in streaming can be applied to venue sensors:
  - share of a venue's plays going to one artist;
  - repeat-play intervals (identical track every 3–4 minutes);
  - plays outside opening hours;
  - absence of ambient crowd noise, i.e. a sound-level and spectral "liveness" check;
  - correlation between new panel venues and sudden artist spikes.
  The anomaly-detection techniques are standard practice; their applicability here is my inference.

### Gaps
- Platforms do not publish their detection models, and no peer-reviewed description of Spotify's or Deezer's detection pipelines was found this session.

## Defenses for sensor networks (secret/rotating sampling, attestation, corroboration, caps, audits, transparency logs)

### Takeaway
A defensible design combines several layers:
1. **Unpredictability:** secret, randomised and rotating venue and time samples.
2. **Device trust:** hardware-backed attestation and per-observation signatures.
3. **Corroboration and caps:** multiple devices, radio cross-checks, and per-venue and per-artist influence caps.
4. **Accountability:** an append-only, publicly auditable log in the style of Certificate Transparency, plus independent audits.

CISAC's own rule ("statistically valid sample") makes the sampling design itself the thing that must be defended.

### Cited Findings
- **CISAC rule.** CISAC compliance expects distribution "based on actual usage or statistically valid sample" (Professional Rules 18A–18C). — [CISAC Compliance Monitoring](https://www.cisac.org/compliance)
- **PRS for Music hierarchy.** PRS ranks methods as census first ("the preferred basis for all distribution sections"), then sampling, then analogy where data is insufficient. It notes that sampling is used where full data collection is too costly. — [PRS distribution policy: cycles and concepts (Oct 2022)](https://www.prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/prs-distribution-policy/2prspolicydistributioncyclesandconceptsoct-22.pdf). The pages on sampling were seen as snippets; full policy not read.
- **Play Integrity verdict tiers.** Google's Play Integrity verdicts use labels such as MEETS_DEVICE_INTEGRITY and MEETS_STRONG_INTEGRITY. STRONG requires hardware-backed signals and a recent security patch. From May 2025, Android 13+ requires a security update within the last year, which cut strong verdicts by about 14.5%. — [Android Developers: improved verdicts](https://developer.android.com/google/play/integrity/improvements); [Android Developers Blog, Oct 2025](https://android-developers.googleblog.com/2025/10/stronger-threat-detection-simpler.html)
- **Play Integrity and key attestation.** Platform key attestation gives a strong signal of hardware-backed boot integrity. Developers verifying attestation themselves must handle an Android attestation root certificate rotation in February 2026. Google's documentation is inconsistent about which label is the top tier on Android 13+. — [Android Developers: improved verdicts](https://developer.android.com/google/play/integrity/improvements)
- **Certificate Transparency.** CT (RFC 6962, 2013; obsoleted by RFC 9162, CT v2.0, Dec 2021) is the model accountability design:
  - an append-only Merkle-tree log;
  - signed tree heads;
  - inclusion proofs and consistency proofs;
  - independent monitors and auditors who detect misissuance without trusting the log operator.
  — [RFC 6962](https://www.rfc-editor.org/rfc/rfc6962); [RFC 9162](https://www.rfc-editor.org/rfc/rfc9162) **[RFC text not re-fetched this session; facts are standard]**

### Inferences
Recommended layered design, synthesised from the above:
1. **Sampling secrecy and rotation.**
   - Draw the venue panel and the recording windows by stratified random sampling (region × venue size × urban/rural).
   - Rotate a fraction (e.g. a quarter) of the panel each period.
   - Use a commit-reveal scheme: publish a hash of the selection seed before the period and reveal the seed after it. The selection is then secret while it matters, and verifiable afterwards (provably not cherry-picked).
   - Add unannounced "shadow" devices and mystery-shopper visits as audit strata.
2. **Device trust.**
   - Hardware-backed key attestation at enrolment.
   - A per-device signing key held in StrongBox or TEE signs each observation: fingerprint ID, timestamp, coarse location, sound-level and liveness features.
   - Require MEETS_DEVICE_INTEGRITY or better; quarantine devices that fail.
   - Many cheap phones common in Cameroon may lack STRONG integrity or recent patches. Strict gating could exclude much of the low-cost hardware and bias the panel. Consider SoundProof-issued, managed phones (fixed fleet, kiosk mode) for the panel.
3. **Signed observations into the Merkle log.**
   - Run CT-style: periodically publish signed tree heads, e.g. to SONACAM, MINAC and an artists' association.
   - Give any rights-holder an inclusion proof for "their" plays.
   - Let auditors verify consistency, i.e. that no rows were silently deleted.
   - The log proves integrity *after* capture, not the truth *of* capture. Attestation, corroboration and audits cover capture.
4. **Corroboration.** Where possible put two devices in high-weight venues, cross-check radio-stream detections against off-air recordings, and check consistency between bar plays and radio and streaming popularity. Large divergences go to review, not automatic payment.
5. **Caps and robust estimators.**
   - Cap the share of any single artist within one venue-day (e.g. at the 99th percentile of historical behaviour).
   - Winsorise per-venue totals.
   - Cap any single venue's contribution to an artist's estimate.
   - Use median-of-means or trimmed estimators across strata.
   - These limit the payoff of looping at the cost of a small, disclosed bias.
6. **Audits.** Commission an annual independent statistical audit (design, weights, confidence intervals) and an annual security audit. Publish confidence intervals per artist, or at least per payout bracket.

### Gaps
- No published security evaluation was found for Android attestation on the low-end devices common in Central Africa.
- No 2025–26 public bypass research on Play Integrity was found; this needs a dedicated security review.
- No source was found documenting the sampling-error tolerances CMOs accept (e.g. PRS's target precision).

## Fairness: user-centric vs pro-rata, long tail, minimum payments, bias, ethics, disputes

### Takeaway
The streaming evidence suggests that changing the allocation formula moves less money than expected; the CNM found only small long-tail gains from user-centric payment. For a sampled bar panel, the bigger fairness risks are:
- **coverage bias:** which regions, languages and genres the panel can hear;
- **small-count noise:** artists with few plays get zero or wildly variable estimates.

Both need explicit, published mitigations and an appeals path.

### Cited Findings
- **CNM 2021, long tail.** The CNM/Deloitte study found that user-centric payment (UCPS) would have a limited impact. Artists beyond the 10,000th rank would gain on average less than €10. The lowest tier's share rose only from 7.7% (pro-rata, "MCPS") to 8.1% (UCPS). — [Music in Africa summary](https://www.musicinafrica.net/node/150521); [CNM](https://cnm.fr/en/studies/impact-of-online-music-streaming-services-adopting-the-ucps/)
- **CNM 2021, winners and reactions.** Gains concentrated mid-tier: about €9,000 per artist ranked 11–100 and about €2,500 for ranks 101–1,000. The top 10 lose. The CNM president said neither model solves artist remuneration; ADAMI called it "an expected disappointment." — [Hypebot](https://www.hypebot.com/hypebot/2021/02/user-centric-royalties-will-not-lead-to-a-significant-shift-in-streaming-payouts-new-study.html)
- **Moreau et al. (2024).** An academic paper found that user-centric payment would cause more significant revenue reallocation than an "artist-centric" alternative. — [RePEc/HAL hal-04679366](https://ideas.repec.org/p/hal/journl/hal-04679366.html) (abstract only seen in snippet)
- **SONACAM's flat-payout history.** SONACAM payments have been very small and partly flat. A September 30, 2025 distribution reportedly paid 27,000 FCFA per artist, with payments from October 7, 2025 via Express Union. — [Cameroun24](https://cameroun24.net/article/70951-sonacam-debut-des-paiements-de-la-r-partition-du-30-septembre-chaque-artiste-recevra-27-000-fcfa.html). The headline is the only content seen; whether 27,000 was a flat amount for all artists or a floor is **unverified**.
- **2018 first distribution.** SONACAM's first distribution in 2018 split 154M FCFA among about 1,500 artists. Its vice-president said the criteria included artists' "degré de productivité." — [camer.be](https://camer.be/amp/culture/musique/cameroun-droits-dauteurs-154-millions-pour-les-artistes-cameroon.html)
- **Disputes and litigation.** In 2021 a distribution was contested over its low amounts, and about 600 artists could not collect their share. The Association dynamique des artistes nouvelle génération (Adang) obtained a court decision against MINAC, the Commission de contrôle des organismes de gestion collective, and SONACAM's board, who appealed to the Cour d'appel du Centre. — [Cameroon Tribune: "la nouvelle affaire Sonacam"](https://cameroon-tribune.cm/article.html/49423/fr.html/details_2); [Cameroon Tribune: "Incertitudes persistantes"](https://cameroon-tribune.cm/articles/12112/fr/details_2). Dates and substance were not read in full **[partially verified]**.
- **Commentary, Oct 2026.** The Cameroon Tribune (Oct 30, 2026) argued that Cameroon's collective management problems would be solved if royalties were distributed "selon des critères clairs, définis à l'avance." It cited SACEM's quarterly cycle as the model. — [Cameroon Tribune, "Meilleure organisation, meilleure répartition"](https://cameroon-tribune.cm/article.html/82152/fr.html/details_2)

### Inferences
- **Pro-rata vs user-centric** largely does not apply to a bar levy: there are no subscriber accounts. The equivalent choice is between pro-rata by estimated plays and a hybrid with a flat or "solidarity" floor. SONACAM's history of flat payments means a pure pro-rata switch will create visible losers, mostly less-played artists. Expect pushback, and phase it in.
- **Coverage and bias risks:**
  - urban Douala/Yaoundé bars over-represented;
  - Anglophone NW/SW regions under-sampled, partly because of the security situation;
  - genres played at home, at funerals or in churches, but rarely in bars, under-captured;
  - local-language and traditional recordings, and live performance, missing from the fingerprint catalogue (fingerprinting only matches registered recordings).
  Mitigations: stratification with published weights, catalogue-completeness drives before launch, and a residual pool for unmatched audio.
- **Small-count noise.** Most artists will have zero sampled plays in a given period. Options:
  - pool several periods (rolling 12-month window);
  - a Bayesian shrinkage prior using radio data;
  - a published minimum-payment floor funded by a fixed small percentage;
  - a "non-identified" reserve held for claims (cf. CISAC rules on unidentified uses).
- **Ethics of algorithmic allocation.** Publish the method, the code or at least its specification, and the confidence intervals. Make sure each artist can see their own detections, via Merkle inclusion proofs. Keep a human appeals board. This matches the "clear criteria defined in advance" demand voiced in the Cameroonian press.
- **Disputes and appeals.**
  - a time-limited claims window after each distribution, e.g. 3–6 months;
  - an evidence standard (artist-supplied logs, radio playlists);
  - escalation to the Ministry and the Commission de contrôle;
  - then the statutory arbitration commission or the courts.
  The Adang litigation shows courts will be used.

### Gaps
- No academic study of bias in fingerprint or sampled public-performance monitoring in African markets was found.
- The SONACAM distribution rules (règlement de répartition) are not public online; I found no document stating SONACAM's current distribution key or weights between radio, TV and bars.

## Legal/governance standards (CISAC, WIPO, EU CRM Directive, OAPI/Cameroon law, acceptability of sampling)

### Takeaway
The international norms accept sampling in principle, provided it is *statistically valid*, transparent, rule-based and auditable:
- CISAC: "actual usage or statistically valid sample";
- PRS: census, then sample, then analogy;
- EU Directive 2014/26/EU: accuracy, timeliness, transparency and complaints, by analogy.

Cameroonian law sets the governance frame: MINAC approval, prior communication of distribution-rule changes, and users' duty to supply "exact programmes." It says nothing specific on sampling methods.

### Cited Findings
- **CISAC distribution and transparency rules.**
  - Distribution must be "based on actual usage or statistically valid sample."
  - "Equal diligence and fairness" toward sister societies.
  - Distributions at least annually.
  - Procedures for unidentified uses and insufficient documentation.
  - Annual submissions: compliance statement, audited accounts, distribution rules if modified.
  - Sanctions up to expulsion (Arts. 20–24).
  — [CISAC Compliance](https://www.cisac.org/compliance); [CISAC Professional Rules](https://www.cisac.org/services/business-and-governance/professional-rules)
- **WIPO Good Practice Toolkit for CMOs.** The Toolkit had a 2018 first version, a 2021 update and a January 2025 version reflecting the 2024 consultations. WIPO says it is not a normative document. The 2021 edition's Good Practice Tool 9 covers transparency in revenue distribution. — [WIPO 2025 update news](https://www.wipo.int/en/web/copyright/w/news/2025/update-of-wipo-good-practice-toolkit-for-collective-management-organizations); [WIPO 2021 news](https://www.wipo.int/en/web/copyright/w/news/2021/news_0003); [EIFL summary](https://eifl.net/node/3472). I did not read the specific Toolkit text on sampling.
- **EU Directive 2014/26/EU.** Relevant provisions:
  - Art. 13: distribute regularly, diligently and accurately, no later than nine months from the end of the financial year of collection.
  - Art. 12: deductions must be reasonable and justified.
  - Art. 18: information to rightholders at least once a year.
  - Art. 22: annual transparency report.
  - Art. 33: complaints procedures.
  - Arts. 34–35: ADR and dispute resolution.
  - Art. 36: monitoring by competent authorities.
  — [EUR-Lex CELEX 32014L0026](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014L0026). **[EUR-Lex fetch failed this session; article content from background knowledge. Verify wording before quoting.]** Not binding in Cameroon; use as a benchmark.
- **Cameroon Law No. 2000/011 of 19 December 2000** (text via OAPI):
  - Art. 61: remuneration for public use of phonograms is paid by commercial users, based on operating revenue or, failing that, a lump sum, and "répartie par moitié entre les artistes-interprètes et les producteurs de phonogrammes."
  - Art. 62: the tariff is set by the CMO in consultation with users, or by an arbitration commission.
  - Art. 63(1): users must supply the CMO with "les programmes exacts des utilisations auxquelles elles procèdent et tous les éléments documentaires indispensables à la répartition des droits."
  - Art. 75(2): one CMO per category.
  - Art. 77(2): affiliation gives the CMO a mandate to collect and distribute.
  - Art. 78(2): CMOs use revenue per a schedule in statutes approved by the Minister of Culture.
  - Art. 79: CMOs must communicate to the Minister their annual accounts and changes to "règles de perception et de répartition des droits, un mois au moins avant leur examen par l'assemblée générale." The Minister may inspect "sur pièces et sur place."
  - Art. 5: folklore exploitation fees go to a cultural-policy support account.
  — [OAPI-hosted text of Loi 2000/011](https://oapi.int/wp-content/uploads/2025/04/Loi_19decembre_2000Cameroun.pdf). Text extracted and read this session.
- **Decree No. 2001/956/PM of 1 November 2001** (implementing Law 2000/011):
  - one CMO per category; B = music (Art. 18);
  - no collective management without MINAC approval (Art. 19); approval lasts five years (Art. 22(1));
  - MINAC orders rectification within one month on breach; suspension up to 30 days; withdrawal only for repeat offence, by reasoned decision (Art. 22);
  - the arbitration commission is chaired by a judge appointed by the President of the Supreme Court and includes CMO and user representatives (Arts. 11–12).
  The decree contains **no distribution rules or deduction caps**. — [WIPO Lex, Decree 2001/956/PM (EN)](https://www.wipo.int/wipolex/en/text/491116); [WIPO Lex record](https://www.wipo.int/wipolex/fr/legislation/details/7757)
- **Oversight body.** A Commission de contrôle des organismes de gestion collective (CCOGC), chaired by Théophile Eyango, oversees the CMOs. Five CMOs operate: SONACAM, SCAAP, SOCILADRA, SOCADAP, SCDV. In 2023, distributions ran four times a year. — [Cameroon Tribune](https://cameroon-tribune.cm/article.html/62178/fr.html/details_2)
- **SONACAM governance tension.** Ateh Bazor was re-elected to head SONACAM, reportedly with 45 opposition votes signalling continued contest. — [AllAfrica, May 2026](https://fr.allafrica.com/stories/202605010511.html); [camer.be](https://www.camer.be/amp/93187/1:6/cameroun-sonacam-ateh-bazor-reelu-le-defi-des-droits-d-auteur-commence-maintenant-cameroon.html)
- **OAPI Bangui Agreement.** Annex VII (literary and artistic property, 2015 revision) frames copyright across the 17 member states, Cameroon included. — **[not fetched this session; general knowledge]**

### Inferences
- **Legal basis for sampling.** Art. 63 puts the duty to provide *exact programmes* on users. Bars in practice provide none, so SONACAM's current alternative is presumably an unspecified or flat key. A documented, statistically valid sensor sample is arguably *closer* to the statute's aim, distribution according to actual use, than flat keys. The argument is stronger if the sample method is written into the distribution rules adopted by the General Assembly and communicated to MINAC under Art. 79(1)(b).
- **Court defensibility hinges on four things:**
  1. rules adopted in advance by the competent body;
  2. a published methodology with error bounds;
  3. independent audit;
  4. a working complaints process.
  The Adang litigation shows Cameroonian artists litigate distribution and governance. A transparent log plus inclusion proofs is useful evidence.
- **Split beneficiaries.** The 50/50 performers/producers split in Art. 61 concerns neighbouring rights. Whether the beer-crate levy pays authors (SONACAM), neighbouring-right holders, or both under a key across CMOs (as the Brasseries mechanism suggests) determines what SoundProof must measure: works vs recordings vs performers. Fingerprinting identifies *recordings*, which then map to works and performers through metadata. The metadata quality (CIS-Net/IPI per CISAC Rule 17) is itself a fairness and fraud vector.

### Gaps
- **Regulator and court statements on sampling.** I found no court decision or regulator statement anywhere specifically ruling on the validity of sample-based performance distribution. This needs further search, e.g. US BMI/ASCAP consent-decree survey methods, UK Copyright Tribunal, Brazil's ECAD distribution regulation ([ECAD Distribution Regulation PDF](https://www4.ecad.org.br/wp-content/uploads/2022/11/Distribution-Regulation.pdf), not read).
- **Cameroonian instruments not found.** I did not locate:
  - the 2009 MINAC decision on bar tariffs;
  - the CCOGC's founding texts (circa 2014–2017 decrees **[unverified]**);
  - any 2024–26 text on SONACAM distribution keys or the beer-crate levy's amount and split. The 100M FCFA figure in the brief could not be verified.
- **WIPO Toolkit text.** The text on distribution and sampling (2021/2025) was not read.
- **Bangui Annex VII.** The provisions on collective management were not checked.

## Privacy law for in-venue audio sensing (Cameroon 2024 law, GDPR/EDPB analogues, best practice)

### Takeaway
A 2024 law exists: Cameroon Law No. 2024/017 of 23 December 2024 on personal data protection, which became enforceable on 23 June 2026 after an 18-month transition. It requires consent (with exceptions), prior authorisation of processing, prior authorisation of cross-border transfers, and creates an independent authority. Ambient audio in bars can capture bystanders' conversations. On-device fingerprinting with no audio retention and no speech content is the defensible design, but it still needs a registration or authorisation analysis.

### Cited Findings
- **Law 2024/017: adoption and authority.** The National Assembly adopted the law on 26 November 2024 and it was promulgated on 23 December 2024. It creates an independent Autorité de protection des données à caractère personnel. — [Digital Business Africa](https://www.digitalbusiness.africa/cameroun-lassemblee-nationale-adopte-le-projet-de-loi-relatif-a-la-protection-des-donnees-a-caractere-personnel/); [Digital Business Africa: authority created](https://www.digitalbusiness.africa/cameroun-lautorite-de-protection-des-donnees-a-caractere-personnel-creee/)
- **Law 2024/017: entry into force.** It entered into force or became enforceable on 23 June 2026 after an 18-month moratorium (Art. 73 per commentary). — [Droit Médias & Finance](https://droitmediasfinance.com/index.php/actualites/droit-tech-fintech/1297-cameroun-la-loi-sur-la-protection-des-donnees-a-caractere-personnel-entre-en-vigueur-ce-23-juin-2026); [CIO Mag](https://cio-mag.com/protection-des-donnees-au-cameroun-la-course-contre-la-montre-avant-juin-2026/)
- **Law 2024/017: provisions per practitioner commentary.**
  - It applies to data of people established, residing or *in transit* in Cameroon.
  - Personal data includes identifiers and IP addresses (Art. 5).
  - Processing on Cameroonian territory requires prior authorisation (Art. 19).
  - Transfers abroad require prior authorisation (Art. 32, numbering uncertain).
  - Controllers must keep registers and policies; a DPO is recommended for large-scale processing.
  — [CIO Mag commentary on Law of 23 Dec 2024](https://cio-mag.com/?p=59861)
- **Law 2024/017: consent and fines.** Processing without prior consent is prohibited, subject to exceptions not detailed in the source seen. Fines of up to 1 billion FCFA were reported for violations. — [237online](https://www.237online.com/cameroun-la-revolution-digitale-debarque-avec-une-loi-musclee-sur-les-donnees-personnelles/); [Digital Business Africa: fines](https://www.digitalbusiness.africa/cameroun-bientot-des-peines-damende-d-un-milliard-de-francs-cfa-pour-violation-des-donnees-personnelles/). I did not read the official Journal Officiel text; article numbers come from secondary sources.
- **EDPB on smart glasses (Oct 2026).** The statement warns that microphones can record "background conversations" of non-participants. It calls for clear signals when recording (e.g. LEDs), on-device processing where possible, obscuring bystanders' identities, and limited retention. — [EDPB statement on smart glasses](https://www.edpb.europa.eu/system/files/2026-11/20261013_edpb_012026_statement_smart-glasses_en.pdf)
- **EDPB Virtual Voice Assistant Guidelines 02/2021.** Data should be stored no longer than necessary. — [EDPB Guidelines 02/2021 v2.0](https://www.edpb.europa.eu:443/system/files/2021-07/edpb_guidelines_202102_on_vva_v2.0_adopted_en.pdf)
- No EDPB guidance specific to retail or venue ambient-audio analytics was found.

### Inferences
- **Is the data personal?** A fingerprint of a commercial recording plus a venue ID and timestamp is arguably not personal data. However, raw audio of patrons is, and so are device or operator identifiers and precise location tied to an identifiable bar owner. The data protection impact depends on never letting raw audio leave the phone.
- **Best-practice design:**
  - compute spectral-peak fingerprints on-device over short windows;
  - discard the audio buffer immediately and never store or upload audio;
  - use fingerprints that cannot be inverted into intelligible speech (landmark hashes are lossy);
  - do not run speech recognition;
  - put a visible sticker or notice in the bar, as the EDPB's signalling principle suggests;
  - sign a contract with the venue owner as a counterpart;
  - limit retention of observation logs;
  - minimise data in the public Merkle log: publish hashes or aggregates, not venue identities, if venue secrecy is a security goal.
- **Two tensions to resolve.** CT-style public logs conflict with panel secrecy; resolve this with commit-reveal and delayed disclosure. Logs or keys hosted on foreign cloud services may trigger Law 2024/017's prior-authorisation rule for transfers.
- **Compliance steps.** Seek authorisation or registration from the new authority before deployment. Conduct an impact assessment as good practice even if the law does not name one. Appoint a DPO.

### Gaps
- The official text of Law 2024/017 was not read. Not verified: whether it requires DPIAs, which legal bases besides consent exist (legitimate interest? statutory mission?), how it treats audio/biometric data, and whether the authority's implementing decree has been issued and is operational as of Nov 2026.
- No Cameroonian or OAPI-region guidance on ambient audio sensing was found.
- CNIL or ICO guidance on audio analytics in commercial premises was not searched this session; suggested follow-up.
