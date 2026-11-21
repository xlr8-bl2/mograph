# How CMOs/PROs estimate music usage for royalty distribution (census, sampling, proxies), with an African focus

Scope note: I made about 20 tool calls. Several primary PDFs could not be fetched: the PRS policy links returned 404, NPR returned 503, and the ASCAP rules pages were not found by search. Coverage is strong for PRS, GEMA, SOCAN and Kenya, partial for SACEM, SONACAM and BURIDA, and thin for ASCAP/BMI specifics, the vendors, and SAMRO/COSON/GHAMRO/BUMDA. Background knowledge that I could not source in this session is in the Gaps sections and is marked UNVERIFIED.

## 1. Census vs sample vs proxy: the general framework used by major CMOs

### Takeaway
Major CMOs use a clear order of preference. Census (every use) comes first, then a statistically representative sample, then analogy/proxy data where collecting actual usage costs too much compared with the money involved. Background music in pubs, bars and shops is the standard case where CMOs explicitly choose proxies over sampling. That is the gap SoundProof would fill.

### Cited Findings
- PRS for Music's distribution policy ranks its distribution methods in this order of preference: census (collect and pay on every performance in the licence period), then sampling, then analogy. — [PRS distribution cycles & concepts policy, Apr 2023](https://www.prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/prs-distribution-policy/april-2023/2prspolicydistributioncyclesandconcepts); [PRS full distribution policy](https://prsformusic.com/-/media/files/prs-for-music/membership/membership-policies/full-document-prs-dist-policy) (content seen via search summaries; the direct fetch returned 404)
- PRS says sampling is not an appropriate basis where data collection is expensive and music usage is very high. It gives background music in pubs as the example where a statistically representative sample "would not be cost effective". — same PRS policy documents (search-extracted)
- PRS uses analogy (proxy data) "most frequently" for general public performance of non-featured recorded music. If a licensee does not supply data, PRS holds the revenue and, after reasonable steps, may distribute it over analogous data. — same PRS policy documents (search-extracted)
- ASCAP states it runs a census (complete count) "whenever it is economically sensible". Where a census is impractical, it runs "a sample survey designed to be a statistically accurate representation of performances in a medium". Its payment system is guided by a "follow the dollar" principle: money from a medium is paid out on performances in that medium. — [ASCAP self-description (archived reading, Harvard Berkman)](https://cyber.harvard.edu/is02/readings/ascap.html) (old text, around 2002)
- SOCAN uses fees from venues, sporting events, background music suppliers and theme parks to fund its radio distribution pools (CBC Pool, Survey Radio Pool, Census Radio Pool). In practice, radio airplay is the proxy that distributes general-licensing money. — [SOCAN Simplified Distribution Rules (Oct 2023 edition, posted 2024)](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)

### Inferences
- SoundProof is in effect an attempt to move the bar/beer-levy pool from "analogy" or flat payouts up to "sampling", the middle tier of the PRS hierarchy. The main hurdle, by PRS's own reasoning, is cost per observation relative to the size of the pool. Cheap phones attack exactly that hurdle.
- "Follow the dollar" suggests a design rule: keep the beer-levy pool separate and distribute it on bar observations, rather than mixing it with broadcast data, unless bar data is too sparse.

### Gaps
- I could not retrieve ASCAP's current Survey and Distribution System rules. These cover sample hours, weighting by license fee and station class, and "premium" credits for top-rated stations. The station-weight and hit-weighting details are UNVERIFIED from this session.
- I could not retrieve current BMI methodology. From prior knowledge (UNVERIFIED here): BMI moved to census-style radio data from digital monitoring, and BMI general-licensing money is distributed using proxy media data.

## 2. Sampling design, sample sizes and extrapolation (radio and venues)

### Takeaway
The best-documented venue sampling scheme is GEMA's discotheque monitoring. It uses a stratified random sample of about 1,600 dance floors, each recorded for about 2 hours per operating week, extrapolated to total national play-minutes. SOCAN samples some radio (21 days per period, with population weighting for local CBC) and weights by each station's licence fees.

### Cited Findings
- GEMA: monitoring boxes tap the output signal at the DJ mixer on "a large, representative number of DJ mixing consoles all over Germany". The results are "projected to the whole of Germany" using projected minute values. Distribution is annual (as of 1 June). Works must be registered by 31 January of the year after use. — [GEMA DK/DK VR distribution page](https://gema.de/en/music-creators/royalties/categories-dk-dkvr); [GEMA club & discotheque monitoring](https://gema.de/en/music-creators/monitoring/discotheque-monitoring)
- GEMA sampling design:
  - The population is all music plays in a year in German discotheques and similar venues.
  - Dance floors are divided into 12 strata, with a separate sample drawn in each.
  - The estimate is each title's share of total play-duration.
  - GEMA calls the result an unbiased ("unverzerrte") extrapolation of a title's annual total play time across all dance floors.

  — [GEMA information sheet on discotheque monitoring](https://www.gema.de/fileadmin/user_upload/Musiknutzer/Informationen/information_diskothekenmonitoring.pdf); [English version](https://www.gema.de/fileadmin/user_upload/Musikurheber/Informationen/information_discothekenmonitoring_e.pdf)
- GEMA sample size: around 1,600 dance floors captured per year, with 2 hours of music recorded per dance floor per operating week. — [GEMA Infoblatt DK/DK VR FY2021](https://www.gema.de/documents/d/guest/user_upload-dokumente-musikurheber-tantiemen-archiv-tantiemenverteilung_inland-infoblatt_dk_dk_vr_gescha%CC%88ftsjahr_2021-pdf); [2022](https://gema.de/documents/d/guest/infoblatt_dk_dk_vr_2022_de); [2023](https://www.gema.de/documents/d/guest/infoblatt_dk_dk_vr_2023_de-pdf). Older reporting (2015) cited about 120 discotheques per week, 1 hour each, and described a GEMA cooperation with Yacast to build a track-accurate system. — [netzpolitik.org 2015](https://netzpolitik.org/2015/black-box-discotheken-monitoring-gema-kooperiert-mit-yacast-gti-entwickelt-track-genaues-system/). These figures differ by year and should be confirmed against the current Verteilungsplan.
- SOCAN radio:
  - Commercial stations monitored by Mediabase are paid on a census basis, but only if the work is registered with Mediabase.
  - Non-Mediabase commercial stations submit programming each quarter (Survey Radio Pool).
  - CBC network and regional radio are paid on census. Local CBC is "sampled across the country and weighted according to the population in each locality".
  - Pay audio and satellite radio are paid from a 21-day sample per distribution period.
  - "Station Weight" is "in direct proportion to the license fees received from each station".
  - Credits are duration-based: under 1:00 = 1 credit; 1:00–6:59 = 4 credits; 7:00 or longer = 1 credit per additional minute.
  - SOCAN warns members that "some stations pay on a sample, which can mean that your song did air on that station, but was not captured for distribution".

  — [SOCAN Simplified Distribution Rules](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)
- Typical CMO valuation: a quarterly credit value equal to the pool divided by total payable credits. The SOCAN text above states this explicitly ("total number of credits… divided into the total amount of money in each pool to give a Credit Value"). — [SOCAN rules](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)

### Inferences
- GEMA's design maps directly onto SoundProof:
  - Stratify bars, for example by city, size/tariff class and type (bar, cabaret, snack-bar, nightclub).
  - Record a fixed number of hours per sampled venue per week.
  - Estimate each title's share of play-minutes per stratum.
  - Weight strata by their share of levy or licence revenue (the station-weight analogue).
  - Multiply each title's share by the pool.
- Note the scale: GEMA monitors about 1,600 dance floors in a country with thousands of clubs. In 2013, France had about 2,000 true discotheques (see section 3). For a 100M FCFA pool, a much smaller sample may be cost-justified, but the rare-title error grows as the sample shrinks.
- The SOCAN warning ("aired but not captured") is the explicit long-tail cost of sampling. SoundProof should plan for it, for example with a minimum-payment floor or a claims and evidence process.

### Gaps
- I did not obtain GEMA's documented confidence intervals or its minimum play-thresholds for DK distribution.
- I did not retrieve ASCAP's specific sample-hour numbers or the BMI/PRS radio sampling parameters.

## 3. Background/public-performance music (bars, shops, clubs): monitoring and distribution

### Takeaway
Most CMOs still distribute bar, shop and restaurant money on proxies (radio/TV airplay, charts) rather than on observed usage. Nightclubs are the exception, where DJ-booth monitoring samples are common (GEMA, and SACEM via its discotheque rules). The newest trend (2025–2026) is census data taken directly from business-music streaming providers, such as PRS with Soundtrack.

### Cited Findings
- PRS has partnered with Soundtrack (a business music streaming service) to receive play-by-play data on the exact music played across around 7,000 UK premises. The aim is to reduce reliance on analogous data, and PRS will begin using the data in distributions from December 2026. Soundtrack's founder argued that in most countries public performance royalties are distributed on proxy data, not actual usage. — [Music Week](https://www.musicweek.com/publishing/read/new-prs-for-music-ceo-ben-mcowen-wilson-unveils-soundtrack-deal-to-improve-accuracy-of-royalties/095347)
- SACEM publishes a separate distribution-rules chapter for "discothèques et bars à ambiance musicale". In 2013 France had 2,696 establishments on a "discothèque et bar dansant" contract (about 2,000 true discotheques), and these represented only 4% of total SACEM distributed royalties. A 2022 SACEM–CHRD union agreement aligned the "bars à ambiance" (BAM) category with the old nightclub tariff. — search-extracted from [French National Assembly written questions](https://www.assemblee-nationale.fr/dyn/15/questions/QANR5L15QE28724.pdf) and [Banque des Territoires](https://www.banquedesterritoires.fr/le-nombre-de-discotheques-diminue-de-moitie-en-trente-ans); SACEM Luxembourg rules: [Règles de répartition avril 2025](https://www.sacem.lu/media/33bee4f4-de46-4202-97d0-4d64f8201150/regles-de-repartition-avril-2025-fr.pdf) (not read in full)
- In Canada, background music licensing is split. SOCAN Tariff 15.A licenses establishments directly. Tariff 16 licenses background-music suppliers, and the Copyright Board treated Tariff 15.A as the "most appropriate proxy" for Tariff 16 when setting rates. Re:Sound (neighbouring rights) separately licenses nightclubs, bars, restaurants and retail. — [Copyright Board decision](https://decisions.cb-cda.gc.ca/cb-cda/decisions/en/366668/1/document.do); [DLA Piper, Music licensing in Canada, Jan 2024](https://www.dlapiper.com/en/insights/publications/2024/01/music-licensing-in-canada)
- SOCAN uses general-licensing revenue (venues, background suppliers) to fund radio distribution pools. Radio airplay is therefore the de facto proxy for venue use. — [SOCAN rules](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)

### Inferences
- In a bar-heavy market like Cameroon, radio-as-proxy probably fits poorly: bar playlists may lean toward local genres such as bikutsi and makossa, DJ mixes, USB/phone playlists, and older catalogue. That divergence is the main argument for direct sampling. This is a hypothesis to test in a pilot by comparing SoundProof bar data with radio logs.
- A hybrid is defensible on these precedents. One version distributes most of the pool on bar samples, and a residual on radio proxy or a flat floor until bar data matures.

### Gaps
- I did not verify SACEM's actual discotheque method, such as the number of venues sampled or whether it uses Yacast or BMAT boxes. From prior knowledge (UNVERIFIED): SACEM has used club monitoring boxes and "sondages" for small establishments, and distributes small venues' money on radio and TV data.
- I did not verify PPL's public-performance method. UNVERIFIED: PPL uses airplay as a proxy for public performance, with some background-music supplier data.
- I did not verify the details of PRS's earlier background music monitoring trials or its nightclub DJ monitoring vendor (UNVERIFIED: DJ Monitor / Kollector-type devices).
- I found nothing on SIAE, JASRAC or APRA AMCOS in this session. UNVERIFIED: APRA AMCOS ran background music and nightclub surveys and uses proxies; JASRAC has run sampling of background music in shops.

## 4. African CMOs: distribution and monitoring

### Takeaway
African CMO practice is poorly documented in public. Sources show reliance on broadcaster-supplied logs (SAMRO), third-party airplay trackers (Radiomonitor, via MAUS in South Africa), long-running disputes over monitoring accuracy and governance (Kenya), and flat or "productivity"-based payouts with no published usage data (SONACAM). I found no published evidence of a working venue or bar monitoring network at any African CMO.

### Cited Findings
- SAMRO (South Africa) relies on broadcasters to provide usage data. Musicians can track airplay independently with Radiomonitor or WARM (World Airplay Radio Monitor). — [Music In Africa, Understanding broadcasting royalties in SA](https://musicinafrica.net/node/222086); [Music In Africa, Importance of music metadata and radio monitoring](https://musicinafrica.net/magazine/importance-music-metadata-and-radio-monitoring/)
- MAUS, a South African airplay tool launched in 2022, uses Radiomonitor data and calculates royalties owed from each station's predetermined usage rate. — [Music In Africa, MAUS / Mark Stent interview](https://www.musicinafrica.net/magazine/maus-mark-stent-talks-about-new-sa-airplay-tracking-tool)
- SAMRO introduced a system to improve its ability to collect royalties and monitor airplay on internet, TV and radio. The vendor is not named in the summary. — [Music In Africa (search-extracted)](https://musicinafrica.net/?p=143045)
- Kenya: in 2016 the band Elani disputed its royalties against its heavy airplay. MCSK blamed "irregularities in monitoring airplay" and broadcaster non-compliance, and Elani went to radio stations to collect logs themselves. — [IP-Watch 2016](https://www.ip-watch.org/2016/02/10/kenyan-musicians-escalate-fight-for-royalties/); [Music In Africa: Broadcaster royalties in Kenya 'still a huge problem'](https://www.musicinafrica.net/magazine/broadcaster-royalties-kenya-still-huge-problem)
- Kenya governance:
  - KECOBO deregistered KAMP, PRISK and MCSK for failing licence conditions.
  - PRISK became PAVRISK. The provisional licences expired on 8 May 2024, and the Copyright Tribunal invalidated a bid to make PAVRISK the sole CMO.
  - In 2025, KECOBO called joint MCSK-PAVRISK-KAMP licensing "illegal and criminal".
  - A forensic audit covering 2020–2022 found KAMP and PRISK failed to remit millions of shillings.

  — [The Standard](https://www.standardmedia.co.ke/counties/article/2001421665/kenya-copyright-board-deregisters-kamp-prisk-and-mcsk); [Music In Africa, MCSK vs PAVRISK](https://www.musicinafrica.net/node/368504); [Nation](https://nation.africa/kenya/life-and-style/showbiz/is-kecobo-favouring-pavrisk-in-artistes-royalty-collections--5585212); [CIPIT Strathmore](https://cipit.strathmore.edu/?p=11531)
- SONACAM (Cameroon):
  - First distribution, December 2018: 154M FCFA to about 1,500 artists. The criteria were based "notamment sur le degré de productivité des artistes" (vice-president Elisabeth Benkum). — [camer.be](https://camer.be/amp/culture/musique/cameroun-droits-dauteurs-154-millions-pour-les-artistes-cameroon.html)
  - A later distribution (undated in the snippet, around 2022–2023) paid about 80M FCFA to 2,040 artists, with amounts ranging from 25,000 to 300,000 FCFA. January 2023 reached about 1,700 artists. — [Cameroon Tribune](https://cameroon-tribune.cm/article.html/58312/fr.html/details_2); [Cameroon Tribune](https://cameroon-tribune.cm/article.html/40608/fr.html/details_2)
  - In 2023 the five Cameroonian CMOs held four distributions (January, June, October, December). Canal+ Afrique payments to the CMOs rose from 70M to 150M FCFA per year, with a 340M FCFA per year target by 2026. Other sources were telecom "Corporate" contracts and a 2022 GICAM convention. The article does not mention usage monitoring or diffusion data. — [Cameroon Tribune, 28 Dec 2023](https://cameroon-tribune.cm/article.html/62178/fr.html/details_2)
- BURIDA (Côte d'Ivoire): radios, TVs and telecoms owed more than 800M FCFA for 2019–2021. From 2020 to 15 September 2021, BURIDA distributed 2,406,051,569 FCFA. BURIDA has pursued digitalisation, presented its digital tools to SACEM, and added mobile-money payouts. — [Koaci, Sept 2021](https://www.koaci.com/article/2021/09/21/cote-divoire/culture/cote-divoire-differents-droits-des-artistes-les-radios-televisions-et-telephonies-doivent-plus-de-800-millions-fcfa-au-burida-tout-sur-les-derniers-chiffres-des-repartitions_154051.html); [Osiris, Jan 2022](https://www.osiris.sn/Digitalisation-Les-outils.html)
- African CMOs assign IPI numbers to identify creators, and "most CMOs and broadcasters" use these codes to track airplay. — [Music In Africa](https://musicinafrica.net/magazine/importance-music-metadata-and-radio-monitoring/)

### Inferences
- The flat 25,000–300,000 FCFA range and "productivity" criteria at SONACAM are consistent with distribution that does not use observed usage data. SoundProof's comparison baseline is therefore not a sampling scheme but a flat or discretionary one. Even a modest sample would be a step up in transparency.
- The Kenyan experience shows that monitoring data becomes a governance and political flashpoint. Artists will contest results, so raw logs need to be auditable and available to members, which is what Elani had to do by hand.

### Gaps
- I found no source on the BUMDA (Senegal; now SODAV, UNVERIFIED), COSON/MCSN (Nigeria) or GHAMRO (Ghana) monitoring vendors or distribution formulas. UNVERIFIED prior knowledge: GHAMRO and COSON have announced airplay-monitoring partnerships at various times, and some West African CMOs have signed with BMAT or local monitoring firms. I could not confirm any of this.
- I found no source on the SONACAM beer-crate (brasserie) levy mechanics or on the 27,000 FCFA flat payout cited in the brief. Treat both as client-supplied.
- I found no evidence of BMAT, Soundmouse or other vendor venue deployments at any African CMO.

## 5. Monitoring vendors: coverage, accuracy, deployments

### Takeaway
This session produced little vendor-specific evidence. The confirmed points are: BMAT monitors TV, radio, venues and digital globally; Mediabase underpins SOCAN's census radio data; Radiomonitor supplies South Africa's MAUS; and Yacast partnered with GEMA on club monitoring. I could not source accuracy claims.

### Cited Findings
- BMAT says it monitors and reports music usage globally across TV, radio, venues and digital. A Jamaican CMO has used BMAT digital monitoring since 2013. — [Wikipedia, BMAT](https://en.wikipedia.org/wiki/BMAT_Music_Company) (secondary source)
- Mediabase monitoring defines the SOCAN Census Radio Pool. Works not registered with Mediabase are not captured. — [SOCAN rules](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)
- Radiomonitor provides South African airplay data, used by MAUS. — [Music In Africa](https://www.musicinafrica.net/magazine/maus-mark-stent-talks-about-new-sa-airplay-tracking-tool)
- GEMA cooperated with Yacast to develop a track-accurate discotheque system (2015). — [netzpolitik.org](https://netzpolitik.org/2015/black-box-discotheken-monitoring-gema-kooperiert-mit-yacast-gti-entwickelt-track-genaues-system/)

### Inferences
- Reference-database dependence is a key failure mode. SOCAN pays a work only if it is registered with Mediabase. In Cameroon, local recordings must be fingerprinted into SoundProof's reference database, or the system will systematically miss them. That is a long-tail bias created by the design itself.

### Gaps
- I did not retrieve published accuracy rates, fingerprinting precision in noisy venues, or coverage counts for BMAT Vericast, Soundmouse/Orfium, DJ Monitor, Kantar, or Nielsen BDS. These need a dedicated vendor search.

## 6. Criticisms: bias toward hits, long tail, transparency

### Takeaway
The recurring criticisms are these: samples miss niche and long-tail plays; station or venue weighting by licence fee or audience favours mainstream repertoire; club samples are said to skew toward major-label hits; and poor monitoring combined with weak governance produces disputes (Kenya). Census and direct data (Soundtrack, Mediabase) are presented as the fix, but they bring a registration bias of their own.

### Cited Findings
- A critic quoted by NPR (2014) said PROs are "more likely to sample radio stations that have higher listenership, so it's statistically rigged". This is one party's opinion and the context is unverified. — [NPR The Record, 2014](https://www.npr.org/sections/therecord/2014/02/13/276462033/in-the-digital-era-hit-songs-arent-everything) (page returned 503; quote via search snippet)
- netzpolitik.org questioned whether GEMA's club sample is representative of club music, arguing it is geared toward major-label hits. GEMA claims its extrapolation is statistically unbiased. — [netzpolitik.org 2015](https://netzpolitik.org/2015/black-box-discotheken-monitoring-gema-kooperiert-mit-yacast-gti-entwickelt-track-genaues-system/); [GEMA info sheet](https://www.gema.de/fileadmin/user_upload/Musiknutzer/Informationen/information_diskothekenmonitoring.pdf)
- SOCAN tells members that on sampled stations a song "did air… but was not captured for distribution". — [SOCAN rules](https://www.socan.com/wp-content/uploads/2024/10/Simplified-Distribution-Rules-2024.pdf)
- A 2005 US Copyright Royalty Board filing argued that ASCAP/BMI practice gives "no support for permitting reports based on sample, rather than census, data". This is an advocacy document. — [CRB exhibit 2005-5 (SoundExchange)](https://www.crb.gov/proceedings/2005-5/soundex-exh414.pdf)
- Soundtrack's founder said most countries distribute public-performance royalties on proxies rather than actual usage. — [Music Week](https://www.musicweek.com/publishing/read/new-prs-for-music-ceo-ben-mcowen-wilson-unveils-soundtrack-deal-to-improve-accuracy-of-royalties/095347)

### Inferences
- An unbiased estimator does not by itself make a fair outcome. Even when the expected value is correct (GEMA), sampling variance means rarely played titles often get zero in a given period. Mitigations that follow from this:
  - pooling across periods
  - minimum floors
  - publishing standard errors
  - letting members submit evidence

### Gaps
- I did not retrieve CISAC Professional Rules on distribution, WIPO good-practice toolkits on CMO distribution, the EU Collective Rights Management Directive 2014/26/EU transparency provisions, or academic econometric studies of PRO sampling accuracy. All are important and need a follow-up search.
