# Cheapest MCUs, microphones and minimal on-device audio-fingerprint DSP for a venue music meter (2025–2026)

Research date: 2026-11-23. All prices in USD. LCSC prices come from search-indexed snapshots of LCSC/JLCPCB pages, not live checks, and most snapshots are undated. Treat them as roughly 2024–2026 indicative prices. "1k" means the 1,000+ tier unless stated otherwise. Items marked **[UNVERIFIED]** come from my background knowledge or a single weak source and need checking.

---

## Q1. Which are the cheapest MCUs with enough RAM/CPU for real-time 8–16 kHz FFT plus audio input?

### Takeaway
The cheapest realistic parts are 48–144 MHz Cortex-M0+/M4/RISC-V chips in the $0.15–$0.60 range at 1k volume, sampling an analog mic through their built-in 12-bit ADC with timer and DMA. Examples are the PY32F030 (~$0.16–0.27), STM32G030 (~$0.45), and CH32V203 (~$0.37–0.46, with an on-chip op-amp). A 512-point FFT ~31 times/s needs only ~5–10 M cycles/s and ~4–8 KB of RAM, so a 48 MHz M0+ with 8 KB of SRAM is sufficient. The sub-$0.12 parts (CH32V003 with 2 KB of RAM and no hardware multiply; PY32F002A with 3 KB of RAM) are too small. Parts with Wi-Fi radios (ESP32-C2/C3) or the RP2040 cost more in total BOM and add nothing, because the cellular module provides connectivity.

### Ranked MCU table (cheapest viable first)

