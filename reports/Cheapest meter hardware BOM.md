# Pitch a six-dollar meter that only listens

The cheapest meter SoundProof can credibly mass-produce for Cameroonian bars costs about **$5.20–7.60 ex-works at 1,000 units and $4.60–6.90 at 10,000**. It has two chips. A $0.16–0.37 microcontroller (PY32F030 or CH32V203) samples a $0.10 electret microphone, runs a small FFT and sends spectral peaks to a $2.2–2.6 China-band LTE Cat-1 modem (Luat Air780EPM or China Mobile ML307R), which forwards them to SONACAM's server. The server does all matching. The "one-chip" alternative, a Luat Air780EHV module with a built-in audio codec running our code, comes to **$5.90–7.80 at 1k**. That is no cheaper, because the codec-equipped module costs ¥22.8 (about $3.21) against ¥16.8 for the audio-less version ([Luat selection guide](https://docs.openluat.com/SelectionGuide/SelectionGuide/)), and nobody has yet shown its SDK doing real-time FFT. A 2G design is the most expensive at **$8.40–11.10**, because the SIM800C module alone costs $5.27 even at 100+ units and is marked obsolete ([LCSC](https://www.lcsc.com/product-image/C2981431.html); [Octopart](https://octopart.com/part/simcom/SIM800C)). Silicon is not the hard part. Cameroon's import taxes multiply the CIF cost by roughly **1.32×** ([LeFisk](https://lefisk.cm/blog/tarif-douane-cameroun-cemac-guide-complet)), so the recommended meter lands at **about $7.30–10.60**. One line in a 2002 ART fee order, read literally, charges **30,000 FCFA (about $50–54) per imported radio unit beyond the first five** ([ART arrêté 00080](https://www.art.cm/sites/default/files/documents/arrete_n00080minefi_minpt_20022002_droits_frais_contributions_redevances_art.pdf)). If that reading holds, the fee alone is several times the price of the device, so SONACAM or the team must get ART's interpretation before ordering stock. All CNY prices in this report are converted at an assumed **7.1 CNY = 1 USD**, and FCFA at **560–600 XAF = 1 USD**. Both rates are our own assumptions, not sourced, and should be recomputed at the current rate.

How to read this report: figures with a link are sourced price snapshots, most of them undated 2024–2026 marketplace listings rather than live quotes. Figures marked *estimate* are our own arithmetic or engineering judgement.

## The pitch in plain words

"Our meter is a small box about the size of a phone charger. It plugs into the wall in a bar. Once a minute it listens for ten seconds, turns what it hears into a short list of numbers describing the loudest notes, and sends that list over the mobile network to SONACAM. It never records or sends sound, so nobody's conversation leaves the bar. All the hard work of recognising the song happens on SONACAM's server. That keeps the box simple: two cheap chips, a microphone, a modem and an antenna. It costs about six dollars to build at a thousand units and about nine dollars once it has cleared Cameroonian customs. It uses roughly 15–30 MB of mobile data a month, and it works on MTN, Orange and CAMTEL 4G. If the power goes out, it keeps what it heard and sends it when power returns. The demo you are watching uses a sandbox that simulates exactly this box: the same ten-seconds-a-minute schedule, the same data packets, the same buffering during outages. When we build the real hardware, the server does not change."

## The box hears, hashes and sends; the server decides

The design rests on a strict split. The **device** samples audio at 8 kHz through the MCU's 12-bit ADC, removes DC, applies a Hann window, runs a 512-point fixed-point (Q15) real FFT every 32 ms, picks the 3–8 strongest local peaks per frame, and either sends those peaks or pairs them into 32-bit landmark hashes. It batches each 10-second window, buffers batches in flash, and hands them to the modem. The **server** does everything else: hash lookup against the catalogue, offset-consistency voting, the play-logger state machine, unknown-track escrow and payout logic. CMSIS-DSP supports Q15 real FFTs of length 512 ([CMSIS-DSP](https://arm-software.github.io/CMSIS-DSP/main/group__RealFFTQ15.html)). Measured Cortex-M0+ cost is about 665,000 cycles for a 1,024-point complex Q15 FFT ([ST community](https://community.st.com/stm32-mcus-products-25/fft-in-stm32f0-94582)). Scaling from that gives *an estimate* of 4–10 million cycles per second for this pipeline: 10–20% of a 48 MHz PY32F030, or about 5% of a CH32V203. The RAM budget is about 5–7 KB, which fits the 8 KB parts and leaves room on the CH32V203. Open-source precedent exists: the Olaf landmark fingerprinter runs on an ESP32 and targets RP2040 boards ([0110.be](https://0110.be/posts/ESP32_Olaf_-_Overly_Lightweight_Acoustic_Fingerprinting_on_the_ESP32)). Olaf is AGPL-licensed, and its README flags the Shazam landmark patents ([Olaf GitHub](https://github.com/JorenSix/Olaf)), so production firmware should be a clean-room implementation, with a legal check on whether those patents have expired.

*Recommendation:* send **raw peaks** (time, frequency bin, magnitude; about 3 bytes each) rather than finished hashes. The device cost is identical, the payload is only slightly larger, and the server can change its hashing scheme without a firmware update. This choice also reopens a tension with the earlier SoundProof research. That research favoured the NMFP neural fingerprinter, which identified 84.6% of 10-second real-venue clips ([arXiv 2506.22661](https://arxiv.org/abs/2506.22661)). A neural model needs spectrogram-like input, and landmark peaks are a lossy substitute. Bar noise is exactly where classic landmarks are weakest. A $0.37 MCU cannot run NMFP, but it can send a coarse log-mel spectrogram. Whether a neural model retrained on such coarse frames beats landmarks is an open question, and the pilot has to measure it.

Duty-cycling at 10 seconds per minute captures one-sixth of the time. That is still enough to catch every 3–4 minute song several times. Assuming 12 operating hours a day for 30 days (360 hours a month, *estimate*), monthly uplink is:

| Payload sent per 10 s window | Rate while capturing | Per window | Per hour | Per month (360 h) |
|---|---|---|---|---|
| Lean landmark hashes (a few hundred bytes per window) | ~30 B/s | ~300 B | ~18 KB | **~6.5 MB** |
| Raw spectral peaks (recommended) | 60–120 B/s | 0.6–1.2 KB | 36–72 KB | **~13–26 MB** |
| Dense landmark hashes (20–50/s) | 100–300 B/s | 1–3 KB | 60–180 KB | ~22–65 MB |
| Coarse log-mel (16 bands, 8-bit, 10 fps) | ~160 B/s | ~1.6 KB | ~96 KB | ~35 MB |
| Log-mel (32 bands, 31 fps) | ~1 KB/s | ~10 KB | ~600 KB | ~216 MB |
| Opus 6–8 kbps audio (rejected on privacy grounds) | 0.75–1 KB/s | 7.5–10 KB | 450–600 KB | ~160–216 MB |

Rates come from the per-hour figures in the research notes, divided by six for duty-cycling. Add *an estimated* 2–5 MB a month for heartbeats and connection overhead if batches go up every 10–15 minutes rather than every window. A bar open 24 hours doubles everything. The practical answer is **15–30 MB a month** for the recommended payload. Duty-cycling is a data-cost choice, not a power-saving one, because the meter runs on mains.

## Three designs end up within a few dollars of each other

The non-silicon parts are the same in every design, and at volume they cost more than the chips. The table gives per-unit costs.

| Shared line | 1k | 10k | Source / confidence |
|---|---|---|---|
| Bundled 5 V/1 A EU-plug USB charger + jack + bulk caps + buck to ~3.8 V | $0.65–1.10 | $0.55–0.95 | Charger $0.68 at 1k ([Alibaba](https://www.alibaba.com/product-introduction/Wholesale-USB-Wall-Plug-5W-1A_1600532584736.html)), ¥3.3 EXW Yiwu ([Yiwugo](https://en.yiwugo.com/product/detail/977159089.html)); rest *estimate*. Medium |
| Bare PCB, 50×50 mm, 2-layer | $0.15–0.30 | $0.10–0.25 | *Estimate* from JLC pricing ([JLCPCB](https://jlcpcb.com/blog/special-discount-on-quality-4-layers-pcbs)); an Aug 2026 notice ended some volume discounts ([JLCPCB](https://jlcpcb.com/news/jlcpcb-fr4-pcb-price-adjustment-august2026)). Medium-low |
| SMT assembly (bulk minimum) + amortised setup | $0.53 | $0.48 | $0.48/board bulk minimum, $0.0016/joint ([JLCPCB](https://jlcpcb.com/help/article/40-pcb-assembly-price)). High |
| Test and programming | $0.10–0.30 | $0.05–0.20 | *Estimate*. Low |
| FPC LTE antenna + IPEX | $0.55–0.75 | $0.30–0.60 | Quectel YPCA004AA $0.55 at 1k ([DigiKey](https://www.digikey.com/en/products/detail/quectel/YPCA004AA/21273118)); Shenzhen direct *estimate*. Medium |
| Stock ABS enclosure (drilled mic port) | $0.35–0.90 | $0.35–0.90 | $0.35 at ≥1k ([Alibaba](https://www.alibaba.com/product-introduction/Small-Plastic-Enclosure-Pcb-Design-Project_1601151534047.html)), $0.89 ([Alibaba](https://www.alibaba.com/product-introduction/Small-Standard-Junction-Box-Housing-Customized_1601157358625.html)). Medium |
| Passives, SIM holder, label, carton | $0.25–0.50 | $0.20–0.38 | *Estimate*. Low |

A custom injection mold costs roughly $4,000–10,000 ([dev.to](https://dev.to/china-sourcing-agents/injection-mold-tooling-costs-for-electronics-enclosures-16dl)) and only breaks even against an $0.89 stock box at around 8,000 units (*estimate*). Its real value is branding, a proper mic port and tamper features, so it belongs in the 10k run, not the 1k run.

### (A) One chip: the Cat-1 module runs our code and hears through its own codec

| Rank | Part | Role | 1k | 10k | Source | Confidence |
|---|---|---|---|---|---|---|
| 1 | Luat Air780EHV | Cat-1 modem + user code (LuatOS/C SDK) + built-in ES8311 codec | $3.21 (¥22.8) | ~$3.00–3.21 (*estimate*) | [Luat selection guide](https://docs.openluat.com/SelectionGuide/SelectionGuide/) | Medium on price; **low** on real-time DSP feasibility and band list |
| 2 | Electret capsule (GMI9767P) | Microphone, wired to the codec input | $0.10–0.20 | $0.10 | $0.0995 at 5k ([LCSC](https://www.lcsc.com/product-detail/C234027.html)) | High |
| 3 | Shared lines above | Power, PCB, assembly, antenna, box | $2.58–4.38 | $2.03–3.76 | See table | Medium |
| | **Total ex-works** | | **$5.90–7.80** | **$5.10–7.10** | | |

The cheaper 4 MB Luat parts cannot be used here, because Luat says they "do not support audio" ([Luat selection guide](https://docs.openluat.com/SelectionGuide/SelectionGuide/)). Design A also stands or falls on whether LuatOS's C SDK can stream raw PCM from the codec into an FFT at 8 kHz. Luat publishes no CPU clock or user-available RAM figures, so this is unverified.

### (B) Two chips: cheap MCU does the listening, cheapest modem does the sending

| Rank | Part | Role | 1k | 10k | Source | Confidence |
|---|---|---|---|---|---|---|
| 1 | Luat Air780EPM **or** China Mobile ML307R-DL | Cat-1 modem, AT commands only (no audio needed) | $2.37 (¥16.8) / $2.55 | ~$2.23 (¥15.8 historical) / $2.55 | [Luat](https://docs.openluat.com/SelectionGuide/SelectionGuide/), [Luat FAQ](https://docs.openluat.com/faq/2026-08-11/); [EBEE](https://www.ebee.com/product-detail/chinamobile-ml307r-dl_12501157.html) | Medium-high |
| 2 | CH32V203G6 **or** PY32F030 | ADC capture, FFT, peaks, buffering, modem control | $0.37 / $0.16–0.27 | similar | [LCSC CH32V203](https://www.lcsc.com/product-detail/Microcontroller-Units-MCUs-MPUs-SOCs_WCH-Jiangsu-Qin-Heng-CH32V203G6U6_C5142280.html); [LCSC PY32F030](https://lcsc.com/datasheet/lcsc_datasheet_2205171716_PUYA-PY32F030F18P6TU_C3018715.pdf) | High on price; FFT cycle counts still to be measured |
| 3 | Electret + bias + preamp (CH32V203 on-chip op-amp, or one NPN) | Microphone front end | $0.12–0.25 | $0.12–0.15 | [LCSC](https://www.lcsc.com/product-detail/C234027.html); op-amp example in [WCH SDK](https://github.com/openwch/ch32v20x/tree/main/EVT/EXAM) | High / medium |
| 4 | Level shifting MCU↔modem UART, optional SPI flash for outage buffer | Glue | $0–0.05 (+ flash, unpriced) | same | *Estimate* | Low |
| 5 | Shared lines above | | $2.58–4.38 | $2.03–3.76 | See table | Medium |
| | **Total ex-works** | | **$5.20–7.60** | **$4.60–6.90** | | |

The low end pairs PY32F030 with Air780EPM. The high end pairs CH32V203 with ML307R. A 12-bit ADC gives roughly 60–66 dB of dynamic range, which is ample in a bar at 80–100 dB SPL. The real analog risk is clipping on heavy bass, so the front end should use low gain (10–20 dB) plus an RC anti-alias filter (*estimate*).

### (C) 2G for comparison

| Rank | Part | Role | 1k | 10k | Source | Confidence |
|---|---|---|---|---|---|---|
| 1 | SIMCom SIM800C | GSM/GPRS modem | $5.27 (100+ tier; no 1k tier found) | ~$5.2 | [LCSC](https://www.lcsc.com/product-image/C2981431.html) | Medium; **obsolete** per [Octopart](https://octopart.com/part/simcom/SIM800C) |
| 2 | Same MCU + mic as (B) | Capture and DSP | $0.28–0.62 | same | as above | High |
| 3 | Extra bulk capacitance for 2 A bursts (≥1,000–2,200 µF or ~1 F supercap) | Burst buffer | $0.30–0.80 | similar | *Estimate*; burst spec in [SIM800 HW design](https://wirenboard.com/wiki/images/a/a7/SIM800_HW_Design.pdf) | Low |
| 4 | Shared lines | | $2.58–4.38 | | | |
| | **Total ex-works** | | **$8.40–11.10** | **~$8–10.5** | | |

Complete 2G trackers sell for $4.10 at 1,000 units ([Alibaba GF-07](https://www.alibaba.com/product-introduction/GF-07-Mini-Small-Tiny-Gps_1601168579982.html)). That proves 2G can be cheap, but only with a chip-down modem SoC, which needs RF design and certification work that is not justified below about 100,000 units (*estimate*). A module-based 2G design costs more than Cat-1 on every line.

## Build design B, and keep A as a prototype experiment

*Recommendation:* pitch and prototype **design B with a CH32V203 and a China-band Cat-1 modem**, with the ML307R and Air780EPM as two interchangeable sources. Design B is cheapest at the low end. Its CH32V203 version costs about the same as design A, and it moves the technical risk to where it is easiest to manage. Bare-metal FFT on a Cortex-M or RISC-V MCU is well documented, while real-time DSP inside a Lua-scripted modem is not. Treating the modem as a plain AT-command pipe makes the radio swappable. The same fingerprint firmware can drive an ML307R, an Air780EPM, an international Cat-1 part or a 2G module, which hedges supply, band and certification risk at once. The CH32V203 costs about $0.20 more than the PY32F030 but buys more than 10 KB of RAM, hardware multiply at a higher clock and an on-chip op-amp that replaces the preamp transistor. With the PY32F030's 6–8 KB, the 5–7 KB RAM budget would be too tight to be comfortable. Design A deserves one prototype spin. If LuatOS's C SDK delivers PCM to an FFT at 8 kHz, a one-chip board saves about 20 placements and one firmware image. It does not save money at today's list prices.

## Mains, a buck converter and capacitors replace the battery

The cheapest robust power path is a bundled EU-plug 5 V/1 A USB charger ($0.45–0.93), which keeps 220 V and its safety testing off our PCB. A small buck converter drops this to about 3.8 V for the modem's VBAT. The ML307R accepts 3.4–4.5 V ([LCSC ML307R](https://www.lcsc.com/product-detail/C48982537.html)). On VBAT sit 470–1,000 µF of low-ESR capacitance, because Cat-1 modules draw roughly 0.5–0.9 A while transmitting ([Quectel forum](https://forums.quectel.com/t/7semi-ec200u-modem-switches-off-at-http-activity-even-if-power-supply-led-is-on/53600)). 2G needs 2 A bursts of 577 µs every 4.6 ms and must hold VBAT above 3.0 V ([SIM800 HW design](https://wirenboard.com/wiki/images/a/a7/SIM800_HW_Design.pdf)), which a 5 V/1 A charger cannot supply without much larger bulk storage. An onboard Hi-Link HLK-PM01 AC-DC module ($1.38 at 1k, [LCSC](https://www.lcsc.com/product-detail/Power-Module-AC-DC_HI-LINK-HLK-PM01_C209903.html)) is the alternative if chargers keep disappearing into bar staff's phones. It is rated only 3 W, costs about $0.80 more, and brings mains safety into the homologation file. A captive cable or an unusual barrel plug on the charger is the cheaper fix (*estimate*).

Store-and-forward makes a battery unnecessary. When the network drops, the MCU keeps capturing and queues peak batches. At about 18–72 KB per hour, the CH32V203's internal flash holds only a few hours, so a small SPI NOR flash is worth adding (unpriced). When mains drops, the music almost certainly stops too. The only thing to protect is the batch currently being written, which takes 10–100 ms at tens of mA, and a few hundred µF already covers that (*estimate*). A "last gasp" message over Cat-1 needs 3–15 J, and a 1–2.5 F supercap is only borderline for that, so it is better to let the server infer outages from missing heartbeats. A 500 mAh LiPo plus TP4056 charger adds $2.1–4.2 ([Goldsupplier](https://www.goldsupplier.com/provide/p171620121.html); [LCSC TP4056](https://lcsc.com/product-detail/Battery-Management_JSMSEMI-TP4056_C22470410.html)), ages badly in hot bars and complicates air freight. It should be an optional SKU at most.

## Customs turn a $6.50 box into about $9 in Douala

Cameroon applies the CEMAC common external tariff (bands of 5/10/20/30%; mobile phones under HS 8517.11 at 10%), plus a **1% TCI** on CIF and **19.25% VAT** on CIF + duty + TCI ([LeFisk](https://lefisk.cm/blog/tarif-douane-cameroun-cemac-guide-complet); [PwC](https://taxsummaries.pwc.com/republic-of-cameroon/corporate/other-taxes)). The formula is: CIF = ex-works + freight + about 0.5% insurance. Landed = CIF × (1 + D + 0.01) × 1.1925, plus about 3–8% of CIF for broker, port and minor levies (*estimate*). At D = 10%, that is about 1.32× CIF before brokerage. The meter's likely heading, HS 8517.62 (data transceivers), is unconfirmed and could fall in the 20–30% band. Sea LCL freight runs about $0.05–0.15 a unit, air about $0.40–0.70 (*estimates*).

| Design (ex-works midpoint/range) | Landed, sea, D = 10% | Landed, air, D = 10% | Landed, sea, D = 30% |
|---|---|---|---|
| B at 10k ($4.60–6.90) | ~$6.50–9.70 | ~$7.10–10.30 | ~$7.60–11.40 |
| B at 1k ($5.20–7.60) | **~$7.30–10.60** | ~$7.90–11.30 | ~$8.60–12.50 |
| A at 1k ($5.90–7.80) | ~$8.30–10.90 | ~$8.90–11.50 | ~$9.70–12.80 |
| C (2G) at 1k ($8.40–11.10) | ~$11.80–15.50 | ~$12.40–16.10 | ~$13.80–18.20 |

If the importer is a VAT-registered company, VAT is recoverable, which brings landed cost down to about 1.16× CIF. Homologation is an extra one-off per model. No published fee schedule was found, so *an estimated* $500–3,000 for dossier, lab reports and translation adds $0.50–3.00 a unit over 1,000 units or $0.05–0.30 over 10,000. The data SIM is the running cost. At 15–30 MB a month, Hologram's published rates of $1 a month plus $0.03/MB come to about **$1.45–1.90 a month** ([Hologram](https://hologram.io/pricing/)). That makes connectivity, not hardware, the largest cost over a meter's life.

## Cameroon's networks favour the cheap China-band modules

Cameroonian 4G runs on **MTN at 2,500 MHz Band 41** and **Orange at 1,800 MHz Band 3** ([Wikipedia](https://en.wikipedia.org/wiki/List_of_LTE_networks_in_Africa)). Orange reportedly added B20 and B41 carriers ([Powertec](https://portal.powertec.com.au/industry-resources/companies/orange-cameroon)), and CAMTEL uses B1 and B3 ([Kimovil](https://www.kimovil.com/en/frequency-checker/CM)). Nexttel has no 4G ([phonetravelwiz](https://www.phonetravelwiz.com/buying-a-sim-card-in-cameroon-guide/)). The cheap China-variant band set of B1/3/5/8/34/38/39/40/41, confirmed for the ML307R ([LCSC](https://www.lcsc.com/product-detail/C48982537.html)) and the original Air780E ([openluat](https://docs.openluat.com/air780e/)), covers all three operators. The "international" Air780EU set of B1/3/7/8/20/28 **lacks B41** ([DFRobot](https://wiki.dfrobot.com/tel0177/)) and would get no 4G on MTN. The cheaper part is therefore also the better fit, an unusual win. The Air780EPM's and EHV's own band lists have not been confirmed, so they should be checked before committing. No NB-IoT or LTE-M deployment exists: Onomondo lists MTN Cameroon (MCC-MNC 62401) for 2G, 3G and LTE only ([Onomondo](https://onomondo.com/coverage/)). Nor is 2G going away. Shutdown dates for MTN and Orange are listed as "unknown" ([Orange Business](https://reseaux.orange-business.com/wp-content/uploads/2024/05/2G-3G-shutdowns-worldwide-planning-20240201-2.pdf)), and ART fined Orange and MTN 2.6 billion FCFA in 2025 over 2G/3G coverage breaches ([Rio Times](https://www.riotimesonline.com/cameroon-telecom-regulator-93-breaches-orange-mtn-camtel/)). The catch is that Cat-1 has no 2G fallback. MTN reports 4G reaching about 60% of the population against 97% for 2G ([ITWeb](https://itweb.africa/article/mtn-cameroon-guns-for-total-network-coverage/nWJadMbN6RPMbjO1)). An urban Douala/Yaoundé pilot suits Cat-1. Rural bars may need the 2G SKU, which design B's swappable modem makes cheap to offer.

For SIMs, Onomondo is the only global IoT provider found that explicitly lists a Cameroonian network, with data from €0.003/MB ([Onomondo Africa](https://onomondo.com/iot-sim/africa)). 1NCE's $10 for 10 years and 500 MB ([1NCE](https://1nce.com/en-us/1nce-connect/10-dollars-for-10-years)) would only last at the lean-hash rate of about 6.5 MB a month, and its Cameroon coverage is unconfirmed. Hologram and emnify publish no Cameroon partner. Cameroon's Decree 2015/3759 requires subscriber identification, and ART has sanctioned operators over pre-activated SIMs ([Financial Afrik](https://www.financialafrik.com/2019/07/05/cameroun-orange-mtn-et-viettel-sanctionnes/)). A limit of three SIMs per subscriber per operator ([Osiris](https://osiris.sn/Cartes-SIM-non-identifiees.html)) means a fleet will need an **enterprise M2M contract with MTN, Orange or CAMTEL**, ideally signed by SONACAM. Permanent roaming on a foreign IoT SIM may not be tolerated. CAMTEL's national roaming on MTN ([MTN](https://www.mtn.com/mtn-cameroon-to-support-camtel-service-with-national-roaming-agreement/)) makes a CAMTEL contract a possible single-SIM route to wider coverage.

## One ambiguous fee could cost more than the meter

Any terminal connecting to Cameroonian networks must be homologated by ART under Arrêté 00000013/MINPOSTEL of 2012 ([ART Recueil 2023](https://art.cm/sites/default/files/documents/Recueil%20des%20textes%20du%20secteur%20des%20Postes%2C%20T%C3%A9l%C3%A9communications%20et%20TIC%20-%20Edition%202023.pdf)). The application requires a receipt for dossier-study fees ([ART form](https://www.art.cm/sites/default/files/documents/DEMANDE%20D%E2%80%99HOMOLOGATION%20D%E2%80%99EQUIPEMENT.pdf)). The fee amounts sit in annex 3 of the 2002 joint order 00080, which could not be extracted. The same order's Article 12 sets ART's technical-reception fee for radio equipment imported for sale: **100,000 FCFA for fewer than five units of a type, and 30,000 FCFA per additional unit** ([ART arrêté 00080](https://www.art.cm/sites/default/files/documents/arrete_n00080minefi_minpt_20022002_droits_frais_contributions_redevances_art.pdf)). If that applies to every meter, it adds $50–54 to a $9 device and breaks the business case. If it means 30,000 FCFA per additional equipment *type*, it is trivial. The same order also prescribes annual radio vignettes (1,500–2,000 FCFA), a 10,000 FCFA penalty per unvignetted terminal, and a licensed-installer requirement for "complex" terminals. Their applicability to an IoT meter is unclear. ART is actively campaigning against non-homologated equipment together with customs ([Ecomatin](https://ecomatin.net/telecoms-les-equipements-non-homologues-dans-le-viseur-de-lart)), and customs checks IMEIs ([Douanes Cameroun](http://www.douanes.cm/douanes-cameroun/8395/dedouanement-des-telephones-tout-sur-les-categories-de-telephones-et-les-droits-de-douane/)), so informal import is not an option. A second, subtler conflict follows from the band analysis. The China-variant modules fit Cameroon's bands best, but they may lack the CE-RED reports that ART dossiers typically rely on, while the internationally certified variants miss MTN's B41. *Recommendation:* use the bundled certified charger to keep mains safety off the board, and get ART's written answer on Article 12 and on CN-variant acceptability before ordering. Ideally SONACAM, as a public-interest body, asks ART directly.

### Risks and open questions to verify

| Risk / question | Why it matters | How to close it |
|---|---|---|
| ART Article 12 per-unit fee (30,000 FCFA) | Could add $50+ per unit | Written ruling from ART; check whether the 2002 schedule was superseded |
| CN-variant module acceptable to ART without CE-RED | Best band fit vs certification | Ask ART; request Luat/China Mobile certificate pack |
| Air780EPM/EHV band lists | B41 needed for MTN | Datasheets; field test on MTN and Orange SIMs |
| Landmark peaks vs neural (NMFP) accuracy in noisy bars | Landmarks may lose 10+ points of recall | Record bar audio; compare peaks, coarse log-mel and NMFP on the same clips |
| FFT cycle count on CH32V203/PY32F030 | Real-time budget is estimated, not measured | Benchmark on a dev board, about 1 day |
| LuatOS PCM-to-FFT feasibility (design A) | Decides whether one-chip is viable | One prototype spin |
| Volume prices for modules at 10k | Only list prices found | Sales quotes from Luat and China Mobile IoT |
| HS code and duty band (10% vs 20–30%) | Swings landed cost by about $1.30 | Broker ruling in Douala |
| Local M2M tariffs and SIM identification rules | Opex dominates lifetime cost | MTN/Orange/CAMTEL enterprise quotes |
| Landmark patent and AGPL exposure | Firmware IP | Clean-room code; legal check on US6990453/US7627477 |
| Chargers removed by staff | Silent outages | Captive cable; heartbeat alarms |

## Conclusion

The research inverts two intuitions. First, 2G is not the budget option. On modules, Cat-1 is now cheaper than GPRS, and China-domestic parts beat "international" ones on both price and Cameroon band fit. Second, the bill of materials is no longer driven by the chips. Silicon (modem, MCU and microphone) comes to about $2.70–3.20 of a $5–8 box, and the charger, PCB assembly, antenna and enclosure take the rest. Over the meter's life, customs, the SIM contract and possibly one regulatory clause outweigh both. A team that can say "the box costs about nine dollars landed, the SIM costs more than that every six months, and here is the regulatory question SONACAM must ask ART" sounds more credible than one quoting a chip price.

The decision to keep the device dumb, sending peaks and letting the server match, is also what makes the hackathon sandbox honest. The simulated meters can emit exactly the payload, schedule and outage behaviour of design B, so every server component built in 48 hours carries over unchanged to real hardware. The one choice the sandbox cannot settle is whether on-device peaks preserve enough information for neural matching in a loud Yaoundé bar. That is the first experiment of any pilot, and it should run before the board is finalised.
