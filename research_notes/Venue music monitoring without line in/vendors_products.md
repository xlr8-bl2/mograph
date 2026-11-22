# Commercial vendors and products for venue and broadcast music monitoring (focus: microphone-based ambient recognition)

Scope note: about 17 search and fetch calls. Several vendor pages (PRS M Magazine articles, the OneMusic NZ Audoo PDF) came back as titles only or unreadable binaries, so some details come from search-result summaries rather than full-text reads. These are marked "(snippet)". Vendor accuracy figures are **unverified marketing or CMO self-reports** unless stated otherwise.

## 1. How do venue devices capture audio (microphone vs line-in vs DJ-software integration), and what hardware do they use?

### Takeaway
Capture methods split three ways:
- **Line-in to the DJ booth or mixer output:** the dominant method in nightclubs (Yacast for GEMA, and most probably DJ Monitor).
- **Ambient microphone boxes:** used for bars, cafés, shops and other background-music premises (Audoo, BMAT's box, GoCreate's "black boxes").
- **Metadata or play-log feeds:** these come from DJ software (Pioneer KUVO/rekordbox, now closed) or from background-music streaming services (Soundtrack to PRS). They need no audio recognition at all.

Audoo is the clearest commercial precedent for SoundProof's microphone-only, Shazam-style box.

