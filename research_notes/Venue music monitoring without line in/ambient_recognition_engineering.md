# Ambient (microphone-based) music recognition in noisy venues: engineering best practices and best-performing methods

Scope note: these notes build on earlier findings (Nikou & Giannakopoulos 2025 real-world protocol, landmark weakness under tempo change, Google Now Playing sequence agreement) and add 2025–2026 results. The most relevant new evidence is (a) NMFP (ISMIR 2025, UPF + BMAT), evaluated on real microphone recordings from bars, nightclubs and concert halls; (b) POLARIS (arXiv Sept 2026), with a real smartphone re-recording benchmark comparing Panako, audfprint, Olaf and NMFP; (c) music-foundation-model fingerprints (MuQ/MERT, NeurIPS 2025 workshop); (d) PeakNetFP and pretrained Conformers (2025) for tempo robustness and efficiency.

## 1. Which fingerprinting methods are most robust to real re-recording, crowd noise, reverb and DJ tempo/pitch changes, and which are open source?

### Takeaway
On real venue microphone recordings, the best published open-weights system is NMFP (neural, 8 kHz, 1 s windows / 0.5 s hop, triplet loss). It reaches 84.6% top-1 track ID on 10 s queries from bars, clubs and concert halls, against 76.3% for the official NAFP and 82.5% for GraFP at a 100 ms hop. On a smartphone re-recording benchmark, the new training-free POLARIS (84.6%) beats NMFP (74.3%), audfprint (61–67%), Olaf (24%) and Panako 2.1 (4.8%). Neither handles tempo or pitch shifts by design. For DJ-style tempo/pitch changes, the music-foundation-model fingerprints (MuQ fine-tuned) and PeakNetFP are the strongest published options. Classic Panako is not competitive on real acoustic re-recordings.

### Cited Findings
**NMFP (Araz, Cortès-Sebastià, Molina, Serrà, Serra, Mitsufuji, Bogdanov, ISMIR 2025; UPF/BMAT/Sony AI)**
- Real-world test set: "microphone recordings captured using smartphones and digital recorders in various settings, including bars, nightclubs, and concert halls". It has 3,692 query–reference pairs, with ground truth from a commercial fingerprinting system verified by human annotators. Synthetic test: 10,000 query tracks against a database of 95,163 full-length FMA tracks (53.6M fingerprints). — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Final track-level top-1 hit rate on **real-world venue recordings** at 1 / 2 / 5 / 10 s queries — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661):
  - NMFP: 63.9 / 75.0 / 82.0 / 84.6%
  - NAFP-LAMB (official weights): 42.0 / 58.6 / 71.4 / 76.3%
  - NAFP-Adam: 33.6 / 49.9 / 65.4 / 71.7%
  - GraFP at 500 ms hop: 20.0 / 42.5 / 62.0 / 69.4%; GraFP at 100 ms hop: 46.6 / 60.8 / 74.8 / 82.5%
- On synthetic data with 0–10 dB SNR noise plus room and mic IRs, NMFP scores 86.6 / 91.7 / 94.5 / 95.6%. NAFP-LAMB scores 73.8 / 83.9 / 90.4 / 92.7%. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Segment-level ("near" match, within one hop) on synthetic data: NMFP 75.6 / 83.5 / 89.2 / 92.0% at 1/2/5/10 s. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- The gap between synthetic and real results remains large: at 2 s, NMFP drops from 91.7% to 75.0%. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Code, pretrained weights and data are released at github.com/raraz15/neural-music-fp. The authors say NMFP, NAFP and GraFP are the only real-valued neural AFP models with public code and weights; NowPlaying, CULAF and ABFP have none. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661); [GitHub neural-music-fp](https://github.com/raraz15/neural-music-fp)
- The authors argue that 100 ms hops (GraFP, ABFP) hurt scalability. A 100 ms hop over their database would need 268M fingerprints instead of 53.6M. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

**POLARIS (J. Li, Vanderbilt, arXiv 2609.14820, Sept 2026): training-free saliency landmarks with Delaunay triplets**
- SD-RR real re-recording benchmark: 496 openly licensed Song Describer tracks and 1,488 queries of 10 s each. Audio was played from MacBook Pro built-in speakers and recorded on an iPhone 14 Pro Max about 1.6 m away, 460 tracks indoors (apartment) and 36 outdoors, with chirp-aligned ground truth. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- Top-1 track accuracy on SD-RR real re-recordings (track+offset in brackets) — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820):
  - POLARIS-A/F: 84.61% (75.3 / 76.2%)
  - POLARIS-O: 79.57%
  - NMFP pretrained: 74.33% (71.17%)
  - audfprint query-boosted: 67.20%; audfprint matched payload: 61.02%
  - Olaf 2.0.10: 24.40%
  - Panako 2.1: 4.84% (Panako returned no match for 832 of the 1,488 queries)
