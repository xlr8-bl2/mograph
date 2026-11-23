# Non-silicon costs of a cheap cellular IoT audio meter for Cameroon bars (power, backup, PCB/PCBA, antenna, enclosure, certification/import), 2025–2026

Conventions: **[Q]** = a quoted/listed price from a source (snapshot, usually undated or 2024–2026). **[E]** = my estimate or derivation, not quoted. All USD. FX assumption [E]: 1 USD ≈ 560–600 XAF (XAF is pegged at 655.957 per EUR; the USD rate floats with EUR/USD). Search date: Nov 2026. Most supplier-marketplace prices (Alibaba, LCSC snapshots) carry no date.

---

## 1. Power: cheapest way to run from mains, peak current, backup

### Takeaway
The cheapest robust option is an onboard Hi-Link-style AC-DC module (~$1.38 at 1k on LCSC). A bundled no-name 5V/1A USB wall charger costs about the same ($0.45–0.93 [Q]) and moves mains safety and certification off your PCB, but chargers get stolen or reused in bars. 2G (SIM800-class) needs 2 A bursts, which means bulk capacitance or a Li cell. Cat-1 draws ~0.5–0.9 A during TX. That is easier, but still needs a few hundred µF plus a 1 A-capable rail. A store-and-forward design only needs backup long enough to flush flash and send a "power lost" message. That argues for supercaps or no backup over a LiPo.

