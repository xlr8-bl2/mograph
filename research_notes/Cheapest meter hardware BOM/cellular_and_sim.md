# Cheapest cellular modules and SIM options for a mass-produced IoT audio meter in Cameroon (2025–2026)

Research date: 2026-11-23. Currency conversion assumption: about 7.1 CNY = 1 USD. This is my own assumption, not a sourced rate. Recompute the conversions with the current rate. All prices are snapshots. "Retail" means a single-unit or small-quantity list price. "Volume" means a published tier of 1k+ units.

## Q1. Prices and specs of the cheapest modules, user-code support, memory, and audio inputs

### Takeaway
The cheapest viable path is a Luat/Hezhou Cat.1 module (Air780EPM / Air780EX2 / Air700ECP at about ¥16.8, roughly $2.4) or a China Mobile ML307R (about $2.55 at 1k+ on LCSC-linked listings). The Luat modules run user code (LuatOS/Lua, plus a C SDK). The ML307R appears to be AT-command only in the sources found. One constraint matters for an audio meter: Luat states that 4MB-RAM variants (Air780EPM, Air700ECP, Air780EGP) do **not** support audio. Any audio needs an 8MB variant: Air780EHM/Air700ECH at ¥19.8 with an external I2S codec, or Air780EHV at ¥22.8 with a built-in ES8311 codec that does recording. 2G SIM800C is not cheaper (about $5.3 at 100+ on LCSC) and is flagged obsolete on Octopart.

### Ranked table (cheapest viable first)

| Rank | Module | Tech | Price (USD, approx.) | Price type / source | User code? | Memory | Audio | Bands (relevant) |
|---|---|---|---|---|---|---|---|---|
| 1 | Luat Air780EPM | LTE Cat.1 | ¥16.8 ≈ $2.37 (official list); history ¥15.8 (incl. tax); third-party ¥19.9 | Official list / Taobao; volume by sales quote | Yes, LuatOS | 4MB flash + 4MB RAM | **No audio** (4MB model) | China band set (assumed same as Air780E: B1/3/5/8/34/38/39/40/41, unverified for EPM) |
| 1= | Luat Air780EX2 / Air700ECP | LTE Cat.1 | ¥16.9 / ¥16.8 ≈ $2.37 | Official selection guide | Yes, LuatOS | 4+4MB | No audio | Unverified |
| 2 | China Mobile ML307R-DL | LTE Cat.1 | $4.17 (1+), $2.80 (100+), **$2.55 (1000+)** | EBEE distributor tiers; LCSC part C48982537 | Not confirmed. Sources describe AT commands only | n/a | "PCM voice function" listed | B1/3/5/8/34/38/39/40/41 |
| 3 | Luat Air780EHM / Air700ECH | LTE Cat.1 | ¥19.8 ≈ $2.79 | Official selection guide | Yes, LuatOS | 8MB + 8MB | External codec via I2S | Unverified (China set assumed) |
| 4 | Luat Air780EHV | Cat.1 + VoLTE | ¥22.8 ≈ $3.21 | Official selection guide | Yes, LuatOS | 8+8MB | **Built-in ES8311 codec, recording, MP3** (mic can connect directly) | Unverified |
| 5 | Luat Air780E (original) | LTE Cat.1 | Batch about ¥24 ≈ $3.4 (FAQ, 2026-08); 2022 launch dev-board promo ¥9.9 (ended) | Official FAQ via forum | Yes, LuatOS | 4+4MB | No TTS/VoLTE; 4MB tier = no audio | FDD B1/3/5/8, TDD B34/38/39/40/41 |
| 6 | SIMCom SIM800C | 2G GSM/GPRS | $7.30 (1+), **$5.27 (100+)**; another snapshot $5.77/5.53/5.39 | LCSC C2981431 (retail/small volume) | No (AT); Octopart marks obsolete | n/a | Analog audio (voice) | Quad-band GSM |
| 7 | Luat Air780EHU (EU/Asia/Africa intl) | LTE Cat.1 | ¥39 ≈ $5.5 | Official selection guide | Yes, LuatOS | 8+8MB | Needs external chip | Intl band set (Air780EU: B1/3/7/8/20/28) |
| — | Quectel EC800M-CN / EC800N-CN / EG800K | LTE Cat.1 / Cat.1bis | **No price found** | — | Yes, QuecOpen (and QuecPython) | not found | not found | CN variants (China bands) |