- On PEX Hard Medium (synthetic, excluding pitch and tempo shifts), NMFP is best at 98.99%, ahead of POLARIS at 94.06% and audfprint at 90.64%. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- Reference payload: POLARIS 1.89 MiB per hour of audio vs NMFP 3.54 MiB/h. Query time on an M2 Max CPU: POLARIS-A 0.30 s and NMFP 0.33 s per 10 s query. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- Limitations stated by the author: POLARIS "does not model global pitch or tempo changes"; evaluation was closed-set with one laptop and one phone. Code and dataset are at github.com/JihengLi/POLARIS. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- Ablation: using plain spectrogram-magnitude peaks instead of saliency maxima drops SD-RR accuracy from 84.6% to 62.0%. Landmark selection is the main lever for classic systems under acoustic re-recording. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)

**Music-foundation-model fingerprints (Singh et al., SoundPatrol/Cornell, NeurIPS 2025 workshop)**
- Setup: MuQ, MERT or BEATs backbone with a 2-layer MLP projection to 256 dimensions, trained contrastively on 300k Disco-10M samples. Evaluation used 5,000 FMA references and 1,000 queries of 10 s each (track level), plus the Pexeso hard benchmark (segment level). — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)
- Top-1 track-level hit rates (%) — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399):

  | Model | Time stretch | Pitch shift | Time+pitch | Noise | Reverb | Reverb+noise | Band-pass | Encodec | Overall |
  |---|---|---|---|---|---|---|---|---|---|
  | MuQ fine-tuned | 96 | 94 | 87 | 97 | 100 | 90 | 63 | 96 | 88.2 |
  | GraFPrint | 58 | 97 | 67 | 90 | 84 | 80 | 15 | 17 | 67.8 |
  | NAFP | 39 | 91 | 55 | 84 | 86 | 78 | 18 | 10 | 63.5 |
  | Dejavu (Shazam-like) | 25 | 91 | 12 | 71 | 80 | 52 | 9 | 3 | 49.2 |

- Augmentation ranges: time stretch 0.7–1.5, pitch ±5 semitones. — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)
- Pexeso hard-medium segment-level F1 scores — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399):
  - Track F1: MuQ 87.3, GraFPrint 81.3, NAFP 80.6, Dejavu 73.2
  - Bounding-box F1 (boundary precision): MuQ 74.7 vs Dejavu 58.4
- Caveats from the authors: foundation models are weak on band-pass and high-pass filtering, and NAFP wins on low-pass (96 vs 74). Foundation-model scores declined from the small to the medium database while NAFP and GraFP improved, an open scalability concern. The paper does not appear to release weights; I found no release link. — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)