| Rank | MCU | Core / clock | SRAM | Audio input path | Price (source) | Can do 512-pt FFT @31/s + peaks? |
|---|---|---|---|---|---|---|
| — (too small) | WCH CH32V003 | RV32EC, 48 MHz | 2 KB | 10-bit ADC; no I2S | $0.1046–0.1308 @2.5k; SOP-8 $0.1059 @4k ([LCSC snapshot via search](https://www.lcsc.com/search?q=ch32v003)); "<10 cents" vendor claim with no quantity given ([CNX](https://www.cnx-software.com/2022/10/22/10-cents-ch32v003-risc-v-mcu-offers-2kb-sram-16kb-flash-in-sop8-to-qfn20-packages/)) | **No / marginal.** 2 KB of RAM can't hold a 512-pt Q15 FFT (2 KB alone). RV32E**C** has no M extension **[UNVERIFIED from memory]**, so multiplies are software and slow. A 128–256-pt FFT might fit, with poor resolution. |
| — (too small) | Puya PY32F002A | Cortex-M0+, 24 MHz **[UNVERIFIED]** | 3 KB | 12-bit ADC | from $0.0888; $0.1115 @500 ([LCSC](https://www.lcsc.com/product-detail/Microcontroller-Units-MCUs-MPUs-SOCs_PUYA-PY32F002AL15S6TU_C5292060.html), [Jay Carlson](https://jaycarlson.net/2023/02/04/the-cheapest-flash-microcontroller-you-can-buy-is-actually-an-arm-cortex-m0/)) | **Marginal.** 3 KB fits a 256-pt FFT only. |
| 1 | Puya PY32F030 (F18/F28) | Cortex-M0+, 48 MHz | 6–8 KB (sources conflict by part number) | 12-bit ADC + DMA | PY32F030F18P6TU $0.1582 @2976+; F28P6TU $0.27 @1008+ ([LCSC datasheet/search](https://lcsc.com/datasheet/lcsc_datasheet_2205171716_PUYA-PY32F030F18P6TU_C3018715.pdf)) | **Likely yes** with Q15 512-pt real FFT (~4–5 KB working RAM; see the budget below). Cheapest viable part. Tight on RAM, and the toolchain/CMSIS-DSP port is less mature. |
| 2 | WCH CH32V203 (F6/G6/K8/C8) | RV32IMAC (QingKe V4B), 144 MHz **[UNVERIFIED clock from memory]** | 10–20 KB (64 KB on RB) **[UNVERIFIED]** | 12-bit ADC + DMA; **on-chip op-amp** (WCH's EVT has an `OPA/OPA_Voltage_Follower` example) ([openwch/ch32v20x](https://github.com/openwch/ch32v20x/tree/main/EVT/EXAM)); I2S status disputed (below) | G6U6 $0.3727 @1k; C8T6 $0.4578 @1k; K8T6 from $0.38; F6P6 $0.3841 @72+ ([LCSC C5142280](https://www.lcsc.com/product-detail/Microcontroller-Units-MCUs-MPUs-SOCs_WCH-Jiangsu-Qin-Heng-CH32V203G6U6_C5142280.html), [LCSC C3001172](https://www.lcsc.com/product-detail/C3001172.html)) | **Yes, comfortably.** It has hardware multiply and a high clock. The on-chip op-amp may replace the external electret preamp transistor/op-amp. Best value. |
| 3 | ST STM32G030 | Cortex-M0+, 64 MHz | 8 KB | 12-bit 2.5 Msps ADC + DMA; SPI (no I2S **[UNVERIFIED]**) | K6T6TR $0.4469 @1k; K8T6 $0.46–0.62 @1k; C8T6 $0.53–0.63 @1k ([LCSC search snapshots](https://www.lcsc.com/product-image/C431631.html)) | **Yes.** This is a mature CMSIS-DSP target. |
| 4 | Raspberry Pi RP2040 | 2× Cortex-M0+, 133 MHz | 264 KB | PIO can do I2S/PDM; 12-bit ADC (500 ksps) | $0.70 on a 3,400-unit reel, $0.80 on a 500-unit reel, $1.00 single ([Raspberry Pi / CNX, Mar 2025](https://www.cnx-software.com/2025/03/18/buy-raspberry-pi-rp2350-mcu-rp2354a-and-rp2354b-variants/)); RS: £0.719 @500–2000 ([RS](https://uk.rs-online.com/web/p/microcontrollers/2211610)) | **Yes, easily.** It needs external QSPI flash plus a 12 MHz crystal, so the real BOM is higher **[UNVERIFIED adder ~$0.15–0.35]**. Olaf fingerprinting targets RP2040 boards ([0110.be](https://0110.be/posts/ESP32_Olaf_-_Overly_Lightweight_Acoustic_Fingerprinting_on_the_ESP32)). |
| 5 | Raspberry Pi RP2350A/B | 2× Cortex-M33 (DSP+FPU) or Hazard3 RISC-V, 150 MHz | 520 KB | PIO I2S/PDM | RP2350A $0.80 / $0.90 / $1.10 (3.4k reel / 500 reel / single); RP2350B $0.90 @2,500 ([CNX](https://www.cnx-software.com/2025/03/18/buy-raspberry-pi-rp2350-mcu-rp2354a-and-rp2354b-variants/), [Raspberry Pi](https://www.raspberrypi.com/news/rp2350-now-available-to-buy/)) | Yes, with large headroom. RP2354 variants have integrated flash, which removes the external flash chip. |
| 6 | Espressif ESP32-C2 (ESP8684) | RV32, 120 MHz, Wi-Fi/BLE | 272 KB **[UNVERIFIED]** | I2S? **[UNVERIFIED; C2 may lack I2S]** | ESP8684H4X $0.9459 @100 ([LCSC](https://www.lcsc.com/product-image/C48929155.html)); 44¢ @1k at 2022 launch ([Hackster](https://hackster.io/news/espressif-s-cost-reduced-esp32-c2-hits-the-market-as-the-esp8684-dev-board-now-available-4825af1e9a68)) | Yes, but the radio is wasted here and you pay for a 26/40 MHz crystal and RF layout. |
| 7 | Espressif ESP32-C3 | RV32IMC, 160 MHz, Wi-Fi/BLE | 400 KB | I2S + 12-bit ADC | $1.14 @1k bare (needs external flash); ESP32-C3FH4 $1.64 @1k ([LCSC C2838500](https://www.lcsc.com/product-detail/C2838500.html), [JLCPCB](https://jlcpcb.com/partdetail/C2838500)) | Yes, easily. Olaf runs on the ESP32 ([Olaf GitHub](https://github.com/JorenSix/Olaf)). Too expensive for this role. |
| 8 | ST STM32G431 | Cortex-M4F, 170 MHz | 32 KB | ADC, SAI/I2S | RBT6 $1.28 @1k ([LCSC C431633](https://lcsc.com/datasheet/lcsc_datasheet_1912111437_STMicroelectronics-STM32G431RBT6_C431633.pdf)) | Overkill. |

Not priced in this pass (gaps): CH32V307/CH32V305 (have I2S per the WCH V30x datasheet; likely ~$1.5–2.5 **[UNVERIFIED]**), CH32X035, GD32F1/GD32VF103, Artery AT32F403A/F415, Bouffalo BL602/BL616, ESP32-S3/C6, and Nuvoton. Background expectation: AT32F415 and GD32VF103 sit around $0.6–1.2 at 1k, and BL602/BL616 around $1 **[all UNVERIFIED]**. None beats the CH32V203 or PY32F030 for this job.

### Cited findings
- CMSIS-DSP Q15 real FFT supports lengths including 512 ([CMSIS-DSP docs](https://arm-software.github.io/CMSIS-DSP/main/group__RealFFTQ15.html)).
- Reported on Cortex-M0+: a 1024-point complex Q15 FFT takes about 664,531 cycles, versus about 89,839 on Cortex-M4 ([ST community](https://community.st.com/stm32-mcus-products-25/fft-in-stm32f0-94582)). A fixed-point FFT of unstated length took ~136,296 cycles on M0+ ([ST community](https://community.st.com/s/question/0D50X00009XkhK9/fft-in-stm32f0)).
- A 128-point `arm_rfft_q15` on an STM32F051 (M0, 48 MHz) took 1.367 ms ([ST community](https://community.st.com/stm32-mcus-products-25/fft-in-stm32f0-94582)). This looks slow, possibly an unoptimized build.
- A 1024-point Q15 FFT takes ~70,000 cycles on a Cortex-M4 with CMSIS-DSP ([applied-ee](https://applied-ee.github.io/embedded/docs/audio-projects/audio-dsp/fft-spectral-analysis/)).
- The CH32V003 has no I2S port. A community example drives an audio DAC through SPI plus timer plus DMA instead ([ch32v003fun](https://github.com/cnlohr/ch32v003fun/blob/329585447621c53a472ac8ff20011583cbf30dde/examples/spi_dac/README.md)).
- **CH32V203 I2S is disputed.** One search summary of the V203 datasheet claimed the pin table lists SPI2/I2S2 and SPI3/I2S3, with "3*SPI(2*I2S)" ([WCH datasheet copy](https://static.chipdip.ru/lib/375/DOC030375584.pdf)). However, WCH's official CH32V20x example tree has SPI, ADC, DMA and OPA folders but **no I2S folder** ([openwch/ch32v20x](https://github.com/openwch/ch32v20x/tree/main/EVT/EXAM)). From memory, I2S is a CH32V30x feature only **[UNVERIFIED]**. I2S is confirmed for the CH32V303/305/307 family ([WCH V30x datasheet](https://static.chipdip.ru/lib/257/DOC042257987.pdf)).
- The CH32X035 datasheet describes a PIOC (programmable protocol I/O controller) for single- and two-wire protocols. No I2S is listed ([LCSC CH32X035 datasheet](https://wmsc.lcsc.com/wmsc/upload/file/pdf/v2/lcsc/2404070952_WCH-Jiangsu-Qin-Heng-CH32X035G8U6_C7437027.pdf)).
- Olaf (landmark fingerprinting) runs on the ESP32 with an INMP441 mic. Its author says it is also compatible with RP2040-based devices ([0110.be](https://0110.be/posts/ESP32_Olaf_-_Overly_Lightweight_Acoustic_Fingerprinting_on_the_ESP32)).

### Inferences (my calculations, not sourced)
- **CPU budget at 8 kHz with a 512-pt FFT and hop 256 (31.25 frames/s):** A 512-pt real FFT costs about one 256-pt complex FFT plus a split step. Scaling from the ~664k-cycle 1024-pt complex figure on M0+ gives roughly 130–300k cycles per frame, or ~4–10 M cycles/s. That is 10–20% of a 48 MHz M0+ and ~5% of a 144 MHz CH32V203. Magnitude (|re|+|im| or squared magnitude, with no sqrt), peak picking and hashing add an estimated <1 M cycles/s. At 16 kHz the cost doubles, which is still fine on a ≥48 MHz part. **Real-time is feasible on any ≥48 MHz core with a single-cycle 32-bit multiply.**
- **RAM budget, Q15:** DMA ping-pong input of 2×256×2 B = 1 KB; FFT in/out buffer 512×2 B plus complex output ~2 KB; window table 512 B (can live in flash); magnitude spectrum 257×2 B = 0.5 KB; a peak-history ring for landmark pairing (e.g., the last ~32 frames × 5 peaks × 3 B) ≈ 0.5 KB; hash output buffer for 10 s ≈ 0.5–1 KB; stack and UART driver for the modem ~1 KB. **Total ≈ 5–7 KB.** 8 KB parts (PY32F030 8 KB variants, STM32G030) are just enough. The CH32V203 (≥10 KB) is comfortable. 2–3 KB parts are not enough at 512 points.
- **Audio input without I2S:** PDM mics can be read with any SPI peripheral in master-receive mode. SCK serves as the PDM clock at ~1–1.024 MHz, with DMA into a buffer and software CIC/FIR decimation down to 8 kHz. That costs a few M cycles/s more. On sub-$0.50 MCUs the cheapest path is still an **analog mic into the 12-bit ADC**, triggered by a timer at 8 kHz with DMA.
- **"No separate MCU" option:** Many Cat-1/Cat-M1/NB-IoT modules (e.g., Quectel/Fibocom/MediaTek/ASR/UNISOC-based "OpenCPU" modules) run customer code on a Cortex-A or Cortex-M core with tens to hundreds of KB of RAM. Some have a 12-bit ADC or a PCM/I2S codec interface for voice. The DSP load estimated above (≈5–10 M cycles/s, <10 KB RAM) is small enough for such a core. This could remove the $0.16–0.46 MCU entirely. Feasibility depends on SDK access, ADC sample-rate limits and a PCM interface. **Not deep-dived; covered by the cellular researcher.**

### Gaps
- No published cycle count for a 512-pt `arm_rfft_q15` on M0+, or for any FFT on the CH32V203 or PY32F030. It should be measured on the target.
- CH32V203 I2S presence is unresolved (sources conflict). It needs checking in the CH32FV2x_V3x reference manual.
- No prices gathered for GD32, AT32, BL602/BL616, Nuvoton, ESP32-S3/C6 or CH32V307.
- Mouser single-unit vs volume prices weren't captured for most parts; LCSC tiers were used instead.

---

## Q2. Which microphones are cheapest, and are they adequate for music fingerprinting?

### Takeaway
The cheapest mic is an **analog electret capsule at ~$0.10 (5k) to $0.21**. With a one-transistor or on-chip op-amp preamp into the MCU's 12-bit ADC, it adds only ~$0.02 of passives. Bars are loud (roughly 80–100 dB SPL), so little gain is needed and the 100 Hz–4 kHz band is easily covered. Analog/PDM MEMS run ~$0.20–0.45, and I2S MEMS ~$0.89–1.78. I2S mics are only worth it if the MCU or modem has an I2S port and you want zero analog front-end.

### Ranked microphone table

| Rank | Part | Type | Price | Notes |
|---|---|---|---|---|
| 1 | INGHAi GMI9767P-60DB (LCSC C234027) | Analog electret, 9.7 mm through-hole, -60 dB sensitivity | $0.2004 @5+, **$0.0995 @5k** ([LCSC](https://www.lcsc.com/product-detail/C234027.html)) | Cheapest found. Needs a bias resistor and a preamp. Through-hole adds assembly cost. |
| 2 | INGHAi GMI6027P-2C-40db (C233903) / GMI6027 family | Analog electret, 6 mm | $0.211 @150+ ([LCSC](https://www.lcsc.com/product-detail/C233903.html)) | Volume price likely ~$0.10–0.15 **[UNVERIFIED]**. |
| 3 | Zilltek ZTS6011E (C481291) | Zilltek MEMS (analog vs PDM unconfirmed) | $0.2498 @1, $0.1995 @10 ([LCSC datasheet](https://www.lcsc.com/datasheet/lcsc_datasheet_2003161905_ZILLTEK-ZTS6011E_C481291.pdf)) | An SMD, reflow-friendly alternative to electrets. Datasheet fetch failed, so type and specs are unconfirmed. Zilltek ZTS6018: $0.2387 @10 ([LCSC](https://www.lcsc.com/datasheet/lcsc_datasheet_2205051816_ZILLTEK-ZTS6018_C2999749.pdf)). |
| 4 | MEMSensing MSM261DHP006 (C22390138) | Likely PDM digital (MSM261D naming) **[UNVERIFIED]** | $0.5749 @10, **$0.3909 @1k** ([LCSC](https://www.lcsc.com/product-detail/C22390138.html)) | MSM261D3526Z1CM is discontinued at LCSC ([LCSC datasheet](https://www.lcsc.com/datasheet/lcsc_datasheet_2101041805_MEMS-MSM261D3526Z1CM_C966937.pdf)). MSM261DGT003: $0.4535 @1k ([LCSC](https://www.lcsc.com/product-detail/C48227730.html)). |
| 5 | TDK InvenSense ICS-43434 | I2S MEMS, 24-bit | LCSC $0.8907 @100+ ([LCSC/JLCPCB](https://jlcpcb.com/partdetail/ICS-43434/C5656610)); DigiKey $3.27 @1 to ~$2.00 @250 ([DigiKey](https://www.digikey.com/en/products/detail/tdk-invensense/ICS-43434/6140298)); Mouser $1.78 at the top tier per aggregator ([Octopart](https://octopart.com/part/invensense/ICS-43434)) | Highest quality and simplest digital path, but needs I2S on the host. |
| 6 | CUI CMM-4030D-261-I2S-TR (C3171849) | I2S MEMS | $4.0071 @1, $1.4705 @1100+ ([LCSC](https://lcsc.com/product-detail/MEMS-Microphones_CUI-CMM-4030D-261-I2S-TR_C3171849.html)) | |
| — | MEMSensing MSM261S4030H0(R) | I2S MEMS | No LCSC chip price found. DFRobot SEN0327 module is AUD 8.40 ([Core Electronics](https://core-electronics.com.au/catalog/product/view/sku/SEN0327)) | The module price is irrelevant for mass production. |
| — | INMP441 | I2S MEMS (TDK) | No bare-chip price found. Widely sold on ~$1–3 hobby breakouts **[UNVERIFIED]**; believed EOL/NRND at TDK **[UNVERIFIED]** | Avoid for new designs. Use ICS-43434 or Chinese I2S MEMS. |
| — | LinkMEMS LMD4030T261 (C7587900), INGHAi GSA4030H10 | Chinese MEMS (I2S/PDM variants) | Listed on LCSC; prices not captured ([LCSC](https://lcsc.com/product-detail/MEMS-Microphones_LinkMems-LMD4030T261-OA1_C7587900.html)) | Gap. Possibly cheaper than TDK. |

### Cited findings
- Electret capsules: GMI9767P-60DB is $0.0995 at 5k ([LCSC](https://www.lcsc.com/product-detail/C234027.html)), and GMI6027P is $0.211 at 150+ ([LCSC](https://www.lcsc.com/product-detail/C233903.html)).
- The WCH CH32V20x family has an on-chip op-amp (OPA example in the official SDK) ([openwch/ch32v20x](https://github.com/openwch/ch32v20x/tree/main/EVT/EXAM)). It could serve as the electret preamp.
- The CMM-4030D-261 I2S datasheet listing shows a 100 Hz–10 kHz response with -26 dBFS sensitivity on a DigiKey part listing ([DigiKey](https://www.digikey.com/short/zdfjf1)), which covers the needed band.

### Inferences
- **Can a 12-bit ADC plus electret and transistor/op-amp do the job?** Probably yes. A 12-bit ADC has ~10–11 ENOB, or ~60–66 dB of dynamic range. Landmark and chroma fingerprints depend on relative spectral peaks, not absolute SNR, so 40–50 dB of in-band SNR is plenty in a noisy bar. The bigger risk is **clipping at high SPL** (a 100+ dB SPL bass-heavy venue), so design for low gain (~10–20 dB) and/or a software AGC. A first-order RC anti-alias filter (fc ≈ 3.5 kHz) at 8 kHz sampling is advisable, or oversample at 16–32 kHz and decimate in software.
- **Estimated mic plus front-end BOM:** electret $0.10 + bias/coupling R/C + one NPN or on-chip OPA ≈ **$0.12–0.15**. Analog MEMS (SMD, reflowable, more consistent) ≈ $0.20–0.25. A PDM MEMS at ~$0.39 plus SPI decimation removes the analog front-end. An I2S MEMS at $0.9–1.8 is only justified if the host lacks a good ADC.
- Electrets need through-hole or leaded assembly unless SMD electret variants are used. That may outweigh the ~$0.10 saving against an SMD analog MEMS at volume.

### Gaps
- Sensitivity/SNR datasheet figures for Zilltek and MEMSensing parts weren't retrieved (the fetch failed).
- Volume (10k) pricing for analog MEMS wasn't captured.
- No measured comparison of electret plus MCU ADC against MEMS for fingerprint match rate.

---

## Q3. What is the minimum on-device pipeline that preserves server-side matching quality? Data per hour and compute

### Takeaway
On-device **spectral-peak landmark hashing** (Shazam/audfprint/Olaf style) or a **Chromaprint-style 32-bit-per-frame code** cuts uplink to roughly 0.1–1 MB/h. Both are computable on an 8 KB, 48 MHz MCU. Sending compressed audio (Opus at 6–8 kbps) costs ~3–3.6 MB/h, needs a much heavier encoder, and has privacy implications because speech is recoverable. Log-mel frames fall in between (~0.5–3.6 MB/h) and preserve the most flexibility for server-side re-processing. Chromaprint is built for full-track or stream ID, not short noisy snippets, so landmarks are the safer choice for a noisy bar.

### Comparison table (estimates)

| Payload | On-device compute | Data rate (continuous) | Per hour (continuous) | Robustness in a noisy bar | Privacy |
|---|---|---|---|---|---|
| Opus 6–8 kbps (music mode) | MDCT codec, high complexity, likely too heavy for M0+ **[UNVERIFIED]** | 6–8 kbps | ~2.7–3.6 MB | Server can run any matcher | **Poor:** speech recoverable |
| Codec2 | Speech-only vocoder; destroys music timbre | 0.7–3.2 kbps | 0.3–1.4 MB | **Bad for music** **[UNVERIFIED but well known]** | Poor |
| IMA ADPCM, 8 kHz, 4-bit | Trivial | 32 kbps | ~14.4 MB | Good | Poor |
| Log-mel, 32 bands × 8 bit × 31 fps | FFT + mel weights | ~1 KB/s | ~3.6 MB | Good; server can derive peaks | Medium: speech is hard but not impossible to reconstruct |
| Log-mel, 16 bands × 8 bit × 10 fps (decimated) | Same | ~160 B/s | ~0.58 MB | Moderate | Better |
| Landmark hashes (~20–50 hashes/s × 4–6 B hash+time) | FFT + peak pick + pairing | ~100–300 B/s | ~0.36–1.1 MB | **Best known for noisy short snippets** | Good: not invertible to audio |
| Landmarks at the brief's target (a few hundred bytes per 10 s) | Same, fewer peaks | ~30 B/s | ~0.1 MB | Moderate to good | Good |
| Chromaprint (32-bit word per ~0.124 s frame) | FFT 4096 @11025 Hz + chroma + filters | ~32 B/s | ~0.115 MB | Weak for short noisy snippets | Good |

Duty cycling (e.g., 10 s captured every 60 s) divides all rates by ~6.

### Cited findings
- Chromaprint resamples to mono 11025 Hz, uses 4096-sample frames (0.371 s) with 2/3 overlap, maps the spectrum to 12 chroma bins, and turns the chroma image into 32-bit integers via a sliding filter. It is compared by bit error rate ([Essentia tutorial](https://essentia.upf.edu/tutorial_fingerprinting_chromaprint.html); [Lalinský, "How does Chromaprint work?"](https://oxygene.sk/2011/01/how-does-chromaprint-work/)). The ~8 words/s figure is derived from the hop (my arithmetic).
- AcoustID/Chromaprint identifies whole songs, not short snippets ([Wikipedia: AcoustID](https://en.wikipedia.org/wiki/AcoustID)). This is a limitation for a bar meter.
- Olaf (Overly Lightweight Acoustic Fingerprinting) is an open-source landmark system that runs on embedded platforms (ESP32 tested; some Teensys and Arduinos targeted), traditional computers and the browser. It defaults to 16 kHz, keeps embedded reference fingerprints in a compiled header, and is **AGPL-3.0**. The README warns that patents US7627477 B2 and US6990453 (Shazam landmark patents) may limit use ([Olaf GitHub](https://github.com/JorenSix/Olaf); [0110.be](https://0110.be/posts/ESP32_Olaf_-_Overly_Lightweight_Acoustic_Fingerprinting_on_the_ESP32)).
- **Embedded audio ML on M0+:** an eGRU keyword/acoustic-event model runs on Cortex-M0+ in Q15 fixed point, using FFT-vector inputs rather than MFCCs ([Dartmouth/Odame](https://sites.dartmouth.edu/odame/?p=460)). MFCC extraction takes 431 µs per frame on an MCXN947 Cortex-M33 ([arXiv 2506.08911](https://arxiv.org/pdf/2506.08911)). "Hello Edge" established KWS on Cortex-M with MFCC front-ends ([arXiv 1711.07128](https://arxiv.org/pdf/1711.07128)). End-to-end KWS with MFCC has been benchmarked on STM32 N6/H7/U5 ([arXiv 2509.07051](https://arxiv.org/html/2509.07051v1)).
- An audio-derived key generation scheme (TREVOR) ran on a Cortex-M4 SAME54 at 50 MHz in ~3.2 s per key ([arXiv 2311.04433](https://arxiv.org/pdf/2311.04433)). This is evidence of non-trivial audio feature work on low-end Cortex-M.

### Inferences
- **Recommended minimum pipeline:** 8 kHz ADC → DC removal → Hann window → 512-pt Q15 real FFT, hop 256 (32 ms) → squared magnitude in ~4 kHz/16 Hz bins → log2 approximation → pick local maxima in time-frequency neighbourhoods, ~3–8 peaks/frame decimated to ~10–30 peaks/s → pair each anchor with 3–5 targets in a fan-out zone → 32-bit hash (f1:9b, Δf:6b, Δt:6b, plus anchor time) → ~4–6 B per hash → batch per 10 s and send via modem UART. Fits ~5–7 KB RAM and ~10–20% of a 48 MHz M0+ (Q1 estimates).
- **Hedge:** sending **raw peaks** (time, freq bin, magnitude: ~3 B per peak, ~20–40 peaks/s ≈ 60–120 B/s) instead of hashes lets the server choose the hashing scheme later. On-device cost is the same and the payload is barely larger. This preserves flexibility if the server algorithm changes.
- **Patent/licensing risk:** the Shazam landmark patents are cited as a caution by the Olaf README. US6990453 was filed around 2001 and is likely expired by 2026 **[UNVERIFIED; needs legal check]**. Olaf's AGPL licence makes it unsuitable to ship in closed firmware without a commercial licence or a clean-room implementation.
- Audfprint (Dan Ellis, Python) is the canonical open reference for the server-side landmark matcher. Its defaults are believed to be ~11 kHz, n_fft 512, and a density of ~20 hashes/s **[UNVERIFIED; not fetched]**.

### Gaps
- No peer-reviewed paper found that implements landmark fingerprinting specifically on Cortex-M0+ or the CH32/PY32 class. Olaf on ESP32 is the closest open evidence.
- No measured match-rate vs. bytes-per-10 s curve was found for bar-noise conditions. It needs an in-house experiment, e.g., replaying recordings through audfprint at varying hash densities.
- Opus encoder cycle cost on M0+/RISC-V was not verified.
- Audfprint default parameters were not fetched.

---

## Summary BOM estimate (inference)
- **Cheapest viable, separate MCU:** PY32F030 (~$0.16–0.27) or CH32V203G6 (~$0.37) + electret ($0.10) + ~$0.03 of passives/transistor (or the CH32V203's on-chip OPA) → **≈ $0.30–0.50** for audio capture plus fingerprinting, excluding the cellular module, power and PCB.
- **No-MCU option:** run the same DSP on a cellular module's OpenCPU core with the electret into the module's ADC (if it is fast enough) or a PCM codec input. This saves ~$0.16–0.46. Feasibility is for the cellular researcher.