### Cited Findings
**Yacast (France)**
- The GEMA discotheque monitoring box "taps the output signal from the DJ booth", which is line-in, not a microphone. Audio is matched against an acoustic fingerprint database, and unrecognised works go to manual identification by music specialists. — [GEMA Discotheque monitoring](https://www.gema.de/en/music-creators/monitoring/discotheque-monitoring) (snippet)
- GEMA has used Yacast since 1 January 2015, replacing MediaControl. — [LiveKomm](https://www.livemusikkommission.de/gema-entwickelt-mit-hilfe-der-livekomm-diskotheken-monitoring-weiter-yacast-wird-neuer-dienstleister/) (snippet)
- Yacast has also monitored French clubs for SACEM for more than 10 years, and works with SUISA in Switzerland. — [LiveKomm](https://www.livemusikkommission.de/gema-entwickelt-mit-hilfe-der-livekomm-diskotheken-monitoring-weiter-yacast-wird-neuer-dienstleister/) (snippet)
- Monitoring is sample-based: about 120 German discotheques, one hour each per week. — [GEMA](https://www.gema.de/en/music-creators/monitoring/discotheque-monitoring); [netzpolitik.org 2015](https://netzpolitik.org/2015/black-box-discotheken-monitoring-gema-kooperiert-mit-yacast-gti-entwickelt-track-genaues-system/) (snippet)

**BMAT (Spain)**
- BMAT installs "a small identifying device in the venue" or gives venues its mobile app, Patti. Recorded audio is sent "to our servers to analyse", so recognition happens in the cloud, not on the device. The FAQ does not say whether capture is by microphone or line-in. — [BMAT FAQ](https://www.bmat.com/faq/)
- The device is described as "router-sized". BMAT's fingerprinting is claimed to identify tracks with altered pitch or speed. — [BMAT – Razzmatazz club monitoring](https://www.bmat.com/lluis-torrents-music-monitoring-clubs/) (snippet)

**Audoo (UK)**
- The Audio Meter is a plug-in ambient-microphone device installed in cafés, bars, salons, restaurants and shops across Great Britain for PRS for Music and PPL. — [MusicTech](https://musictech.com/news/industry/prs-for-music-ppl-audoo-meters/); [PPL press release](https://www.ppluk.com/about-us/news/prs-for-music-ppl-partner-with-music-tech-company-audoo-to-enhance-public-performance-royalty-distributions/); [RadioToday Aug 2023](https://radiotoday.co.uk/2023/08/prs-for-music-and-ppl-partner-with-audoo-to-install-listening-devices/)
- How it works, per the vendor:
  - The device first separates music from background noise.
  - It then fingerprints the music on the device and reports plays in near real time.
  - The founder says the design captures room reflections, which he claims improves identification over Shazam-like tools.
  
  — [MusicTech](https://musictech.com/news/industry/prs-for-music-ppl-audoo-meters/); [Audoo FAQs](https://audoo.com/resources/faqs) (snippet)
- Audoo also markets a separate "DJ Recognition" product. — [Audoo DJ](https://audoo.com/technology/dj)

**DJ Monitor (Netherlands)**
- Used by PRS and PPL in UK nightclubs and festivals since the 2017–2018 pilot (Ministry of Sound, Fabric, Deltic Group). — [PRS press 2018](https://prsformusic.com/press/2018/prs-for-music-and-ppl-extend-music-recognition-technology-to-nightclubs); [PPL "Pilot to policy"](https://www.ppluk.com/about-us/news/pilot-policy/)
- SACEM selected DJ Monitor after a competitive test in France. — [Record of the Day](https://www.recordoftheday.com/news-and-press/sacem-partners-with-dj-monitor-to-enhance-identification-of-electronic-music-works) (snippet)
- The capture method (line-in vs microphone) could not be confirmed from a full-text source; see Gaps.

**Pioneer DJ KUVO (Japan) – metadata, not recognition**
- KUVO was a hardware box in the club that uploaded track and artist data from rekordbox-linked CDJs. — [Digital Music News 2014](https://www.digitalmusicnews.com/2014/10/24/pioneer-dj-kuvo/)
- It did no audio fingerprinting. The data was "entirely dependent on what's in the ID3 tag … or additional data the DJ has inputted into rekordbox". — [DJ Mag](https://djmag.com/longreads/producers-are-losing-millions-royalties-every-year-%E2%80%93-heres-what-you-can-do-about-it) (snippet)
- BMAT distributed KUVO data to CMOs. As of December 2018, 14 PROs in 11 territories were receiving it. — [Pioneer DJ news 2019](https://www.pioneerdj.com/en/news/2019/kuvo-and-bmat-partnership/) (snippet)
- KUVO closed in September 2022, and its functions were removed from rekordbox from version 6.6.4. — [rekordbox notice](https://rekordbox.com/en/2022/06/kuvo-function-terminated/)

**Soundtrack (Soundtrack Your Brand) – play-log feed**
- PRS will receive play-by-play data from more than 7,000 UK businesses using Soundtrack's background-music service. PRS will use it for public-performance distributions from December 2026, replacing proxy data. No microphone is involved. — [Music Ally, 28 Oct 2026](https://musically.com/2026/10/28/prs-for-music-taps-soundtrack-for-better-public-performance-data/); [Digital Music News](https://www.digitalmusicnews.com/2026/10/27/prs-proxy-data-shift/)
- PRS reportedly gets data from "several background music suppliers". No PPL involvement was mentioned. — [Neon Music](https://neonmusic.co.uk/prs-soundtrack-uk-business-royalties) (secondary)

**ACRCloud (China/Germany)**
- Offers microphone-capture SDKs (Unity3D, Windows 10, iOS, Android). — [GitHub acrcloud_sdk_unity3d](https://github.com/acrcloud/acrcloud_sdk_unity3d); [universal_windows10_sdk](https://github.com/acrcloud/universal_windows10_sdk)
- Offers an offline or local recognition mode, where an offline database is bundled into the app. — [acrcloud_sdk_ios](https://github.com/acrcloud/acrcloud_sdk_ios)
- Offers a Linux local broadcast-monitoring server written in Python 3 that writes results to MySQL. — [acrcloud_local_monitor](https://github.com/acrcloud/acrcloud_local_monitor)
- Its product lines include Broadcast Monitoring, Music Reporting and Offline Recognition. — [ACRCloud](https://www.acrcloud.com/)

**Vollou (Belgium)**
- An app and recognition technology listed as one of Sabam's recognised MRTs for its "Nightlife" product. It also captured DJ sets at Ultra Europe 2025. This is vendor marketing. — [Vollou](https://www.vollou.com/)

**GoCreate Africa "Go Monitor" (Nigeria)**
- Claims to monitor broadcast, DSPs and venues (event venues, hotels, concerts, lounges). — [BusinessDay NG](https://businessday.ng/arts-and-life/article/gocreate-africa-launches-music-monitoring-technology-digital-distribution-service/); [Vanguard 2022](https://www.vanguardngr.com/2022/01/gocreate-africa-unveils-music-monitoring-app-in-ghana/)
- In Ghana, it would deploy "black boxes" in physical spaces such as nightclubs and hotels. — [Music In Africa](https://www.musicinafrica.net/magazine/ghana-musiga-exec-touts-music-recognition-newcomer-solve-data-gap)

**Low-cost hardware**
- PPL's CIO notes that Raspberry Pi or Arduino hardware makes a capture computer possible for "well under £50". — [MBW, Mark Douglas, 12 Jan 2023](https://musicbusinessworldwide.com/there-is-a-real-need-to-look-behind-the-marketing-hype-and-truly-understand-what-is-being-offered)

### Inferences
- In clubs, incumbents chose line-in (Yacast, KUVO). This suggests microphone capture under loud DJ conditions was considered inferior. SoundProof's microphone-only constraint puts it closer to Audoo's background-music use case (bars and cafés) than to the club use case.
- Audoo and BMAT represent the two architectures. Audoo fingerprints on the device and sends fingerprints only. BMAT records audio and sends it to the cloud. For low-bandwidth markets like Cameroon, on-device fingerprinting with fingerprint upload (Audoo-like, or ACRCloud's SDK fingerprint extraction) is the more bandwidth-efficient model.

### Gaps
- DJ Monitor's capture method (line-in from mixer vs microphone) could not be confirmed because the PRS M Magazine pages returned titles only.
- BMAT's box capture method (microphone vs line-in) is not stated on its FAQ.
- No hardware specs (microphone type, SoC, connectivity) were found for Audoo, BMAT or DJ Monitor. The OneMusic NZ Audoo fact sheet was an unreadable PDF.

## 2. On-device vs cloud recognition, and claimed accuracy with any independent verification

### Takeaway
All accuracy numbers found are vendor or CMO self-reports:
- Yacast: 97%.
- DJ Monitor: about 95% in the PRS/PPL pilot, and 80–95% across UK clubs and festivals in 2022.

The PRS/PPL pilot is the closest thing to a published evaluation, but it is not independent. PPL's own CIO publicly said some detection rates in PPL tests were low enough "to question the accuracy of the assertions made about accuracy". No independent, published accuracy test of microphone-based venue detection was found.

### Cited Findings
**Yacast**
- Identification rate of 97% as of June 2017. This is the GEMA-reported figure for line-in club monitoring, and the manual-identification step is included. — [GEMA](https://www.gema.de/en/music-creators/monitoring/discotheque-monitoring) (snippet)

**DJ Monitor**
- The PRS/PPL pilot captured about 31 million seconds of music, with "up to 95%" successfully reported back or matched. — [PPL "Pilot to policy"](https://www.ppluk.com/about-us/news/pilot-policy/)
- DJ Monitor reported an 80–95% positive identification rate across UK festivals and clubs in 2022. — [PRS M Magazine](https://www.prsformusic.com/m-magazine/features/the-future-of-mrt-how-djmonitor-is-transforming-the-payments-landscape) (snippet)
- DJ Monitor claims a database of more than 46 million tracks. — [Record of the Day](https://www.recordoftheday.com/news-and-press/sacem-partners-with-dj-monitor-to-enhance-identification-of-electronic-music-works) (snippet)

**Audoo**
- Claims "levels of accuracy that haven't been seen before" but gives no published number. — [MusicTech](https://musictech.com/news/industry/prs-for-music-ppl-audoo-meters/)

**PPL critique (Mark Douglas, CIO, January 2023)** — [MBW](https://musicbusinessworldwide.com/there-is-a-real-need-to-look-behind-the-marketing-hype-and-truly-understand-what-is-being-offered)
- PPL ran overnight office tests with non-mainstream music. In some cases, detection was low enough to question vendor claims. No figures were given.
- Niche genres often had *lower* match rates. This contradicts the vendor pitch that MRT fixes niche-genre underpayment.
- Tracks that merely sampled a recording were wrongly reported as covers or derivatives.
- MRT has no context. It reports interstitials and adverts that fall outside the licence.
- Vendor rights-ownership data is unreliable. Claims of databases with more than 120 million recordings face conflicting rights claims.
- Vendor claims should be verified through trials and parallel running.
- Proxy data plus market research and sampling "can deliver a high level of accuracy".

**Unverified 21% figure**
- A search summary cited "a study using audio-recognition technology found only 21% of songs played could be accurately identified", in the context of Audoo and Music Venue Trust grassroots venues. — [Making A Scene](https://www.makingascene.org/the-music-industry-has-been-collecting-royalties-but-does-it-know-what-songs-are-actually-being-played/) (snippet)
- The primary source and methodology are unverified. The figure may refer to proxy-data accuracy rather than MRT accuracy. **Do not use without checking.**

**Academic benchmarks (not venue-specific)**
- Landmark fingerprinting on a mixed DJ set reached about 0.89 accuracy but only about 0.65 specificity. The authors concluded that accurate DJ-mix results are hard to get. — [JKU paper](https://www.jku.at/fileadmin/gruppen/173/Research/187_Paper.pdf)
- A SIFT-based spectrogram descriptor reported over 97% accuracy for up to ±20% tempo change and over 95% for ±20% pitch change. These are lab-synthetic transforms. — [EURASIP/Springer](https://link.springer.com/article/10.1186/s13636-015-0050-0)
- The Philips fingerprint reportedly works at about −4 to −7 dB SNR. — [ResearchGate](https://www.researchgate.net/publication/340999846_Potential_barriers_to_music_fingerprinting_algorithms_in_the_presence_of_background_noise)
- Bar and restaurant noise (talking) is non-stationary and degrades recognition. The paper proposes denoising before fingerprinting. — [arXiv 2212.11277](https://arxiv.org/pdf/2212.11277)
- A 2026 preprint reports about 99.6% top-1 accuracy on clean queries, 93.3% with additive noise, and about 24.9% on live versions. Only table excerpts were seen. — [arXiv 2606.17301](https://arxiv.org/pdf/2606.17301)

### Inferences
- The best-documented high accuracy figures (Yacast 97%, DJ Monitor up to 95%) appear to come from line-in or club setups and include human or manual cleanup. They should not be treated as achievable for a microphone box in a noisy Cameroonian bar.
- SoundProof should plan its own parallel-run validation against ground-truth playlists, as PPL recommends, and publish its figures. This would differentiate it from vendors whose numbers are unverified.
- Local repertoire coverage (Makossa, Bikutsi, Afrobeats, Nigerian and Ghanaian music) is likely the binding constraint, more than acoustics. PPL found niche genres had lower match rates largely because of reference-database coverage.

### Gaps
- No independent third-party accuracy test of microphone-based venue devices was found for Audoo, BMAT, DJ Monitor or ACRCloud.
- No published per-condition numbers (crowd noise, overlapping tracks during DJ transitions) from any vendor.
- No vendor whitepaper or patent was read in full for BMAT, Audoo or DJ Monitor.

## 3. Deployment scale, CMO customers, pricing, privacy, and lessons or failures

### Takeaway
**Scale is modest.** BMAT has about 700 club venues, Yacast about 120 sampled German clubs, DJ Monitor a few hundred UK clubs and festivals, and Audoo a UK rollout of undisclosed size. Most CMOs still sample and use proxies.

**Privacy:** the leading microphone vendor's approach is "no audio stored or transmitted; fingerprints only, speech stripped".

**Lessons and failures:**
- KUVO's metadata approach died.
- Line-in is preferred in clubs.
- Rollout cost at hundreds of thousands of venues is the CMO's main worry.
- Niche-genre and reference-database coverage, not acoustics, often caps match rates.

### Cited Findings
**Scale and customers**
- BMAT called its club-monitoring network the largest worldwide, at 700 venues. A testimonial cites 240 establishments in one year for one CMO. — [BMAT Razzmatazz page](https://www.bmat.com/lluis-torrents-music-monitoring-clubs/) (snippet)
- In 2023, BMAT signed an agreement with the International Nightlife Association covering nightclubs, bars and pubs. — [Nightlife International](https://www.nightlifeinternational.org/en/news/item/4561-nightclubs-to-implement-music-recognition-technology-to-help-artists) (snippet)
- BMAT also had an EU Horizon 2020 project. — [CORDIS 739701](https://cordis.europa.eu/project/id/739701/pl)
- Yacast customers are GEMA (about 120 clubs sampled one hour per week), SACEM and SUISA. — [GEMA](https://www.gema.de/en/music-creators/monitoring/discotheque-monitoring) (snippet)
- DJ Monitor customers are PRS and PPL (UK), and SACEM chose it for electronic music. — [PRS 2018](https://prsformusic.com/press/2018/prs-for-music-and-ppl-extend-music-recognition-technology-to-nightclubs); [Record of the Day](https://www.recordoftheday.com/news-and-press/sacem-partners-with-dj-monitor-to-enhance-identification-of-electronic-music-works)
- Audoo customers are PRS and PPL (UK background-music premises, since 2023) and APRA AMCOS / OneMusic NZ (2021–2022 fact-sheet metadata). — [PPL](https://www.ppluk.com/about-us/news/prs-for-music-ppl-partner-with-music-tech-company-audoo-to-enhance-public-performance-royalty-distributions/); [OneMusic NZ PDF](https://assets.onemusicnz.com/onemusicnz/images/PDFs/Fact-Sheets/what-is-the-audoo-audio-meter.pdf)
- A Music Ally piece reports music-monitoring technology rolling out in Japan and Ghana in January 2022. — [Music Ally](https://musically.com/2022/01/10/music-monitoring-technology-rolls-out-in-japan-and-ghana/)
- Audoo and Music Venue Trust began a grassroots-venue assessment in June 2026. Sources disagree on 12 vs 120 venues. — [Digital Music News, 9 Jun 2026](https://www.digitalmusicnews.com/2026/06/09/mvt-audoo-partnership-prs-proxies/); [Pollstar](http://news.pollstar.com/2026/06/11/music-venue-trust-audoo-to-assess-performance-royalty-distribution-in-uk-grassroots-sector/)
- Vollou is recognised as an MRT by Sabam (Belgium) for Nightlife. — [Vollou](https://www.vollou.com/) (vendor)

**Soundmouse / Orfium**
- Orfium acquired Soundmouse (London; cue-sheet and broadcast audio recognition) in January 2023. — [PR Newswire](https://www.prnewswire.com/news-releases/music-tech-firm-orfium-acquires-soundmouse-to-empower-rights-holders-creators-and-media-companies-to-unlock-more-value-across-the-entertainment-landscape-301722288.html); [DMN](https://www.digitalmusicnews.com/2023/01/16/orfium-acquires-soundmouse/)
- BMI agreed on 19 May 2026 to acquire Soundmouse from Orfium, closing mid-2026. It will run as an independent company. Closing was not confirmed in sources found. — [BMI](https://www.bmi.com/news/entry/bmi-announces-agreement-to-acquire-soundmouse-a-global-leader-in-cue-sheet-management); [MBW](https://musicbusinessworldwide.com/bmi-to-acquire-soundmouse-from-orfium-to-build-largest-and-most-comprehensive-global-cue-sheet-database/)
- Soundmouse is broadcast/AV cue-sheet focused. No venue-device product was found.

**Africa**
- GoCreate Africa (Go Monitor) started with MCSN (Nigeria), then MUSIGA (Ghana) in 2022. It announced plans for Rwanda, Zimbabwe, Uganda and South Africa by Q1 2022, but no 2025–2026 confirmation of deployments was found. — [Vanguard](https://www.vanguardngr.com/2022/01/gocreate-africa-unveils-music-monitoring-app-in-ghana/); [Music In Africa](https://www.musicinafrica.net/magazine/ghana-musiga-exec-touts-music-recognition-newcomer-solve-data-gap)
- Qisimah (Ghana) is a radio-monitoring platform built on ACRCloud broadcast monitoring APIs. This is an ACRCloud case study. — [ACRCloud Medium](https://medium.com/acrcloud/qisimah-the-emerging-broadcast-monitoring-service-in-africa-7be998e70b39)
- Playdata (Nigeria) is a cloud broadcast-monitoring service founded by eLDee and Kingsley Offor. — [Wikipedia: Playdata charts](https://en.wikipedia.org/wiki/Playdata_charts)

**Other startups**
- Soundstr (US) built usage reports using Gracenote recognition. Its early study excluded clubs and concert venues. — [American Songwriter](https://americansongwriter.com/meet-soundstr-tech-startup-trying-streamline-performing-rights-royalty-payments/)

**Pricing**
- Razzmatazz (Barcelona) said the BMAT device brings "no problem nor additional cost", which implies the CMO pays. — [BMAT](https://www.bmat.com/lluis-torrents-music-monitoring-clubs/) (snippet)
- PPL's CIO estimates that rollout to tens or hundreds of thousands of venues could cost "millions per year". PPL licenses about 440,000 UK businesses. — [MBW](https://musicbusinessworldwide.com/there-is-a-real-need-to-look-behind-the-marketing-hype-and-truly-understand-what-is-being-offered)
- No per-device or subscription prices were found for any vendor.

**Privacy**
- Audoo: "no audio is ever stored on device, or transmitted from it". It strips foreground and environmental noise such as speech, and sends only fingerprints in real time. These are vendor claims and no audit was found. — [Audoo FAQs](https://audoo.com/resources/faqs); [MusicTech](https://musictech.com/news/industry/prs-for-music-ppl-audoo-meters/)
- BMAT: says recorded audio is sent to its servers. — [BMAT FAQ](https://www.bmat.com/faq/)
- Yacast/GEMA transparency criticism: venue operators cannot see when the boxes record, and the list of monitored clubs is not public. — [netzpolitik.org](https://netzpolitik.org/2015/black-box-discotheken-monitoring-gema-kooperiert-mit-yacast-gti-entwickelt-track-genaues-system/); [iRights](https://irights.info/artikel/gema-diskotheken-monitoring-remix-sampling/26911) (snippet)

**Lessons and failures**
- KUVO relied on DJ-entered ID3 metadata with no fingerprinting, and the service was shut down in September 2022. — [rekordbox](https://rekordbox.com/en/2022/06/kuvo-function-terminated/); [DJ Mag](https://djmag.com/longreads/producers-are-losing-millions-royalties-every-year-%E2%80%93-heres-what-you-can-do-about-it)
- PPL's Russell Chant said mixing and mashups in DJ sets make recognition harder. — [PRS M Magazine "From decks to data"](https://www.prsformusic.com/m-magazine/features/music-recognition-technology-mrt-djs-from-decks-to-data) (snippet)
- Remix and sampling culture remained an unsolved problem for GEMA club monitoring. — [iRights](https://irights.info/artikel/gema-diskotheken-monitoring-remix-sampling/26911) (snippet)
- PPL's sample and derivative misreporting, the no-context problem (adverts and interstitials), and rights-data conflicts are covered in section 2. — [MBW](https://musicbusinessworldwide.com/there-is-a-real-need-to-look-behind-the-marketing-hype-and-truly-understand-what-is-being-offered)

### Inferences
- **Sampling is the norm.** CMOs typically sample (GEMA: 120 clubs, one hour per week) and combine MRT with proxies. A SoundProof pitch to Cameroonian CMOs could be framed as a statistically designed sample network plus audit, rather than census coverage of every bar. This lowers hardware cost.
- **Privacy design:** Audoo's design (on-device fingerprinting, no raw audio leaves the box, speech suppressed) is the reference for both privacy and bandwidth. It is likely necessary for venue-owner acceptance.
- **Transparency for venues:** GEMA was criticised for black-box opacity. Giving venues visibility (such as a dashboard and recording indicators) could help acceptance.
- **Build options:** ACRCloud's offline/edge SDK, local monitor and pay-as-you-go APIs, already used by African broadcast monitors like Qisimah, are a plausible build-vs-buy shortcut. The key question is whether ACRCloud's reference database covers Cameroonian repertoire. Ingesting local catalogues into a custom bucket would be needed.
- **Africa precedent:** GoCreate's 2022 pan-African plans had no follow-up coverage. This is a possible sign of stalled rollout, but it is unconfirmed.

### Gaps (vendors or questions not covered due to tool-call budget)
- No research was done on these vendors and products: Audible Magic; Gracenote/Nielsen BDS; Mediabase; Radiomonitor; Kantar Media audio; Pex; Shazam/ShazamKit B2B use; Serato; Mood Media / PlayNetwork data sharing; Muzeek; TrackTrace; WhatTheSound. Radiomonitor, Gracenote, Audible Magic, Soundmouse, BMAT, ACRCloud, Audoo and DJ Monitor are listed in the IFPI MRT tracker of about 60 companies, per [MBW](https://musicbusinessworldwide.com/there-is-a-real-need-to-look-behind-the-marketing-hype-and-truly-understand-what-is-being-offered).
- No direct source confirmed ACRCloud's use by CMOs for venue (not broadcast) monitoring.
- No pricing for any vendor's device or service.
- No patents were reviewed for vendor approaches to DJ mixes or overlapping tracks.
- Current (2025–2026) status of GoCreate Africa and Vollou's Sabam deployment scale is unknown.
- It is unconfirmed whether the BMI–Soundmouse deal closed.
- The JASRAC–Audoo Tokyo bar trial was mentioned in a search summary but not confirmed from a primary source.