**PeakNetFP (Cortès-Sebastià et al., ISMIR 2025)**
- A PointNet++-style network over the top 256 mel-spectrogram peaks per 1 s segment, producing a 128-dimensional embedding with a 0.5 s hop. It has about 169k parameters, roughly 100x fewer than NeuralFP's 16.9M. — [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1)
- Over 98% top-1 at 10 s for time-stretch factors 0.7–1.4, and above 90% across 0.5–2.0. Queries of at least 5 s are needed for stable results at extreme stretches. The authors' QuadFP reimplementation reaches only 3.6% at factor 0.5. — [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1)
- Trained and tested only on time stretching, not on noise or reverb. Code, model and the time-stretch test set (Zenodo 15646861) are open. — [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1); [GitHub peaknetfp](https://github.com/guillemcortes/peaknetfp)

**Pretrained Conformers (Altwlkany et al., 2025)**
- Three sizes (1.5M / 8.8M / 26.2M parameters), with 3 s queries at 16 kHz. Results for the large model with 3 s queries — [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2):
  - Reverb (MIT IR Survey): 96.2% top-1
  - Background noise at 5 / 10 / 15 dB SNR: 93.9 / 97.1 / 97.8%
  - Time stretch: roughly on par with PeakNetFP
- Code and models are at github.com/KemalAltwlkany/pretrained-conformers. — [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2)
- GraFPrint reports 98.3 / 99.3 / 99.6% at 5 / 10 / 15 dB with 3 s queries on a different subset. NMFP's independent re-test, however, found GraFP much weaker on real venue audio (see above). — [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2); [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

**Panako 2.x and Olaf (classic, Joren Six)**
- Panako 2.0 (ISMIR 2021 late-breaking paper) uses ratios between event points. These stay constant under uniform tempo change and survive pitch shifts, and robustness improved over Panako 2014 on time-stretched, pitch-shifted, sped-up and filtered queries. — [ISMIR 2021 LBD](https://archives.ismir.net/ismir2021/latebreaking/000039.pdf); [GitHub Panako](https://github.com/JorenSix/Panako)
- PeakNetFP's related-work section notes that fewer than a third of Panako queries were resolved after an 8% stretch. Panako is described as unsuitable for short queries or extreme stretching. — [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1)
- Olaf is a lightweight C library (AGPL-3.0) that runs on ESP32 and other embedded boards and in WebAssembly (about 75 KB). On FMA-full (100k tracks), the index is about 15 GB and queries run at about 80x real time. Its README warns that patents US7627477 B2 and US6990453 may apply. — [GitHub Olaf](https://github.com/JorenSix/Olaf)

**Earlier Nikou & Giannakopoulos (arXiv 2507.06070) result, for context**
- The paper introduces a real-world evaluation protocol and reports that accuracy drops significantly when audio is captured on mobile devices in noisy environments. — [arXiv 2507.06070](https://arxiv.org/abs/2507.06070)

### Inferences
- For a catalogue of tens of thousands of tracks played as full recordings in bars, the strongest evidence-backed default is a neural fingerprint trained the NMFP way (open code and weights, real-venue validation). A well-designed sparse landmark method (POLARIS) is a credible, cheaper complement or fallback. Running both and fusing scores is a reasonable engineering bet but untested.
- Classic Panako/Olaf should not be the primary engine for over-the-air capture. Their SD-RR accuracy (4.8% / 24.4%) is far below audfprint, NMFP and POLARIS. Panako's tempo-invariance only helps if landmarks survive the acoustic channel.
- If DJs pitch or tempo-shift tracks (common in clubs, less so in bars playing from phones or YouTube), train with time-stretch and pitch augmentation (0.7–1.5, ±5 semitones, as in the MuQ paper). A foundation-model backbone may be needed. NMFP and POLARIS were not designed for tempo or pitch changes.
- Real-venue numbers (around 75% at 2 s, 85% at 10 s per query) are per-query. Play-level detection over a 3–4 minute song with many queries and temporal voting should be much higher (see section 2). That is an inference, not a published bar-specific figure.

### Gaps
- No public benchmark tests all leading methods on the same real bar or club recordings. The BMAT venue dataset used by NMFP is not described as public, and SD-RR was recorded in an apartment from laptop speakers, not a loud venue PA.
- No published real-venue results were found for MuQ/MERT fingerprints, PeakNetFP or the Conformer models.
- No independent re-evaluation of POLARIS was found; it is a single-author preprint.
- Exact Panako 2.0 numbers appear only in figures in the ISMIR 2021 LBD paper, so they were not extracted.

## 2. Training/augmentation strategies for room/mic robustness, and test-time strategies

### Takeaway
Realistic degradation simulation matters more than architecture. Four changes together added 8.3 points on synthetic data and 20.9 points on real venue data in NMFP's 1 s results: diverse scene noise, full-length room IRs applied with past-context reverberation, measured microphone IRs, and a lower 160 Hz frequency cutoff. Removing false negatives from batches contributes on top, and triplet loss beats NT-Xent. At test time, use longer windows (5–10 s or more), dense overlapping embeddings, and sequence/offset-consistency voting in the style of Now Playing.

### Cited Findings
**NMFP ablation, top-1 on real venue data at 1 / 2 / 5 / 10 s** — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

| Step | 1 s | 2 s | 5 s | 10 s |
|---|---|---|---|---|
| Baseline (NAFP) | 33.6 | 49.9 | 65.4 | 71.7 |
| + better degradation datasets | 39.4 | 56.2 | 70.5 | 76.1 |
| + remove false negatives | 45.5 | 64.8 | 77.3 | 81.9 |
| + full IRs (not truncated to 75 ms) | 49.3 | 66.7 | 78.5 | 82.6 |
| + past-context reverberation | 50.6 | 68.1 | 79.2 | 83.0 |
| + 160 Hz lower cutoff instead of 300 Hz | 54.5 | 70.7 | 81.0 | 84.3 |

- Degradation datasets used by NMFP — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661):
  - TUT Acoustic Scenes 2016: 15 scenes, 585 min train / 195 min test
  - Room IRs: OpenAIR (143 IRs, 28 environments), AIR (60 IRs), MIT IR Survey (270 IRs)
  - Microphone IRs: Franco et al., 25 microphones and 38 mic/polar-pattern combinations, measured at 0.5 / 1.25 / 5 m and multiple angles
  - Rooms and microphones are kept disjoint between train and test.
- Degradation chain: additive noise at 0–10 dB SNR, then room IR, then mic IR, with random gain. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- "Past reverberation": convolve with 1 s of preceding audio, then discard it, so the segment contains reverb tails of earlier events. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Lower frequency cutoff: the authors argue that far from the speakers, bass is what survives crowd noise. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Loss comparison (real data, 1 s / 2 s): triplet loss with hard-positive / semi-hard-negative mining (margin 0.5) scores 63.4 / 75.1%. NT-Xent scores 58.7 / 72.3%; DCL, A&U and KCL are worse. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Batch construction: multiple positives per anchor hurt NT-Xent but help triplet loss slightly (512 anchors, 2 positives each, is best). Training takes about 15 h on one RTX 4090. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- Training offset between anchor and positive: ±250 ms (50% of the hop). — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

**Other augmentation recipes**
- Pretrained Conformers — [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2):
  - Noise: ESC-50 and MS-SNSD background noise plus colored noise
  - Reverb: MIT IR Survey
  - Other: pitch shift, polarity inversion, tanh distortion, time stretch, SpecAugment ("crucial to prevent overfitting")
  - Beta-distributed temporal offsets (0–150 ms), reported as "crucial" for misalignment robustness
- MuQ fingerprints — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399):
  - Noise: MUSAN (restaurant/home/street) plus Aachen RIRs
  - Filters: band-pass 300–1800 Hz, high-pass, low-pass
  - Echo at 100–200 ms delays
  - Encodec low-bitrate codec artifacts
- Google Now Playing trained its fingerprinter with triplet loss on "a dataset of noisy music segments which are aligned to the corresponding segment in their reference song". These are real aligned clean/degraded pairs, which NMFP notes are costly to gather. — [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958); [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

**Test-time strategies**
- Query length is the biggest lever. NMFP on real venue data rises from 63.9% at 1 s to 84.6% at 10 s; on synthetic data PeakNetFP needs at least 5 s for extreme stretches. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661); [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1)
- Now Playing emits one 96-dimensional embedding per second from 8 s buffers (seven overlapping 2 s windows). Sound Search doubles the density to one 128-dimensional embedding every 0.5 s. — [Google Research blog](https://research.google/blog/googles-next-generation-music-recognition/); [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958)
- Two-stage matching in Now Playing:
  1. Per-embedding approximate nearest-neighbour search (tree plus quantization) touches about 2% of the database and yields top-K candidates.
  2. Full candidate sequences are retrieved and scored with an adaptive similarity that uses local database density. This beats a naive L2 sum on precision/recall.
  - Quantized fingerprints are about 32x smaller than float, and each song takes under 3 KB.
  - The correct song matches "every embedding", while false positives typically match only one or two. — [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958); [Google Research blog](https://research.google/blog/googles-next-generation-music-recognition/)
- Sound Search uses popularity-weighted thresholds: lower for popular songs, higher for obscure ones. — [Google Research blog](https://research.google/blog/googles-next-generation-music-recognition/)
- NAFP/NMFP retrieval runs a FAISS top-20 search per query fingerprint, builds candidate sequences by offset, and scores them by average similarity. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- POLARIS uses adaptive query-side expansion. It only adds extra two-hop query fingerprints when the first pass is not confident: at least 8 matching hashes and a margin of at least 0.40 between the top two scores. This cuts query time by 56% with no accuracy loss on SD-RR. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)

### Inferences
- If SoundProof fine-tunes, it should reproduce NMFP's recipe and add three things:
  - local bar noise: crowd chatter in French, English and Pidgin, football TV, generator hum, street noise
  - IRs measured in a few real Cameroonian bars, using a sine sweep played through the venue PA
  - its own mic's measured response
- Recording a few hours of aligned clean/degraded pairs in real bars (Now Playing-style supervision) would both validate and fine-tune the model.
- For a monitoring box with no user waiting, use long effective queries: continuous fingerprints, aggregating evidence over 10–30 s or more before committing a detection. Require offset-consistent matches; the reference offset should advance about 1 s per second of wall clock.

### Gaps
- No paper was found that quantifies the gain from multi-window voting over a whole song in real venues; published numbers are per query of 10 s or less.
- No public set of bar/club IRs from West Africa (or anywhere) was found.

## 3. Microphone hardware and placement; AGC/noise-suppression pitfalls; sampling rate

### Takeaway
Peer-reviewed evidence on microphone choice for fingerprinting is thin. The actionable points are:
- disable OS and driver processing: AGC, noise suppression and echo cancellation
- capture at 16 kHz or more (neural models use 8–16 kHz internally)
- preserve bass down to about 150 Hz
- place the mic near and on-axis with a main speaker, away from the crowd

Train on, or calibrate to, the actual mic's response.

### Cited Findings
- **Android audio sources:**
  - `UNPROCESSED` (API 24+) is "tuned for unprocessed (raw) sound if available, behaves like DEFAULT otherwise". Support should be checked via `AudioManager.getProperty(PROPERTY_SUPPORT_AUDIO_SOURCE_UNPROCESSED)`.
  - `VOICE_COMMUNICATION` "will for instance take advantage of echo cancellation or automatic gain control", so avoid it.
  - [Android MediaRecorder.AudioSource](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource)
- **Android VOICE_RECOGNITION:** the CDD says devices must disable noise reduction and AGC by default for this source, and should keep a roughly flat (±3 dB) response from 100–4000 Hz. Platform docs say not to enable noise suppression for VOICE_RECOGNITION. — [AOSP pre-processing docs](https://source.android.com/docs/core/audio/implement-pre-processing); [Android 12 CDD 5.4](https://android.googlesource.com/platform/compatibility/cdd/+show/refs/tags/android-platform-12.1.0_r10/5_multimedia/5_4_audio-recording.md)
- **Raspberry Pi / ALSA:** some USB mics expose an "Auto Gain Control" boolean in the ALSA mixer next to capture volume; others expose no capture controls at all. Controls can be listed with `amixer -c <card> contents` and set with `amixer`. These sources are forum-level, not vendor docs. — [gist ALSA mixer dump](https://gist.github.com/anonymous/9aab06e25badc2cd9a11); [Home Assistant forum](https://community.home-assistant.io/t/atr4697-usb-mic-needs-alsamixer-to-set-auto-gain-control-how-to-on-home-assistant-os-on-pi5/824339); [alsa-ucm-conf issue](https://github.com/alsa-project/alsa-ucm-conf/issues/308)
- **Sampling rate:** NMFP and NAFP run at 8 kHz, GraFP at 16 kHz, and CULAF, ABFP and the Conformers at 16 kHz. NMFP's authors argue that high sampling rates are unnecessary because "humans can typically identify music even under low-pass filtering". Running GraFP on upsampled 8 kHz audio hurt it badly, so the capture rate must match the model's training rate. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661); [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2)
- **POLARIS analysis band:** uses 40 kHz audio and a 0.78–5.47 kHz analysis band. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- **Distance and microphone type:** NMFP trains with microphone IRs from 25 mics (several polar patterns) measured at 0.5, 1.25 and 5 m. This indicates distance and polar pattern are meaningful variables, but per-mic accuracy is not reported. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

### Inferences
- **Capture settings.** Capture 16-bit at 16 kHz or 44.1/48 kHz, then resample to the model's rate. Fix the gain manually so loud PA peaks don't clip; clipping and AGC pumping both distort spectral peaks. A small software limiter or normalizer applied after capture is fine.
- **Placement.** Mount the box 1–3 m from, and pointed at, the main speaker, above head height and away from the bar counter and crowd, so the music-to-chatter SNR is maximized. A cardioid USB mic pointed at the speaker plausibly helps compared with an omni MEMS mic picking up the room. Beamforming arrays such as ReSpeaker are designed for speech and usually ship with voice-oriented DSP (AGC/NS/AEC) that should be bypassed. These are engineering inferences, not benchmarked.
- **Phones as capture devices.** Use `UNPROCESSED` where supported, falling back to `VOICE_RECOGNITION`. Avoid `VOICE_COMMUNICATION`.

### Gaps
- No peer-reviewed comparison of MEMS vs USB condenser vs directional mics, or of beamforming arrays, for music fingerprinting accuracy was found.
- No published accuracy-vs-distance curve for real venues was found.

## 4. Source separation / speech suppression preprocessing: does it help?

### Takeaway
Evidence says separation (for example Demucs-style removal of speech or noise) substantially helps classic peak-based fingerprinting, but gives no substantial benefit to neural fingerprints, which already learn noise invariance. It also adds heavy compute. For a neural pipeline on edge hardware, skip it. For a landmark pipeline, it may be worth testing on a server.

### Cited Findings
- A study in Multimedia Tools and Applications (2025) evaluated 13 source-separation algorithms with five fingerprinting models on TV broadcast recordings (BAF dataset). It found substantial gains for peak-based AFP, particularly for background music, and that "NeuralFP shows no substantial benefit from adding a separation model as preprocessing". — [UPF repository record](https://repositori.upf.edu/items/ca89891c-f07a-4488-90b3-762aae96a53a/full); [GitHub sepafp](https://github.com/guillemcortes/ssafp)
- The systems evaluated there were audfprint, Panako/Olaf 2.1 and NeuralFP; the code is Apache-2.0 with separation checkpoints of about 600 MB. — [GitHub sepafp](https://github.com/guillemcortes/ssafp)
- A 2023 Deezer preprint reports that a model which cleans spectrograms before peak extraction improves common fingerprinting systems, including under noise. — [arXiv 2310.13388](https://arxiv.org/pdf/2310.13388.pdf)

### Inferences
- Source separation models with checkpoints of several hundred MB are impractical on a Raspberry Pi 3/4 in real time. If used at all, run them server-side on uploaded audio only for unresolved segments.
- Cheap spectral gating or high-pass filtering is unlikely to help a neural model trained with noise augmentation, and could create a train/test mismatch. Test it before adopting.

### Gaps
- Exact numeric gains from the 2025 separation study were not extracted; only the abstract-level conclusion was available.
- No study of separation for crowd chatter in bars, as opposed to TV speech, was found.

## 5. DJ mixes and overlapping tracks; play start/end, duration and deduplication

### Takeaway
Segment-level matching with temporal regression is the established way to localize plays and handle concatenated or overlapping tracks. MuQ-based fingerprints with Huber regression of reference time against query time achieved the best Pexeso segment F1. DJ-mix identification remains hard for landmark systems. Play logging should be built as an offset-consistent tracking layer on top of per-window matches.

### Cited Findings
- Segment-level localization for queries concatenated from multiple tracks with fades or overlaps — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399):
  1. Take the top-5 FAISS neighbours per query segment and drop those below 0.7 similarity.
  2. Group matches by (query, reference) and fit t_ref ≈ a·t_query + b by Huber regression; the slope a captures tempo change.
  3. Keep the trajectory with the most inliers and the best R².
  4. Boundaries run from the first inlier to the last inlier plus the segment length.
  - Results: MuQ bounding-box F1 74.7 vs 58.4 for Dejavu on Pexeso hard-medium.
- The Pexeso audio fingerprinting benchmark builds queries from 10 s segments of different tracks, distorted (time stretch, pitch, echo, reverb, filter, noise) and concatenated with fades or overlaps. It is a ready-made test harness for mix-like conditions. — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)
- Sonnleitner et al. (ISMIR 2016) tested three landmark-based fingerprinting systems on real DJ mixes, released a CC dataset of 10 Mixotic mixes, and call DJ-mix monitoring "a hard challenge". UnmixDB (2018) provides generated mixes with ground truth. — [UnmixDB, Zenodo](https://zenodo.org/records/1422385/files/schwarz-fourer-ismir2018late-breaking-unmixdb.pdf); [ISMIR 2016/2020 DJ mix papers](https://ar5iv.labs.arxiv.org/html/2008.10267)
- Real DJ mixes often use different versions of a track (extended mix, remix, edit), so the catalogue must contain the exact versions played. — [Kim et al., Mix-to-track alignment](https://ar5iv.labs.arxiv.org/html/2008.10267)
- Now Playing gates recognition with a tiny music detector:
  - 8k parameters and under 10 KB, outputting a prediction every 640 ms
  - predictions are smoothed over a few seconds, and recognition triggers after c consecutive confident predictions
  - recall of 75.5% at about one false positive per 20 minutes on non-silent audio
  - [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958)

### Inferences
- **Play-logging state machine (recommended design, not from a single source):**
  1. Fingerprint continuously at a 0.5–1 s hop.
  2. For each candidate track, maintain a track hypothesis of reference offset vs wall-clock time.
  3. Open a "play" when N matches in a window of about 10–20 s agree on offset and slope (slope ≈ 1; under DJ pitching, a ≠ 1).
  4. Extend the play while matches continue.
  5. Close the play after a gap of about 15–30 s without consistent matches.
  6. Log start, end, duration, the reference start and end offsets, and a confidence value.
- **Deduplication.** Merge hypotheses for the same track whose offset trajectories are continuous across short dropouts, such as a crowd cheer. Record a new play when the reference offset jumps back near zero, meaning the song was restarted.
- **Overlap during DJ crossfades.** Allow two concurrent hypotheses.
- **Minimum duration.** Set a minimum play duration (for example 30 s) to qualify for royalty reporting, matching how collecting societies usually count. This needs confirming against the society's rules.

### Gaps
- No published system specifically reports play-boundary accuracy in live bar or club environments.
- No recent (2023–2026) peer-reviewed DJ-mix identification benchmark with neural fingerprints was found beyond Pexeso.

## 6. Low-power / edge deployment: compute on Raspberry Pi, quantization, edge fingerprint plus cloud matching

### Takeaway
Small neural fingerprints (Now Playing CNN, PeakNetFP at 169k parameters, the Conformer-small at 1.5M) are compatible with Raspberry Pi or phone-class hardware at about 1–2 embeddings per second. Foundation-model backbones (MuQ/MERT, hundreds of millions of parameters) are server-class. The proven architecture is a music-detector gate, then an on-device embedding, then compact upload of embeddings (not audio) to cloud FAISS search with sequence scoring. Tens of thousands of tracks also fit entirely on the device.

### Cited Findings
- **Now Playing costs:**
  - stand-by increase under 0.4 mA for the DSP music detector
  - each recognition wake-up costs about 2.0 J (0.139 mAh), about 100 times per day, for a total of about 23.5 mAh per day (0.9% of the Pixel 2 battery)
  - 96-dimensional embeddings at 1 per second; under 3 KB per song after quantization
  - tens of thousands of songs on-device
  - [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958)
- **Model sizes:**
  - PeakNetFP 169k parameters vs NeuralFP 16.9M; 800 MiB vs 2,338 MiB GPU memory at batch 125
  - Conformer small / medium / large: 1.5M / 8.8M / 26.2M
  - [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1); [arXiv 2508.11609](https://arxiv.org/html/2508.11609v2)
- **Storage:** NMFP references need 3.54 MiB per hour of audio (520-byte records); POLARIS needs 1.89 MiB/h. — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)
- **Retrieval:** NAFP and NMFP use FAISS (IVF-PQ in PeakNetFP's setup) with top-20 candidates and sequence re-scoring. Now Playing's quantized index searches about 2% of the database with 32x compression. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661); [arXiv 2506.21086](https://arxiv.org/html/2506.21086v1); [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958)
- **Olaf** runs on ESP32-class microcontrollers and as a 75 KB WASM module. It is a viable on-device classic fallback but weak on real re-recordings (24% on SD-RR). — [GitHub Olaf](https://github.com/JorenSix/Olaf); [arXiv 2609.14820](https://arxiv.org/abs/2609.14820)

### Inferences
- **Catalogue sizing.** A 30,000-track catalogue at about 3.5 minutes per track is about 1,750 hours. At NMFP's 3.54 MiB/h that is about 6.2 GB of float embeddings; with PQ compression of around 32x, as in Now Playing, it is roughly 200 MB. This fits on a Pi or in a modest cloud FAISS index, and a single CPU server could serve many boxes.
- **Bandwidth.** Uploading embeddings instead of audio reduces bandwidth to about 2 × 520 bytes per second (≈ 4 MB per hour of operation) before compression. That matters on Cameroonian mobile data, and it is privacy-preserving because no conversation audio leaves the venue.
- **Pi 3 vs Pi 4.** A Pi 4 running an int8 ONNX or TFLite model of 1–9M parameters at 1–2 inferences per second is very likely within budget. A Pi 3 is tighter but plausible for small CNNs. No published Pi benchmark was found, so prototype and measure.
- **Resilience.** Store-and-forward is essential: keep embeddings locally when offline, and upload when connectivity returns.

### Gaps
- No published Raspberry Pi latency benchmarks for NMFP, PeakNetFP or the Conformers were found.
- No published int8 quantization accuracy impact for these specific models was found.

## 7. How to evaluate in the field: protocols, metrics, building a real bar test set

### Takeaway
Follow the 2025–2026 protocols:
- report top-1 track and segment (offset) accuracy at several query lengths (1/2/5/10 s)
- make each track contribute uniformly
- keep rooms and devices disjoint between training and test
- count "no answer" as wrong in closed-set tests, and also report open-set false-positive rates

Build a real test set by playing known playlists through actual bar PAs with sync chirps (the SD-RR method), and complement it with human-verified logs from live operation (the BMAT method).

### Cited Findings
- **NMFP corrections to NAFP evaluation.** Some tracks appeared up to 11 times in NAFP's queries while others were absent, and 12.7% of query sequences straddled two tracks. NMFP uses 6 equally spaced start points per 30 s chunk at 1/2/5/10 s lengths, reports both track-level and segment-level ("exact"/"near") hit rates, removes FMA duplicates, and keeps room and microphone IRs disjoint between splits. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- **NMFP real-world ground truth.** A commercial fingerprinting system produced matches against a large database, which human annotators then verified (3,692 pairs). — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)
- **SD-RR protocol** — [arXiv 2609.14820](https://arxiv.org/abs/2609.14820):
  - three non-overlapping 10 s excerpts per track, chosen with a fixed seed
  - excerpts normalized to −20 dBFS RMS
  - excerpts embedded in a playback sequence with sync chirps, recorded continuously, and chirp-aligned
  - a second spectral alignment check agreed within 0.1 s for 99.1% of queries
  - metrics: track top-1, track+offset (within 0.1 s), reference MiB/h, query MiB and seconds per query
  - Panako's no-candidate outputs counted as incorrect
- **Pexeso benchmark** (pexafb_hard_small and pexafb_hard_medium) gives segment-level track F1, bounding-box F1 and length F1. — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)
- **Cross-distribution testing.** The MuQ paper trains on Disco-10M and tests on FMA to avoid same-distribution optimism. — [arXiv 2511.05399](https://arxiv.org/abs/2511.05399)
- **Music detector operating point.** Now Playing evaluated the detector on 12k music regions inside 450 h of audio containing speech, street and vehicle noise, and reported recall at a fixed false-positive rate (75.5% at one per 20 minutes). — [arXiv 1711.10958](https://arxiv.org/pdf/1711.10958)

### Inferences
- **Field test plan for SoundProof:**
  1. **Controlled venue sessions.** In 5–10 bars of varied size and loudness (daytime quiet, and evenings with crowd and TV), play a known playlist of catalogue tracks from the venue's own system with sync chirps. Record simultaneously with the candidate mics and placements (omni MEMS on the box, cardioid USB pointed at the speaker, a phone), plus a reference line or loopback where possible, for ground truth.
  2. **Live shadow logging.** For 2–4 weeks per venue, have the box log detections while venue staff or an auditor keeps a manual playlist log on a sample of nights, or the DJ exports a set list.
  3. **Distractors.** Include non-catalogue music, so the false-positive rate is measured open-set, and speech-only and TV segments.
- **Report at three levels:**
  - per-window accuracy (2/5/10 s)
  - per-play metrics: recall of plays at least 30 s long, precision of logged plays, start/end boundary error in seconds, duration error, duplicate-log rate
  - false plays per venue-hour
- **Royalty pitch.** Report per-play recall and precision with confidence intervals, and an estimated payout error: the share of royalty money misallocated.
- **Generalization.** Keep test venues and devices separate from any venues used for fine-tuning or IR collection.

### Gaps
- No standard play-level (not query-level) metric for venue monitoring was found in the academic literature. Commercial monitors such as BMAT do not publish their protocols.
- No public African-venue or bar-noise music recognition dataset was found.