### Cited Findings
- Luat official selection guide lists: Air780EPM ¥16.8 (4+4MB, "Air780EPM is 4MB, so no audio support"), Air780EX2 ¥16.9, Air700ECP ¥16.8, Air780EHM ¥19.8 (8+8MB, external codec via I2S), Air780EHV ¥22.8 (built-in ES8311 codec, VoLTE, recording, MP3), Air780EHU (EU/Asia) ¥39, Air780EHN (N. America) ¥49, Air8000A ¥46. It states that 4MB-RAM models cannot support audio. — [Luat selection guide](https://docs.openluat.com/SelectionGuide/SelectionGuide/)
- Luat FAQ: Air780EPM historical reference price about ¥15.8/pc incl. tax. Prices change; batch prices come from sales. — [Luat FAQ 2026-08-11](https://docs.openluat.com/faq/2026-08-11/)
- Air780E: 4MB flash / 4MB RAM, LuatOS Lua development. Launch promo ¥9.9 in late 2022 has ended. FAQ (2026-08) batch price about ¥24. Air1780 series "from ¥9.9". (Secondary forum post summarising official sources, posted 2026-09-21.) — [fcgvisa forum](https://bbs.fcgvisa.com/t/air780e-4g-9-9-lua/57752)
- Air780E bands: FDD B1/3/5/8, TDD B34/38/39/40/41. Does not support TTS or VoLTE. Typical current about 4.7 mA normal, 0.47 mA low power, about 3 µA PSM+. Air780EP is pin/AT compatible with different power use. — [openluat Air780E docs](https://docs.openluat.com/air780e/)
- Air780EU (international) bands LTE-FDD B1/B3/B7/B8/B20/B28, for Europe, Middle East, Africa, Russia, Thailand. — [DFRobot wiki TEL0177](https://wiki.dfrobot.com/tel0177/)
- Retail Air780E boards: DFRobot TEL0179 $19.90 on DigiKey, about $17–19 on eBay/Newegg (board-level, not bare module). — [DigiKey](https://azcus.digikey.com/en/products/detail/dfrobot/TEL0179/24611150)
- ML307R-DL: $4.1683 (1+), $2.8036 (100+), $2.5533 (1000+). — [EBEE](https://www.ebee.com/product-detail/chinamobile-ml307r-dl_12501157.html). LCSC C48982537: 17.7×15.8 mm, 3.4–4.5 V, bands B1/3/5/8/34/38/39/40/41, PCM voice, UART/USB/GPIO/ADC/PWM. — [LCSC ML307R-DL](https://www.lcsc.com/product-detail/C48982537.html)
- M5Stack Unit Cat1-CN uses ML307R-DL over UART AT commands. — [M5Stack docs](https://docs.m5stack.com/en/unit/Unit_Cat1-CN)
- SIM800C LCSC C2981431: $7.2986 (1+) to $5.2698 (100+), with 1062 in stock in one snapshot. Another snapshot showed $5.7722/5.533/5.3879. Xonelec $12.87 (1) to $6.27 (100). Octopart lists the part as obsolete. — [LCSC SIM800C](https://www.lcsc.com/product-image/C2981431.html); [Octopart](https://octopart.com/part/simcom/SIM800C)
- Quectel EC800M-CN: "cost-effective LTE Cat 1" module, 10 Mbps DL / 5 Mbps UL. Some interfaces are QuecOpen-only. A QuecOpen hardware design document exists. — [Quectel EC800M-CN spec](https://developer.quectel.com/en/wp-content/uploads/sites/2/2024/11/Quectel_EC800M-CN_LTE_Standard_Specification_V1.2.pdf); [QuecOpen HW design](https://developer.quectel.com/en/wp-content/uploads/sites/2/2024/11/Quectel_EC800M-CN_QuecOpen_Hardware_Design_V1.1.pdf)
- EG800K is package-compatible with EC800E/G/M/N/K-CN. Avnet listing for an EG800K part shows MOQ 50 and a 56-week factory lead time, with no price. — [Quectel EG800K](https://developer.quectel.com/en/modules-cat/eg800k-series); [Avnet](https://www.avnet.com/apac/product/quectel-wireless-solutions/eg800akcn11ln-4i-ta0aa/arise-que-queeg800akcn11ln)

### Inferences
- For an audio meter running code on the module, the realistic cheapest single-chip option is **Air780EHV (~$3.2)**, with a built-in codec and mic input, or **Air780EHM (~$2.8) + external I2S codec/MEMS mic**. A 4MB part at $2.4 saves about $0.4–0.8 but cannot do audio under LuatOS per the vendor. A PDM/I2S MEMS mic with an external codec might be possible on 8MB parts only.
- The China-band Luat/ML307R parts include B3 and B41, which match Orange (B3) and MTN (B41) in Cameroon (see Q2). The cheap CN variants may therefore work technically in Cameroon without paying ¥39 for the international EHU. This is unverified in the field and has certification/type-approval implications.
- 2G modules are now more expensive than Cat.1 at comparable volumes and draw higher peak current, so 2G has no cost advantage.

### Gaps
- No verified prices found for Quectel M66/MC20, Luat Air200/201/202, Air724UG, Fibocom MC660, Neoway, Quectel BC660K, BG95/BG77, SIMCom SIM7080G, or nRF9151. NB-IoT/LTE-M parts are likely irrelevant anyway (see Q2).
- No published CPU clock or exact user-available RAM/flash for LuatOS (figures above are total module flash+RAM).
- Band lists for Air780EPM/EHM/EHV were not explicitly confirmed. I assumed the China set.
- I did not confirm QuecOpen availability or pricing for EC800M/EG800K at 1k–10k.
- I did not confirm whether ML307R supports OpenCPU (China Mobile "OpenCPU" variants may exist but were not found).

## Q2. LTE bands in Cameroon, operator coverage, NB-IoT/LTE-M, 2G/3G sunset

### Takeaway
Cameroon 4G runs mainly on **B3 (1800, Orange; CAMTEL)** and **B41 (TDD 2500, MTN)**. Orange later added B20 and B41 carriers per one source, and CAMTEL also uses B1 (2100). Nexttel has no 4G. 2G is on 900/1800. No NB-IoT or LTE-M deployment and no 2G/3G sunset date were found. ART is actively penalising operators for poor 2G/3G coverage, which suggests 2G stays for now.

### Cited Findings
- Wikipedia LTE list for Cameroon: MTN 2500 MHz Band 41 (Dec 2015). Orange 1800 MHz Band 3 (Apr 2016, 10 MHz). No LTE-M/NB-IoT listed. — [Wikipedia LTE networks in Africa](https://en.wikipedia.org/wiki/List_of_LTE_networks_in_Africa)
- MTN launched 4G in Dec 2015 on B41. Orange launched on B3 and later added B20 and B41 carriers. — [Powertec MTN](https://portal.powertec.com.au/industry-resources/companies/mtn-cameroon); [Powertec Orange](https://portal.powertec.com.au/industry-resources/companies/orange-cameroon)
- Kimovil Cameroon: 4G B1 (2100), B3 (1800), B41 (TDD 2500). 3G B1 and B8. 2G B3 (1800) and B8 (900). CAMTEL on 1800/2100. — [Kimovil frequency checker CM](https://www.kimovil.com/en/frequency-checker/CM)
- 2018: ART regulator decision related to allocating 700/800 MHz to MTN and Orange. — [Samena](https://samenacouncil.org/samena_daily_news?news=82377); [Osiris](https://www.osiris.sn/Cameroun-C-est-fait-L-ART-assigne.html)
- Nexttel lacks 4G/LTE per one travel source. MTN reported population coverage of 97.35% (2G), 87.58% (3G), and 60% (4G); the article is undated/older. — [phonetravelwiz](https://www.phonetravelwiz.com/buying-a-sim-card-in-cameroon-guide/); [ITWeb](https://itweb.africa/article/mtn-cameroon-guns-for-total-network-coverage/nWJadMbN6RPMbjO1)
- CAMTEL has a national roaming deal on MTN where CAMTEL lacks coverage. — [MTN](https://www.mtn.com/mtn-cameroon-to-support-camtel-service-with-national-roaming-agreement/)
- Onomondo coverage lists MTN Cameroon (62401) with 2G/3G/LTE, without NB-IoT/LTE-M. — [Onomondo coverage](https://onomondo.com/coverage/)
- 2G/3G sunset: an Orange Business tracker lists MTN Cameroon and Orange Cameroun with "Unknown" closure dates (per search snippet; the PDF now redirects). ART fined Orange and MTN 2.6bn FCFA (July 2025) and issued notices on 2G/3G coverage breaches. — [Orange Business tracker (redirected)](https://reseaux.orange-business.com/wp-content/uploads/2024/05/2G-3G-shutdowns-worldwide-planning-20240201-2.pdf); [Rio Times](https://www.riotimesonline.com/cameroon-telecom-regulator-93-breaches-orange-mtn-camtel/)

### Inferences
- A China-band Cat.1 module (B1/3/5/8/34/38/39/40/41) covers MTN B41 and Orange/CAMTEL B3/B1. It lacks B20 (800, Orange's later carrier) and B7, which matters little in Cameroon.
- The international Air780EU set (B1/3/7/8/20/28) **lacks B41**. On MTN 4G (B41-only per sources), it would get no LTE. CN-band parts are arguably the better fit for Cameroon. Cat.1 modules have no 2G fallback, so a site without 4G gets no service.
- Avoid NB-IoT/LTE-M modules. I found no evidence of deployment in Cameroon.

### Gaps
- No ART primary source on spectrum assignments or sunset plans was retrieved. Whether 700/800 MHz LTE is actually live is unconfirmed.
- Cameroon 4G geographic coverage (vs. population) for the deployment areas is unknown. Check nPerf maps for MTN and Orange.

## Q3. eSIM (MFF2) cost, global IoT SIM providers, roaming, and SIM registration

### Takeaway
Published global IoT SIM pricing: 1NCE about $10–15 one-time for 10 years and 500 MB (business only; Cameroon coverage not confirmed). Hologram charges $1/month + $0.03/MB + $3 one-time. emnify is under €2–3 per SIM plus usage. Onomondo starts at €0.003/MB. None of the providers' pages I found named their Cameroonian partner networks, except Onomondo listing MTN Cameroon. Cameroon law (Decree 2015/3759) requires subscriber identification and ART has sanctioned operators over pre-activated SIMs. A local operator M2M SIM (MTN/Orange) may be needed for compliance and permanent deployment. This is unverified.

### Cited Findings
- 1NCE IoT Flat Rate: $10 for 10 years / 500 MB / 250 SMS (US site). €12 (EU). $15 (APAC page). AWS Marketplace $10 flex, $12 eSIM. Business customers only. "173 countries". — [1NCE US](https://1nce.com/en-us/1nce-connect/10-dollars-for-10-years); [1NCE EU pricing](https://www.1nce.com/en-eu/1nce-connect/pricing); [AWS Marketplace](https://aws.amazon.com/marketplace/pp/B082FJWR4R/)
- 1NCE relies on roaming partners and publishes a network discontinuation table. I did not see the Cameroon row. — [1NCE help](https://help.1nce.com/docs/connectivity-services/connectivity-services-mobile-network-services/mobile-network-services-discontinuation/)
- Hologram: $0.03/MB, $1/month per SIM, $3 one-time (SIMs activated after 9/30/24). The network list is "selected based on permanent roaming access". Custom coverage goes through sales. — [Hologram pricing](https://hologram.io/pricing/); [Hologram coverage](https://www.hologram.io/coverage/)
- emnify: 540+ networks in 180+ countries. SIM under €2 (commercial) / €3 (industrial) depending on volume (third-party listing). — [emnify](https://www.emnify.com/global-iot-sim/multi-carrier-sim-card); [symb-iot listing](https://iotconnectivitymarketplace.symb-iot.com/providers/emnify)
- Onomondo: data from €0.003/MB, with multiple networks per country in Africa. — [Onomondo Africa](https://onomondo.com/iot-sim/africa)
- Consumer travel eSIMs name MTN, Orange, and Nexttel as partner networks in Cameroon (retail, not IoT). — [simology](https://simology.io/lv/esim-kameruna/)
- SIM identification: Decree no. 2015/3759 (3 Sept 2015) governs identification of subscribers and terminal equipment. ART sanctioned MTN, Orange, and Viettel (2019) for unidentified/pre-activated SIMs. There is a limit of 3 SIMs per operator per subscriber (aimed at individuals). The Cameroon ministry submitted an ITU-T contribution on "Roaming aspects of IoT and M2M" (2023); contents not read. — [Financial Afrik](https://www.financialafrik.com/2019/07/05/cameroun-orange-mtn-et-viettel-sanctionnes/); [Osiris](https://osiris.sn/Cartes-SIM-non-identifiees.html); [ITU](https://www.itu.int/md/T22-SG03RG.AFR-C-0023/en)

### Inferences
- For a meter sending small summaries (e.g., <1 MB/month), the 1NCE flat rate (~$1–1.5/device/year) is the cheapest published option if Cameroon is covered. At 1 MB/month, Hologram costs about $1.03/month, roughly 10× more.
- The 3-SIMs-per-subscriber rule implies that a fleet of thousands of local SIMs needs an enterprise/M2M contract with MTN or Orange.

### Gaps
- MFF2 eSIM chip cost was not found. 1NCE's eSIM/MFF2 is $12 vs $10 (AWS listing), which implies about a $2 premium. That is an inference.
- Which Cameroonian networks 1NCE, Hologram, emnify, Twilio, Soracom, and KORE roam on is not verified. I found no Cameroon rate card.
- I found no source on Cameroon's stance on permanent roaming, or on local MTN/Orange M2M tariffs.

## Q4. Power needs: 2G burst vs Cat.1

### Takeaway
2G SIM800C draws about 2 A transmit bursts, which needs a low-ESR supply and large bulk capacitance. Cat.1 peak figures are less clear. Secondary sources claim up to 3 A for SIM7600, while Quectel staff point to about 1 A maximum peak for EG915U (Cat.1). Luat publishes very low idle/PSM currents (~3 µA PSM+).

### Cited Findings
- SIM800C hardware design: transmit burst about 2 A. VBAT drop is limited to about 350 mV, and a large bulk capacitor is recommended. — [SIM800C HW design p.41-43](https://manualshive.com/en/manual/simcom/sim800c/1274814?page=43)
- Guide claims up to 2 A for SIM800L and up to 3 A for SIM7600 (secondary). — [ThinkRobotics](https://thinkrobotics.com/es/blogs/product-reviews-buying-guides/gsm-modules-guide-sim800l-vs-sim900a-for-iot)
- Quectel forum on EG915U-EU: a 2.5 A figure was clarified by Quectel as average-related, pointing to about 1 A maximum peak. Not verified against the datasheet. — [Quectel forum](https://forums.quectel.com/t/eg915u-eu-clarification-on-lte-current-peaks-and-burst-duration/46610)
- Air780E: about 4.7 mA normal, about 0.47 mA low power, about 3 µA PSM+. — [openluat](https://docs.openluat.com/air780e/)

### Inferences
- A Cat.1 design can likely use a smaller buck/LDO (about 1–1.5 A rated) and less bulk capacitance than 2G (2 A+ with about 1000 µF). This probably saves some tens of cents in the PSU BOM. The saving is not quantified by any source.

### Gaps
- No published Air780E/ML307R VBAT peak-current spec was found. Check the hardware design manuals.