### Cited Findings
**AC-DC modules**
- HLK-PM01 (Hi-Link, 220VAC→5V 3W, LCSC C209903): $2.08–2.76 at qty 1, **$1.38 at 1,000+** [Q], with ~2.4k–5k in stock in different snapshots — [LCSC HLK-PM01](https://www.lcsc.com/product-detail/Power-Module-AC-DC_HI-LINK-HLK-PM01_C209903.html)
- HLK-PM01L variant (C19632428): $2.43 at 1+, **$1.44 at 500+** [Q] — [LCSC HLK-PM01L](https://www.lcsc.com/product-image/C19632428.html?whichImg=0)
- HLK-PM01 is also in the JLCPCB parts library (C209903), so JLC can assemble it. It is through-hole, so expect a hand/THT soldering fee — [JLCPCB part detail](https://jlcpcb.com/partdetail/HLK-PM01/C209903)
- Note: the PM01 is rated 3 W (5V/600mA). A 2A@4V GSM burst (~8 W instantaneous) exceeds it, so bursts must be fed from bulk capacitance or a cell. (Rating is from the Hi-Link datasheet listed on LCSC: [datasheet](https://lcsc.com/datasheet/lcsc_datasheet_2409141600_HI-LINK-HLK-PM01_C209903.pdf).)

**USB wall charger (bundled)**
- 5V/1A single-port, CE/FCC, US plug: **$0.68 at 1,000–499,999 pcs**, $0.73 at 500–999 [Q] — [Alibaba listing](https://www.alibaba.com/product-introduction/Wholesale-USB-Wall-Plug-5W-1A_1600532584736.html)
- UL-listed 5V 1A, Dongguan supplier: FOB $0.93 [Q] — [Tradewheel](https://www.tradewheel.com/p/ul-usb-dc-5v-1a-wall-mount-2142979/)
- Multi-certified (CE/ETL/PSE/CCC/IRAM): $1.40 at 1k–9,999, MOQ 3,000 for custom packaging [Q] — [Alibaba](https://www.alibaba.com/product-introduction/Factory-Direct-5V-1A-Commercial-USB_1600344200474.html)
- EU-plug 5V1A, Yiwu EXW: CN¥3.3 (~$0.45 [E]) [Q] — [Yiwugo](https://en.yiwugo.com/product/detail/977159089.html)
- Distributor reference: DigiKey 5V 1A charger $3.53 at 1,000 [Q] — [DigiKey](https://azcus.digikey.com/en/products/detail/my-cable-mart/FE-USB-260/24714281)
- (Cameroon uses Type C/E European plugs. Order the EU-plug version.)

**Peak current**
- SIM800 (2G) hardware design: **2 A burst lasting 577 µs every 4.615 ms**. The reference circuit is a 100 µF tantalum (ESR 0.7 Ω) + 1 µF, giving max 350 mV VBAT drop. VBAT must stay >3.0 V during the 2 A burst — [SIM800 HW Design (SIMCom, hosted by Wirenboard)](https://wirenboard.com/wiki/images/a/a7/SIM800_HW_Design.pdf); [manualslib p.55](https://www.manualslib.com/manual/1803676/Simcom-Sim800.html?page=55)
- Hobbyist guidance (TinyGSM wiki) stresses that GSM modules need a supply able to deliver ~2 A bursts and that weak USB supplies or thin wires cause brown-out resets — [TinyGSM: Powering GSM module](https://github.com/vshymanskyy/TinyGSM/wiki/Powering-GSM-module)
- A third-party blog recommends ~2,200 µF, from C = I·Δt/ΔV with 0.5 V droop. That is not a SIMCom spec — [Zbotic](https://zbotic.in/?p=597845)
- EC200U (Cat-1): a user reports **500–900 mA during LTE data TX** depending on band. No official peak figure was found — [Quectel forum](https://forums.quectel.com/t/7semi-ec200u-modem-switches-off-at-http-activity-even-if-power-supply-led-is-on/53600)
- Low-ESR supercaps are a documented approach for buffering GSM/GPRS pulsed loads — [element14 community](https://community.element14.com/technologies/passive-components/w/documents/7413/why-should-you-use-ultra-low-esr-super-capacitors-in-gsm-gprs-applications); [Analog Devices article on pulsed-load GSM power](https://www.analog.com/en/technical-articles/compact-power-solution-overcomes-peak-power-limitations.html)

**Backup components**
- TP4056 Li-ion charger IC (ESOP-8, 1 A): JSMSEMI **$0.0315 at 1k+**, UMW ~$0.036–0.038 at 1k+ (355k in stock), TDSEMIC **$0.025 at 10k+** [Q] — [LCSC JSMSEMI TP4056](https://lcsc.com/product-detail/Battery-Management_JSMSEMI-TP4056_C22470410.html); [LCSC UMW](https://www.lcsc.com/product-image/C725790.html); [LCSC TDSEMIC](https://www.lcsc.com/product-image/C42422118.html)
- 3.7 V 500 mAh LiPo pouch (802530): **$2–4, MOQ 500** [Q] — [Goldsupplier](https://www.goldsupplier.com/provide/p171620121.html). Alibaba sample price $0.80 [Q], but it is unclear whether that is representative — [Alibaba](https://www.alibaba.com/product-detail/3-7V-500mAh-450mAh-400mAh-300mAh_1600833167115.html). Western distributor: $6.36 at 1,000 (DigiKey 702528) [Q] — [DigiKey](https://www.digikey.com/en/products/detail/us-electronics-inc/USE-702528-500PCBJST/15781479)

### Inferences
- **Cheapest viable power path [E]:**
  - (a) Cat-1 module + HLK-PM01 ($1.38) + ~470–1,000 µF low-ESR electrolytic/polymer on VBAT (~$0.05–0.15 [E]) + a small buck/LDO to 3.8 V (~$0.05–0.15 [E]). Power line ≈ **$1.5–1.7**.
  - (b) Bundled $0.45–0.93 EU USB charger + micro-USB/USB-C jack (~$0.03–0.10 [E]). Power line ≈ **$0.6–1.1**, and no mains on the PCB, which simplifies safety (no 220 V creepage, no fuse/MOV, no IEC 62368 for the main board). Risk: bar staff unplug the charger to charge phones. A molded captive cable or an odd barrel connector helps.
  - (c) The cheapest of all is "bring your own USB charger" with only a cable supplied. That saves ~$0.5 but leaves reliability uncontrolled.
- **With 2G (SIM800-class) [E]:** a 5V/1A charger cannot supply a 2 A burst directly. You need ≥1,000–2,200 µF of low-ESR bulk at VBAT, a ~1 F supercap, or a Li cell as buffer. A Li cell also gives backup. This erodes most of 2G's module-price advantage.
- **How much backup is needed [E]:** with store-and-forward (audio aggregates written to MCU flash every N seconds), a power cut loses at most the un-flushed buffer. To finish a flash write you need about 10–100 ms at tens of mA, which a few hundred µF already gives. To also send a "power-lost" packet over Cat-1 you need roughly 5–20 s at ~0.3–0.9 A, about 3–15 J. A 5.5 V 1 F supercap stores ~15 J max, but only ~5–8 J is usable above a buck's dropout. So 1–2.5 F of supercap is borderline-adequate for a last-gasp message [E]. Supercap price was not found (gap). A 500 mAh LiPo (~$2–4) + TP4056 ($0.03) + protection (~$0.05–0.10 [E]) gives hours of runtime, but costs $2.5–4.5, ages badly in hot bars, and adds shipping/IATA lithium hassle.
- Recommendation for the "dead-cheapest" build [E]: **no battery**. Use bulk caps sized for Cat-1 TX + flash flush, have the server infer outages from gaps (ENEO cuts are visible as offline intervals), and buffer data locally so nothing is lost. Treat a LiPo as an optional SKU.

### Gaps
- No LCSC price found for 5.5 V 1 F supercaps, HLK-PM03 or HLK-5M05, or the cheapest SMD AC-DC controllers (e.g., a chip-down flyback). Chip-down AC-DC may hit ~$0.5–0.8 [E] but adds safety-cert burden.
- No official peak-current figure found for EC200U/Air780E/ML307-class Cat-1 modules. Check the hardware design manuals.

---

## 2. PCB fabrication and assembly (JLCPCB/PCBWay), ~50×50 mm, 1k–10k

### Takeaway
At JLCPCB, the per-board bare-PCB cost for a 50×50 mm 2–4 layer board at 1k+ is in the low tens of cents. Assembly is dominated by a $0.0016/joint placement charge, with a **$0.48/board minimum SMT charge on bulk orders**, plus one-time setup ($8–51) and per-extended-part feeder fees ($1.5–3). Realistic PCBA conversion cost (board + assembly, excluding parts) is **~$0.6–1.2 per board at 1k and ~$0.5–0.9 at 10k [E]**. A module-based design (pre-certified Cat-1 module on an LCC footprint) keeps the board 2-layer with ~40–80 parts. Chip-down cellular is not realistic at 1k–10k.

### Cited Findings
- JLCPCB assembly fees: Economic PCBA setup **$8.18**. Standard PCBA setup **$25.56 single-side / $51.12 double-side**. SMT placement **$0.0016 per joint**. Hand soldering **$3.50 per order** (labor). Feeder loading **$3.07 per extended part (Economic)** or **$1.53 per part (Standard)**. Basic parts and "preferred extended" parts carry no feeder fee on Economic [Q] — [JLCPCB prototype assembly page](https://jlcpcb.com/solutions/pcb-prototype-assembly); [JLCPCB assembly price help](https://jlcpcb.com/help/article/pcb-assembly-price)
- Older figures ($8 setup, $25/$50, $3/extended part, $0.0015/joint) appear in older pages and third-party write-ups — [JLCPCB assembly FAQ](https://jlcpcb.com/help/article/98-pcb-assembly-faqs)
- **Bulk orders: minimum SMT assembly charge of $0.48 per board** [Q] — [JLCPCB assembly price help](https://jlcpcb.com/help/article/40-pcb-assembly-price)
- X-ray inspection is charged for BGA/QFN/hidden-joint packages. Cellular LCC/LGA modules may trigger it — same source.
- JLCPCB board pricing reference: a 100×100 mm, 500 pcs, 4-layer order showed a $353 board charge ($421.10 total) in a March 2024 promo [Q]. That is ~$0.71 per 100 cm² board, so ~$0.18 per 50×50 mm equivalent by area [E] — [JLCPCB 4-layer discount post](https://jlcpcb.com/blog/special-discount-on-quality-4-layers-pcbs)
- An **August 2026** JLCPCB notice says volume orders (including >50 m²) of 1/2/4-layer FR4 are now charged at the standard base rate, which signals an end to some volume discounts (no per-m² figures in snippet) — [JLCPCB FR4 price adjustment Aug 2026](https://jlcpcb.com/news/jlcpcb-fr4-pcb-price-adjustment-august2026)
- Milling surcharge: none below 1,000 pcs; **$15 extra for 1,000–2,000 pcs** [Q] — [JLCPCB extra charges](https://jlcpcb.com/help/article/in-what-cases-will-there-be-charged-extra)
- JLCPCB has "discounts for large PCB orders" programs (details not extracted) — [JLCPCB large-order discounts](https://jlcpcb.com/blog/jlcpcb-discounts-for-large-pcb-orders)

### Inferences
- **Worked PCBA example [E]:** 2-layer 50×50 mm module board, ~60 placements / ~250 joints, single-sided.
  - Placement: 250 × $0.0016 = $0.40, so the $0.48 bulk minimum applies → **$0.48/board**.
  - Bare PCB 2-layer at 1k–10k: ~$0.10–0.25/board [E]. 4-layer: ~$0.20–0.40 [E]. Panelize to cut routing/milling.
  - One-time: $25.56 setup + ~10 extended parts × $1.53 ≈ $41 per production run (<$0.05/board at 1k).
  - THT HLK-PM01 + supercap + battery connector: hand/wave soldering per order plus per-joint THT charges (not quantified; gap). Choosing SMD alternatives avoids it.
  - Total conversion ≈ **$0.65–0.80/board at 1k, ~$0.55–0.75 at 10k**, excluding components. Add **~$0.10–0.30 [E]** for functional test/programming if the CM does it (JLC offers limited test; a local test jig is common).
- 4-layer is only worth it for a chip-down RF layout or a PCB-trace antenna with tight ground. A module + FPC antenna design works on 2-layer [E].
- Chip-down (e.g., a UNISOC 8910DM/ASR1603 Cat-1 SoC directly on the board) needs a 4–6 layer HDI layout, RF calibration and your own full PTCRB/CE/FCC-class certification. That is not justified below ~100k units [E]. The module's price premium (~$1–2) is far cheaper than RF cert + NRE.
- PCBWay is generally comparable to or pricier than JLC for this class. No PCBWay volume quote was obtained (gap).

### Gaps
- No live JLC/PCBWay instant quote for exactly 50×50 mm × 1k/10k. Run the quote tool with Gerbers. The Aug-2026 price adjustment may raise the 2024-based bare-board estimate.
- JLC THT/wave soldering per-joint prices were not retrieved.

---

## 3. Antennas

### Takeaway
An adhesive FPC LTE antenna with an IPEX/U.FL pigtail costs **~$0.55–0.85 at 1k from Western distributors** [Q], and likely ~$0.20–0.40 direct from Shenzhen [E, unverified]. A PCB-trace or spring antenna can cut that to ~$0.02–0.10 but needs RF tuning. An IPEX connector (~$0.03–0.05 [E]) is needed with FPC.

### Cited Findings
- Quectel **YPCA004AA** (FPC, IPEX-1, 100 mm lead): **~$0.55 at 1,000** [Q] — [DigiKey](https://www.digikey.com/en/products/detail/quectel/YPCA004AA/21273118)
- Quectel **YFCA002HA** (IPEX-1, 86.5 mm): **$0.727 at 1,000** [Q] — [DigiKey](https://www.digikey.com/en/products/detail/quectel/YFCA002HA/21272692)
- Pulse **BTPA0061204G0C4A03** (I-PEX adhesive flat patch): €0.60–0.84 at 1,000 depending on DigiKey region [Q] — [DigiKey NL](https://www.digikey.nl/en/products/detail/pulse-electronics/BTPA0061204G0C4A03/23616867); [DigiKey DE](https://www.digikey.de/en/products/detail/pulse-electronics/BTPA0061204G0C4A03/23616867)
- TE/Linx ANT-LPC-FPC-50: HKD 25.37 (~$3.25 [E]) at 1,000 [Q], a premium part — [DigiKey HK](https://www.digikey.hk/en/products/detail/te-connectivity-linx/ANT-LPC-FPC-50/16515474)
- Abracon AFAC5867-S698: £3,172 per 1,000 bag (~£3.17 each), stale stock date [Q] — [RS UK](https://uk.rs-online.com/web/p/gsm-gprs-antennas/2505182)

### Inferences
- Cameroon LTE runs mostly on bands 3/7/20 (MTN/Orange; band data not verified in this research), and 2G on 900/1800. A cheap 698–960 / 1710–2700 MHz FPC covers both [E].
- A bar is an indoor environment with metal fridges and concrete. An FPC stuck to the inside of the enclosure top, away from the AC-DC module, is the safe choice. A PCB-trace antenna on a 50×50 mm board is too short for good low-band (B20/900 MHz) efficiency [E]. Budget **$0.30–0.60 [E]** for the antenna line at 1k–10k.

### Gaps
- No LCSC/AliExpress/Shenzhen factory price found for generic FPC LTE antennas or chip/spring antennas. The $0.20–0.40 figure is an unverified estimate.

---

## 4. Enclosures and tamper evidence

### Takeaway
Off-the-shelf small ABS project boxes cost **~$0.35–1.00 at 1k** [Q]. A custom single-cavity P20 injection mold for a palm-size two-part shell costs **~$1,000–10,000** (typical ~$4–6k) [Q], with per-part costs low enough (~$0.15–0.40 [E]) that a mold pays back somewhere around 3k–10k units versus a $0.9 stock box. At 1k units, use a stock box. At 10k, a simple custom mold is justified. Tamper-evident seals cost a few cents [E].

### Cited Findings
- Small plastic PCB project box: **$0.40 (2–499), $0.38 (500–999), $0.35 (≥1,000)** [Q]. Dimensions/material not stated in the snippet — [Alibaba](https://www.alibaba.com/product-introduction/Small-Plastic-Enclosure-Pcb-Design-Project_1601151534047.html)
- Small customized ABS junction box: $1.01 → **$0.89 at ≥1,000** [Q] — [Alibaba](https://www.alibaba.com/product-introduction/Small-Standard-Junction-Box-Housing-Customized_1601157358625.html)
- HongFa small ABS USB enclosure: $0.98 at 2,000+ [Q] — [Alibaba](https://www.alibaba.com/product-introduction/HongFa-Small-USB-Enclosure-Custom-Electronics_62351057355.html)
- Retail comparison: 2-pack of 61×36×25 mm ABS boxes, $4.96 [Q] — [Walmart](https://www.walmart.com/ip/High-Quality-ABS-Plastic-High-Quality-Instrument-Case-Electronic-Project-Box-Waterproof-Cover-Project-Enclosure-Boxes-100X62X28MM/10573057488)
- Custom molds:
  - Palm-sized two-part enclosure: **$4,000–10,000 single-cavity P20**; $12,000–30,000+ multi-cavity/hardened [Q] (sourcing-agent blog) — [dev.to](https://dev.to/china-sourcing-agents/injection-mold-tooling-costs-for-electronics-enclosures-16dl)
  - Simple single-cavity small molds $1,000–6,000; average $7,000–9,000 [Q] — [RSP](https://rspinc.com/capabilities/plastic-molding)
  - Low-volume tooling $500–8,000 plus $0.50–5.00/part; China 60–80% cheaper than US/EU [Q] — [Haizol](https://www.haizol.com/blog/low-volume-injection-molding)
  - ABS/PC electronics enclosure tooling set **$5,650–5,950, 20–35 day lead time** [Q] — [Global Sources](https://www.globalsources.com/Injection-mold/ABS-1201110256p.htm)
  - Some factories waive or amortize the mold cost at high volumes — [FirstMold](https://firstmold.com/tips/injection-molding-costs/); [Global Sources best practice](https://www.globalsources.com/knowledge/investing-in-an-injection-mold-what-is-best-practice-in-china/)

### Inferences
- **Breakeven [E]:** with a $5k mold, $0.25 per molded shell vs $0.89 stock box, savings are $0.64/unit, so breakeven ≈ 7,800 units. Against a $0.35 stock box, a custom mold never pays back on cost alone. Its value is branding, a mic port, a cable gland/strain relief, a mounting tab and tamper features.
- Stock box + drilled mic hole + a 3 mm cable grommet is the 1k–10k choice [E]. Drilling/labeling adds ~$0.05–0.15 [E] if done by the box supplier.
- **Tamper evidence [E, no sourced price]:** VOID tamper-evident labels over a screw/seam typically cost a few US cents each in bulk, and ultrasonic welding or one-way snap fits are free with a custom mold. Firmware-side tamper detection (case-open switch, light sensor, or simply audio/power anomalies) is arguably more useful.

### Gaps
- No sourced price for VOID/tamper-evident labels or one-way security screws.
- Per-shot molded part cost for this specific size was not quoted (the $0.15–0.40 estimate is mine).

---

## 5. Total cost benchmarks: cheap trackers and DTUs, and what floor they imply

### Takeaway
Complete 2G GSM trackers (GF-07 class, with a battery, enclosure and charger) sell at **$4.10 at ≥1,000 FOB** on Alibaba. That implies a 2G BOM+assembly floor of ~$3–3.5. The equivalent Cat-1 floor is ~$8–12 retail-module-adjusted. Western-listed Cat-1 module prices ($12–22) are far above Chinese domestic prices. A non-silicon BOM (power + PCB/PCBA + antenna + enclosure) of **~$2–3.5 [E]** is achievable at 1k–10k.

### Cited Findings
- **GF-07 mini 2G GSM tracker (complete): $4.50 (2–99), $4.30 (100–999), $4.10 (≥1,000)** [Q] — [Alibaba GF-07](https://www.alibaba.com/product-introduction/GF-07-Mini-Small-Tiny-Gps_1601168579982.html)
- Hackaday (2017): a $9 "GSM Monitor Line GPS Locator" had the same internals as a $6 "A8 mini GSM/GPRS tracker" — [Hackaday](https://hackaday.com/2017/11/12/inside-an-amateur-bugging-device)
- Budget 4G mini magnetic tracker (GF10): $23.73 retail [Q] — (from search snippet; listing on Newegg) [Newegg](https://www.newegg.com/p/0FA-06RJ-000W6)
- JioFind 4G tracker (Reliance Jio JAT6020): ₹1,499 (~$17 [E]) retail with a 1,100 mAh LiPo. The teardown is dated Sept 2026 — [CircuitDigest](https://circuitdigest.com/review/inside-the-jiofind-4g-wireless-gps-tracker-teardown)
- Cat-1 trackers at Western retail: Jimi LL301 $90–134, Teltonika FMC880 ~NZ$180, Valtrack V4 (ESP32-C3 + Cat-1) $150 [Q] — [eBay Jimi](https://www.ebay.com/itm/187697799863); [PB Tech](https://www.pbtech.co.nz/product/NETTTK1029/Teltonika-FMC880-4G-LTE-Cat-1-tracker); [Crowd Supply Valtrack](https://www.crowdsupply.com/valetron-systems/valtrack-v4)
- Module prices (for context; covered by other researchers):
  - SIM800C (2G) LCSC: $7.22 (1), **$5.21 (100+)** [Q] — [LCSC SIM800C](https://www.lcsc.com/product-detail/C2981431.html?s_z=t_TI)
  - Quectel EC200U (Cat-1) Avnet APAC: $22.50 (100+), $20.12 (5,000+), 56-week lead time [Q] — [Avnet](https://www.avnet.com/apac/product/quectel-wireless-solutions/ec200ucnaa-n05-sgnsa/arise-que-queec200ucnaa-n05/). Octopart: $13.79 [Q] — [Octopart](https://octopart.com/part/quectel/EC200UAUAA-N05-SGNSA)
  - Quectel EG915U (Cat-1) Arrow: $12.01–13.02 [Q] — [Arrow](https://www.arrow.com/en/manufacturers/quectel-wireless-solutions-co-ltd/communication/wireless-modems)
  - Luat Air780E: bare module ~$17.72 on eBay (Dec 2025) [Q]. That is Western resale. Chinese domestic pricing was not found — [eBay](https://www.ebay.com/itm/157360021237)
  - Cat-1bis ASPs have reportedly fallen ~22%/yr, driven by Chinese vendors (market-research claim, low reliability) — [Intel Market Research](https://www.intelmarketresearch.com/lte-cat-bis-communication-module-market-5190)
- Digital Matter Yabby (LoRaWAN) teardown lists GNSS $9.55 and LoRa module $9.00, showing how premium Western IoT BOMs split (not Cat-1) — [Electronics360](https://electronics360.globalspec.com/article/14912/teardown-digital-matter-yabby-lorawan-915-location-tracker)

### Inferences
- The GF-07 at $4.10 complete, sold profitably with charger, LiPo, case and a 2G modem chip (likely a chip-down MediaTek MT6261-class SoC rather than a SIM800 module, which is unverified), implies that **non-silicon costs plus 2G silicon fit within ~$3–3.5 [E]** at Shenzhen scale. A module-based design cannot reach that.
- The JioFind 4G at ~$17 retail (subsidized/operator product) suggests a Cat-1 + GNSS + battery BOM under ~$10–12 at million-unit scale [E].
- **Implied floor for this meter [E]:**
  - Cat-1 module: ~$3–6 from Chinese channels (Air780E/ML307-class; other researchers should verify), or $12–20 via Western distributors.
  - MCU: $0 if the code runs on the module (OpenCPU/LuatOS).
  - MEMS mic: ~$0.10–0.30.
  - eSIM/MFF2: covered elsewhere.
  - Non-silicon: power ~$0.6–1.7, PCB+PCBA ~$0.6–0.8, antenna ~$0.3–0.6, enclosure ~$0.35–0.9, passives/connectors ~$0.2–0.4. Total **~$2.0–4.4**.
  - Ex-works total ≈ **$6–11 with a Chinese-channel Cat-1 module**, or $15–25 with a Western-channel module.

### Gaps
- No real teardown of a $4–15 Cat-1 tracker identifying chips and costs. No Chinese RMB prices found for Air780E/ML307R/EC800M (other agents should cover this).
- Smart-meter BOM benchmarks were not found.

---

## 6. Certification (ART Cameroon) and import duties/taxes

### Takeaway
Terminal equipment connecting to Cameroon public networks must be homologated by ART before it can be sold. The legal framework is the 2012 MINPOSTEL arrêté + Decision 054/MINPOSTEL of 2013, and fees are set by the 2002 joint arrêté n°00080 (annex 3, amounts not machine-readable). A separate article of the same arrêté charges **100,000 FCFA (~$170–180) for <5 radio equipments of the same type and 30,000 FCFA (~$50–55) per additional equipment** for ART's technical reception before import of radio equipment by licensed installers. If read as per-unit, that is ruinous at scale, so it needs clarification with ART. Customs: the CEMAC CET has bands of 5/10/20/30%. Mobile phones (HS 8517.11) are at 10%, plus **1% TCI** and **19.25% VAT** on CIF+duty+TCI. That is a cumulative **~32–33% on CIF** for category-II telecom goods [E]. Using a pre-certified module does not remove Cameroon homologation, but it supplies the test reports (CE RED / GCF) ART typically accepts in the dossier.

### Cited Findings
**ART homologation**
- Legal basis: Arrêté N°00000013/MINPOSTEL of 27 June 2012 on homologation modalities for electronic-communications terminal equipment and radio installations. It is compiled in ART's 2023 legal compendium — [ART Recueil 2023](https://art.cm/sites/default/files/documents/Recueil%20des%20textes%20du%20secteur%20des%20Postes%2C%20T%C3%A9l%C3%A9communications%20et%20TIC%20-%20Edition%202023.pdf). Decision n°054/MINPOSTEL of 18 April 2013 fixes homologation conditions (cited in an ART guide; search snippet) — [ART guide](https://www.art.cm/sites/default/files/documents/Guide-procedures-assignation-fr%C3%A9quences-2016-destin%C3%A9-au-public.pdf)
- The ART homologation application form requires a **receipt for payment of "frais d'études" issued by the Agency** — [ART Demande d'homologation](https://www.art.cm/sites/default/files/documents/DEMANDE%20D%E2%80%99HOMOLOGATION%20D%E2%80%99EQUIPEMENT.pdf)
- Arrêté n°00080/MINEFI/MINPT (20 Feb 2002), Art. 17: holders/applicants of homologation certificates pay "les frais d'études du dossier; les frais d'établissement du certificat d'admission temporaire ou de l'attestation d'homologation; les frais d'homologation; les vignettes". **Amounts are in annex 3**, which was not extractable from the PDF. "Complex terminal equipment" may only be sold by a licensed installer (agréé). Homologation fees are collected after a positive dossier study and before issuance — [ART arrêté 00080](https://www.art.cm/sites/default/files/documents/arrete_n00080minefi_minpt_20022002_droits_frais_contributions_redevances_art.pdf)
- Same arrêté, Art. 12: for ART's technical reception of radio equipment to be commercialized (certificate/attestation of homologation for import), the licensed installer pays a flat non-refundable fee: **"Moins de cinq (5) équipements de même type : cent mille (100 000) francs CFA ; Plus de cinq (5) équipements de même type : trente mille (30 000) francs CFA /équipement en sus."** — same source
- Same arrêté, Art. 13: annual radio vignettes cost **2,000 FCFA per fixed-station equipment and 1,500 FCFA per mobile/portable**. Art. 25(d): non-affixing of the vignette by sellers/importers incurs a 10,000 FCFA penalty per terminal. Art. 25(f): an unlicensed seller of telecom equipment is fined 1,000,000 FCFA — same source. (Vignettes appear to target private radio networks, not consumer cellular terminals. Applicability is unclear.)
- ART enforcement: it has announced campaigns against non-homologated equipment (phones, tablets, telematics, Wi-Fi equipment, decoders) and ART–Customs cooperation — [Digital Business Africa](https://www.digitalbusiness.africa/?p=27945); [Ecomatin](https://ecomatin.net/telecoms-les-equipements-non-homologues-dans-le-viseur-de-lart); [Osiris: ART and customs](https://osiris.sn/Cameroun-L-Art-et-la-douane-vont-s.html)
- Comparison: Burkina Faso ARCEP charges ~30,000 FCFA per terminal-equipment homologation (a different country) — [ARCEP BF](https://www.arcep.bf/sp_faq/combien-coute-une-homologation/)

**Customs/taxes**
- CEMAC CET: "Les droits de douane du tarif extérieur commun sont fixés à 5 %, 10 %, 20 % et 30 %". **HS 8517.11 mobile phones: 10% (Category II)**. HS 8471 computers: 10%. **TCI 1%** on CIF for goods from outside CEMAC. **VAT 19.25%** on CIF + duties + TCI — [LeFisk tarif douane Cameroun](https://lefisk.cm/blog/tarif-douane-cameroun-cemac-guide-complet)
- VAT 19.25% = 17.5% + 10% communal additional centimes — [PwC tax summaries](https://taxsummaries.pwc.com/republic-of-cameroon/corporate/other-taxes); [US Trade.gov Cameroon import tariffs](https://www.trade.gov/country-commercial-guides/cameroon-import-tariffs)
- Phones: Cameroon customs applies a flat 33.33% to reference values of 5,000–400,000 FCFA across 8 categories, with IMEI checks via mpie.camcis.cm (2026). This shows the IMEI is visible to customs/ART, and the IoT module's IMEI could be flagged — [Douanes Cameroun](http://www.douanes.cm/douanes-cameroun/8395/dedouanement-des-telephones-tout-sur-les-categories-de-telephones-et-les-droits-de-douane/); [L'Oeil républicain, May 2026](https://loeilrepublicain.info/2026/05/27/telephones-importes-ce-quil-faut-savoir-sur-les-nouveaux-montants-de-dedouanement-au-cameroun/)

**Network context**
- 2G is not being switched off. In 2025, 12.2% of the population lacked 2G coverage vs 25% lacking 4G/5G (ITU). Orange's 2G coverage of 91.5%/90.1% is below its 95% licence obligation (July 2025). MTN reported 2G 97%, 3G ~88–90%, 4G 60–70% (older/conflicting figures) — [search summary of ITU/press](https://www.osiris.sn/telecommunications-le-reseau-4g-de-mtn-couvre-pres-de-60-du-territoire.html); [MTN Cameroon](https://www.mtn.com/mtn-cameroon-to-support-camtel-service-with-national-roaming-agreement/); [Cameroon Tribune sanctions](https://www.cameroon-tribune.cm/articles/10382/fr/details_2)

### Inferences
- **Landed cost formula [E]:** Landed = CIF × (1 + D) × ... more precisely:
  1. CIF = FOB (device ex-works incl. packaging) + freight + insurance (~0.5% of value).
  2. Duty = D × CIF, with D = 10% if classed with telecom apparatus (HS 8517.62 "machines for reception/transmission of data" is the likely heading; the rate was not confirmed — it could be 10% or 20–30%).
  3. TCI = 1% × CIF.
  4. VAT = 19.25% × (CIF + Duty + TCI). VAT is recoverable if the importer is a VAT-registered company reselling or using it for taxed services.
  5. Add customs broker/transitaire, port/handling and other small levies (inspection, CEMAC community levies, OHADA levy, IT fee; amounts not found here). Budget **3–8% of CIF [E]**.
  6. Add ART homologation as a one-off (fixed per model). Budget ~$500–3,000 including lab-report translation and dossier costs [E, no fee schedule found]. If the Art. 12 fee of **30,000 FCFA per unit** applies to every imported unit, it would add ~$50+ per device, more than the device. Clarifying this is a must-do before scaling.
  - Rough multiplier [E]: with D = 10%, CIF × (1.10 + 0.01) × 1.1925 ≈ **CIF × 1.32**, plus brokerage → **~1.35–1.40× CIF**. With D = 30%: ≈ 1.56× CIF.
- **Freight [E]:** a 10k-unit batch of ~60 g devices is ~600–800 kg gross. Air freight China→Douala at ~$5–8/kg gives ~$0.4–0.7/unit. Sea LCL is cheaper (~$0.05–0.15/unit) but takes 6–8 weeks. LiPo-equipped devices complicate air freight (UN3481 rules), which is another argument for a battery-less SKU.
- **Pre-certified modules [E]:** module CE-RED/GCF/FCC certs cover radio conformance and are normally reused in a host-device homologation dossier, but they do not exempt the end product from ART homologation. The end product still needs safety (if mains-powered onboard) and EMC reports. Powering from an already-certified USB charger avoids mains-safety testing of the main PCB. Choose a module whose certificates include CE RED, as Cameroon tends to accept CE/international reports.
- Alternatives that may reduce the burden [E]: buy modules locally via a licensed ART installer/distributor, or import the finished device through a partner that already holds an "agrément installateur". Very low-cost IoT devices are often imported informally, but ART's IMEI enforcement push makes that risky for a business whose fleet relies on SIMs from MTN/Orange.

### Gaps
- **ART annex-3 homologation fee amounts were not obtained** (PDF annex not text-extractable). Whether the 2002 schedule still applies or has been superseded (e.g., by a post-2010 law or a 2013+ decision) is also unknown. Contact ART (Direction des Équipements/Homologation) or get the 2023 Recueil annex.
- The exact CEMAC rate for HS 8517.62 (data transceivers/IoT DTUs) and HS 8543 was not confirmed. Precise amounts of other import levies (PSV/inspection, CCI, OHADA 0.05%, redevance informatique) were not found.
- Whether ART requires local testing or accepts foreign lab reports only, and the typical timeline: not found.

---

## Summary cost table (per unit, ex-works China, excluding cellular module/MCU/mic/SIM)

| Line | 1k units | 10k units | Basis |
|---|---|---|---|
| Power (USB charger bundled + jack + bulk caps) | $0.65–1.10 | $0.55–0.95 | [Q] charger $0.45–0.93; rest [E] |
| Power alt. (onboard HLK-PM01 + caps + buck) | $1.5–1.7 | $1.4–1.6 | [Q] $1.38 @1k; rest [E] |
| Backup: none / supercap 1–2.5 F / LiPo 500 mAh + TP4056 | $0 / $0.3–0.8 [E] / $2.1–4.2 | similar | LiPo $2–4 [Q], TP4056 $0.03 [Q] |
| Bare PCB 50×50 2-layer | $0.15–0.30 | $0.10–0.25 | [E] from JLC data |
| SMT assembly | $0.48 min + setup amortized ~$0.05 | $0.48 | [Q] $0.48 bulk minimum |
| Test/programming | $0.10–0.30 | $0.05–0.20 | [E] |
| Antenna (FPC + IPEX) | $0.55–0.75 (distributor) | $0.30–0.60 | [Q] $0.55–0.73 @1k; factory [E] |
| Enclosure (stock ABS) | $0.35–0.90 | $0.35–0.90 (or custom ~$0.25 + $5k mold) | [Q] |
| Passives/connectors/label/box | $0.20–0.40 | $0.15–0.30 | [E] |
| **Non-silicon subtotal (no battery)** | **~$2.4–4.3** | **~$2.0–3.7** | [E] |
| Freight to Douala | air $0.4–0.7 / sea $0.05–0.15 | same | [E] |
| Duty + TCI + VAT | ×~1.32 on CIF (D=10%) | same | [Q] rates; [E] calc |
| ART homologation | fixed per model, amount unknown; possible per-unit 30k FCFA risk | — | [Q] arrêté 00080 |
