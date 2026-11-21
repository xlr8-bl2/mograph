# Detection accuracy of audio fingerprinting in noisy ambient venues, and how to calibrate detection probability

Scope note: these notes are about music identification accuracy for phones picking up sound in rooms, and how to calibrate it. Primary PDFs were downloaded and read for Wang 2003, Chang et al. 2021 (NAFP), Google Now Playing 2017, Nikou & Giannakopoulos 2025 and the BAF dataset (ISMIR 2022). Other sources come from abstracts, READMEs or search snippets, and are marked as such.

## 1. Classic landmark fingerprinting (Shazam/Wang, audfprint, Panako, Dejavu, Chromaprint): accuracy under noise, compression, query length, pitch and tempo changes

### Takeaway
In clean or synthetic-noise lab tests, landmark fingerprinting is very robust. Wang's original system reached 50% recognition at about -9 dB SNR with 15 s queries. Its robustness falls off sharply with GSM-type compression, shorter queries, speed changes and background-level music. On a realistic broadcast background-music benchmark (BAF), audfprint and Panako recall only 2-15% of music seconds, at high precision.

### Cited Findings
- **Wang 2003 (Shazam) test setup:** 250 sample recognitions against a 10,000-track database of popular music. The noise sample was "recorded in a noisy pub to simulate real-life conditions", and excerpts were taken from the middle of each track. Audio was 8 kHz, mono, 16-bit. — [Wang 2003, ISMIR](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Wang 2003, additive pub noise:** the recognition rate falls to 50% at about **-9, -6 and -3 dB SNR** for **15, 10 and 5 s** samples respectively. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Wang 2003, pub noise plus GSM 6.10 compression:** the 50% points move to about **-3, 0 and +4 dB SNR** for 15, 10 and 5 s. Codec compression therefore costs roughly 6-7 dB of noise margin. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Wang 2003, hash survival:** from a heavily corrupted 15 s sample, a statistically significant match can be made with only about **1-2% of hash tokens surviving**. Time-offset histogramming makes dropouts and masking irrelevant to the match. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Wang 2003, false-positive thresholding:** a histogram of the scores of incorrectly matching tracks is built, taking database size into account, to give a pdf of the highest-scoring wrong track. The threshold is then set for a target false-positive rate, e.g. **0.1% or 0.01%**. Reported "false positives" are often real sampling or plagiarism. The method is version-specific and "not expected to generalize to live recordings". — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Dejavu README (laptop mic, 45 Top-40 songs, a friend talking and the author humming nearby):**

  | Query length | 1 s | 2 s | 3 s | 4 s | 5 s | 6 s |
  |---|---|---|---|---|---|---|
  | Correct (of 45) | 27 | 43 | 44 | 44 | 45 | 45 |
  | Accuracy | 60.0% | 95.6% | 97.8% | 97.8% | 100% | 100% |

  From disk, recall was 100%. The database used 377 MB for 5.4 M fingerprints of 45 songs. No false-positive rate is reported. This is a tiny, uncontrolled test. — [Dejavu GitHub](https://github.com/worldveil/dejavu)
- **Dejavu at scale:** on a 10K-song database (30 s clips) with 6 s queries under synthetic noise and reverberation, Dejavu reached a **69.6% top-1 song-level hit rate**. NAFP reached 99.5% in the same test. Dejavu's index was about 400 MB, against under 40 MB for NAFP at d=64. — [Chang et al. 2021, NAFP, ICASSP](https://arxiv.org/abs/2010.11910)
- **Panako 2.0:** for 20 s queries sped up by 10%, the top-1 true-positive rate rose from **18% (Panako 2014) to 83% (Panako 2.0)**. It is designed to tolerate time stretch, speed-up and pitch shift. (Source: search snippet of the ISMIR 2021 late-breaking paper and poster.) — [Six, Panako 2.0 LBD, ISMIR 2021](https://archives.ismir.net/ismir2021/latebreaking/000039.pdf); [poster](https://0110.be/files/attachments/498/panako_updates_poster.pdf)
- **Original Panako (2014):** a thesis summary reports that Panako identifies queries pitch-shifted by -6% to +6%. It achieves this with the constant-Q transform and by storing only frequency differences. — [ISMIR 2014 Panako](https://archives.ismir.net/ismir2014/paper/000122.pdf) (via search snippet)
- **BAF benchmark setup (ISMIR 2022):** 57 h of TV audio and 74 h of production music, with 2,000 references, all at 8 kHz mono. About 80% of the annotated music is background music. Results on unanimity annotations, measured over seconds identified, were:

  | System | Precision | Recall | F1 |
  |---|---|---|---|
  | PeakFP (baseline) | 0.96 | 0.32 | 0.47 |
  | Panako 2.0 | 0.98 | 0.06 | 0.12 |
  | Panako 2.0 (x1.5 peak density) | 0.69 | 0.15 | 0.25 |
  | Olaf | 0.98 | 0.06 | 0.11 |
  | NeuralFP (NAFP) | 0.37 | 0.10 | 0.15 |
  | NeuralFP-spcm1510 | 0.38 | 0.22 | 0.28 |
  | audfprint | 0.86 | 0.02 | 0.04 |
  | audfprint (x2 density) | 0.81 | 0.04 | 0.08 |

  "None of the algorithms obtain a F1-score above 47%." — [Cortès et al., BAF, ISMIR 2022](https://zenodo.org/record/7372162/files/000109.pdf)
- **BAF interpretation:** the authors note that high precision is partly an artefact of the small 2,000-reference set ("BAF is not challenging to False Positives"). Raising peak density increases recall at the expense of precision. Systems tend to detect only the short excerpts where the music SNR is higher. — [BAF, ISMIR 2022](https://zenodo.org/record/7372162/files/000109.pdf)
- **BAF index sizes:** for 2,000 references, audfprint's index is 19 MB and Panako 2.0's is 273 MB. Extrapolated to 10 M references, audfprint would need about 95 GB and Olaf about 1.75 TB. — [BAF](https://zenodo.org/record/7372162/files/000109.pdf)
- **Time stretch is the main weakness of classic and older neural methods.** Top-1 track-level hit rates on 10 s queries against 5,000 FMA tracks were:

  | System | Time stretch | Pitch shift | Noise | Reverb | Overall |
  |---|---|---|---|---|---|
  | Dejavu | 25% | 91% | 71% | 80% | 49.2% |
  | NAFP | 39% | 91% | 84% | 86% | 63.5% |
  | GraFPrint | 58% | 97% | 90% | 84% | 67.8% |

  Source: [Robust Neural Audio Fingerprinting with Music Foundation Models, arXiv 2511.05399](https://arxiv.org/html/2511.05399v1)

### Inferences
- In a bar, speaker-to-phone SNR relative to crowd noise is often near or below 0 dB, and the phone's own processing adds codec-like degradation. Wang's GSM curves (50% at 0 dB for 10 s) are probably a better prior for raw per-query recall than his clean-noise curves. Recall should then be raised through repeated queries over a whole song, not by relying on a single query.
- Over a 3-4 minute song, several independent 10 s windows give many chances to match. Per-song detection probability can therefore be far higher than per-query recall, if per-window errors are not strongly correlated. They are often correlated in practice: a quiet intro, a loud crowd for the whole song, or a phone far from the speaker.
- DJ or "remix" plays with tempo changes of 5-10% will be missed largely by audfprint and Dejavu. Panako 2.0 or newer neural models are needed if venue DJs pitch or tempo-shift tracks.
- Chromaprint (AcoustID) was designed for identifying whole files, not short noisy excerpts. It should not be the primary matcher for ambient capture. This comes from general knowledge of its design; no benchmark was found in this session.

### Gaps
- No ISMIR-quality published noise/SNR curve for audfprint or Dejavu specifically was retrieved. The audfprint documentation was not fetched.
- Panako 2.0 additive-noise numbers were not found. The TU Delft thesis on Panako noise layering was not read.
- No source gave a published reverberation-only (RT60-swept) recognition curve for landmark methods.
- Chromaprint noise robustness lacks a sourced benchmark.

## 2. Neural audio fingerprinting vs classic methods in noise

### Takeaway
On synthetic benchmarks, neural fingerprints (NAFP 2021 and later contrastive/transformer models) beat landmark methods, especially for short queries: about 92-96% top-1 at 5-10 s against 100K songs. When music is actually played through a speaker in a noisy room and recorded on a device, accuracy collapses. NAFP falls to 9-49% at 5 s and 13-70% at 15 s, depending on distance and noise. The best 2025 transformer (BEATs plus transfer learning) recovers this to about 34-86% at 5 s and 56-97% at 15 s.

### Cited Findings
- **NAFP test setup (Chang et al. 2021):** 100K-song database (about 56 M one-second segments, 8K hours). Test queries had background noise at **0-10 dB SNR** plus room and microphone impulse responses. Top-1 exact-segment hit rate for the best model (d=128, batch 640):

  | Query length | 1 s | 2 s | 3 s | 5 s | 6 s | 10 s |
  |---|---|---|---|---|---|---|
  | Top-1 exact hit rate | 62.2% | 83.2% | 87.4% | 92.0% | 93.3% | 95.6% |

  The replicated Google Now Playing (triplet loss) scored **44.3 / 60.1 / 73.6 / 81.0 / 86.1%** at 2 / 3 / 5 / 6 / 10 s. — [Chang et al. 2021, arXiv 2010.11910](https://arxiv.org/abs/2010.11910)
- **NAFP without augmentation:** the model trained without augmentation scored **0.0%** at every query length. Robustness comes entirely from training with degradations. — [Chang et al. 2021](https://arxiv.org/abs/2010.11910)
- **NAFP storage and search time:** 5.8 GB for 56 M segments at d=128. Search took about 1.5 s on a CPU (in memory) for a 10 s query against 100K songs. — [Chang et al. 2021](https://arxiv.org/abs/2010.11910)
- **Google Now Playing (2017):** a fully on-device system on Pixel 2. It uses 96-dimensional embeddings, one per second, and needs **under 3 KB per song** for a database of "tens of thousands of songs". Its music detector runs on a DSP, and total battery use is under 1% per day (about 23.5 mAh). — [Agüera y Arcas et al. 2017, arXiv 1711.10958](https://arxiv.org/abs/1711.10958)
- **Now Playing fingerprinter evaluation:** the fingerprinter was evaluated on 20K eight-second segments from 10K songs, using precision/recall curves. 96-dim and 128-dim embeddings performed nearly identically, and 64-dim was clearly worse. The figure shows precision and recall axes from about 0.7 to 1.0. No single operating point is stated in the text. — [Now Playing 2017](https://arxiv.org/abs/1711.10958)
- **Single embeddings are not enough:** Google states that a single embedding generates many false positives. Sequence matching removes them, because the correct song matches every embedding while false positives match only one or two. — [Google AI Blog 2018](https://research.google/blog/googles-next-generation-music-recognition/)
- **Real-world protocol (Nikou & Giannakopoulos, IJMSTA 2025):** 15 songs (52 min) were played from a laptop while a Bluetooth speaker played background noise at three distances, and the mixture was recorded through a device microphone. The three recordings are low.wav, mid.wav and high.wav, with distortion increasing in that order. Queries were matched against 100K songs (56 M vectors), using a majority vote over 1 s segments with 4 nearest neighbours each. — [Nikou & Giannakopoulos 2025, arXiv 2507.06070](https://arxiv.org/pdf/2507.06070)
- **Same paper, baseline synthetic protocol (Table 3):** NAFP scored 72.2 / 84.5 / 89.7 / 92.4% at **5 s** for 0 / 5 / 10 / 15 dB SNR. At 2 s and 0 dB, NAFP scored 52.8% and BEATs+TL 58.5%. — [Nikou & Giannakopoulos 2025](https://arxiv.org/pdf/2507.06070)
- **Same paper, real-world protocol (Table 4), accuracy for low / mid / high distortion:**

  | System | 1 s | 5 s | 10 s | 15 s |
  |---|---|---|---|---|
  | NAFP, original augmentations | 25.1 / 13.5 / **4.3** | 48.8 / 32.3 / **9.0** | 62.5 / 49.0 / 13.0 | 70.7 / 56.0 / 17.5 |
  | NAFP, new augmentations (with low/high-pass filters) | — | 62.3 / 44.6 / 24.8 | 76.1 / 59.3 / 37.4 | 78.5 / 72.1 / 45.3 |
  | BEATs+TL (pretrained transformer) | 44.9 / 29.7 / 12.1 | 86.1 / 68.5 / 34.1 | 93.2 / 86.1 / 46.1 | **97.0 / 90.2 / 56.5** |

  The new augmentations add band-limiting filters (low-pass cutoff 2-3 kHz). Source: [Nikou & Giannakopoulos 2025](https://arxiv.org/pdf/2507.06070)
- **Why the real-world protocol is harder:** low and high frequencies are attenuated when audio is recorded through a mobile microphone, and the baseline augmentations do not model this. — [Nikou & Giannakopoulos 2025](https://arxiv.org/pdf/2507.06070)
- **Music foundation model fingerprints (2025, 10 s queries, 5K FMA tracks, top-1):**

  | System | Noise | Reverb | Time stretch | Pitch shift | Overall |
  |---|---|---|---|---|---|
  | MuQ fine-tuned | 97% | 100% | 96% | 94% | 88.2% |
  | NAFP | 84% | 86% | 39% | — | 63.5% |
  | Dejavu | 71% | 80% | 25% | — | 49.2% |

  Noise augmentation used MUSAN restaurant, home and street noise, plus Aachen room impulse responses. — [arXiv 2511.05399](https://arxiv.org/html/2511.05399v1)
- **PeakNetFP (2025):** keeps **over 90% top-1** for time-stretch factors from 50% to 200%. (Source: abstract snippet.) — [PeakNetFP, arXiv 2506.21086](https://arxiv.org/html/2506.21086v1)
- **Attention-based embeddings (ResNet+STA):** reported gains over NAFP of 6% at 5 s and up to 14.1% at 1 s, at 0 dB SNR. This is as summarised by Nikou & Giannakopoulos, whose own replication did not reproduce the gains consistently (see Table 3 of their paper). — [arXiv 2210.08624](https://arxiv.org/pdf/2210.08624); [Nikou 2025](https://arxiv.org/pdf/2507.06070)
- **Training-degradation realism:** a 2025 paper argues that current neural fingerprinting training "unrealistically simulate[s] real-life audio degradation", which gives sub-optimal supervision. — [arXiv 2506.22661](https://arxiv.org/abs/2506.22661)

### Inferences
- For SoundProof, published synthetic-SNR numbers (90%+) should be treated as an upper bound. The device-recorded results, about 10-60% for single 5-15 s queries under heavy distortion, are the realistic band for a phone far from the speaker in a loud bar.
- Bar recordings will be band-limited by the phone and speaker, and possibly by bass-heavy PA systems. Training or fine-tuning with band-pass augmentations and recordings made in local venues is likely the single biggest accuracy lever.
- Model size matters on cheap phones. BEATs-scale transformers may be too heavy to run continuously on low-end Android. Now Playing shows that a tiny model (under 3 KB per song, DSP gating) is feasible at tens of thousands of songs, at the cost of lower per-query recall, which is compensated by many queries.

### Gaps
- Google never published a single recall/FPR number for the Now Playing fingerprinter in real venues.
- No neural fingerprinting paper evaluated on recordings from actual bars or clubs with crowds was found. The closest is the laptop/Bluetooth-speaker protocol above.
- 2026 papers were not specifically surveyed.

## 3. False positives: how they scale with catalogue size, thresholds and temporal consistency

### Takeaway
The false-positive rate scales with catalogue size and with how many queries run per day. Continuous monitoring runs thousands of queries per device per day, so a per-query FPR of 10^-3 still means many false detections. Every source controls false positives with (a) a threshold calibrated on the score distribution of the best wrong match and (b) agreement across multiple consecutive windows.

### Cited Findings
- **Wang's thresholding method:** the threshold is chosen from the pdf of the highest-scoring incorrect track given database size, aimed at a target FPR of 0.1% or 0.01%. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Scaling, per Google:** moving from tens of thousands to tens of millions of songs means "a thousand times as many songs which could give a false positive". False positives are removed by requiring sequence agreement across many embeddings. — [Google AI Blog 2018](https://research.google/blog/googles-next-generation-music-recognition/)
- **Now Playing music-detector gate:** a detection requires c consecutive music predictions above a threshold t, averaged over a sliding window of a few seconds. — [Now Playing 2017](https://arxiv.org/abs/1711.10958)
- **BAF warns** that high precision on a 2,000-reference set says little about false positives at scale. Denser fingerprint settings raise recall but lower precision (Panako 0.98 to 0.69). — [BAF](https://zenodo.org/record/7372162/files/000109.pdf)
- **Version sensitivity:** Wang reports that many "false positives" are true detections of sampled material. The algorithm is also very sensitive to which version of a track is playing. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)

### Inferences
- For a catalogue of N about 10^4-10^5 songs, set the threshold empirically. Run the matcher on many hours of non-catalogue audio from real bars (talk, TV, songs not in the catalogue), then pick the threshold that gives under about 1 false song-detection per X device-hours. Report the rate per hour, not per query.
- Require k-of-n consistency: for example, the same track ID with consistent time offsets in at least 2-3 consecutive windows, with the track position advancing at about 1x real time. This cuts false positives roughly multiplicatively. The cost is shorter plays and lower recall for plays heard only intermittently.
- Out-of-catalogue songs are the dominant false-positive risk. Covers, remixes, sampled riffs and local re-releases of catalogue songs would also be misattributed or missed.

### Gaps
- No published curve of FPR against database size for audfprint or Panako was found.

## 4. Vendor-stated accuracy (BMAT, ACRCloud, Audible Magic, Gracenote) and independent evaluations

### Takeaway
No vendor-published, independently verified recognition rate for venue or background-music monitoring was found. Vendor pages state coverage (stations, catalogue size), not accuracy. The only open background-music benchmark (BAF, co-authored by BMAT) shows that public algorithms reach F1 of 0.47 or below.

### Cited Findings
- **ACRCloud** advertises monitoring of 50,000+ stations and a reference database of over 150 M tracks, with no accuracy percentage on its broadcast monitoring pages. — [ACRCloud broadcast monitoring](https://acrcloud.com/broadcast-monitoring); [ACRCloud](https://www.acrcloud.com)
- **BMAT research** (with UPF) frames background music as the hard case: in an ICASSP 2026 study, AI-music detection is strongest in the foreground and weakest in the background. BMAT also has an "audibility estimation" project that classifies TV background music as audible or inaudible. — [BMAT Innovation Lab](https://www.bmat.com/innovation-lab/); [arXiv 2602.06823](https://www.arxiv.org/pdf/2602.06823) (search snippet)
- **BAF:** background-music broadcast monitoring "has not received much attention despite its importance to the music industry". — [BAF, ISMIR 2022](https://zenodo.org/record/7372162/files/000109.pdf)

### Inferences
- Any vendor accuracy claim (often "99%+") should be assumed to refer to clean or broadcast feeds. It should not be used for room-captured bar audio without a local calibration study.

### Gaps
- Audible Magic and Gracenote accuracy statements were not found or fetched.
- No independent third-party audit of venue-monitoring accuracy (for example by CMOs or PROs) was found.

## 5. Music/speech detection accuracy of YAMNet, PANNs and similar on-device models

### Takeaway
General-purpose on-device taggers such as YAMNet have modest overall AudioSet performance (mAP 0.306). Published figures are not specific to the "Music" class. Google's purpose-built music detector reached only **75.5% recall** at about one false trigger per 20 minutes in noisy real-world audio. So the gating stage can itself miss a quarter of music if tuned conservatively.

### Cited Findings
- **YAMNet** (MobileNet v1, 521 classes): balanced mAP 0.306, d-prime 2.318 and lwlrap 0.393 on the 20,366-segment AudioSet eval set. — [YAMNet README](https://github.com/tensorflow/models/blob/master/research/audioset/yamnet/README.md)
- **PANNs:** Wavegram-Logmel-CNN mAP 0.439, MobileNetV1 mAP 0.389. — [Kong et al., PANNs, arXiv 1912.10211](https://arxiv.org/abs/1912.10211)
- **Comparison:** PANN 0.44 and AST 0.46 vs YAMNet 0.31 mAP on the AudioSet test set. — [AHEAD-DS / OpenYAMNet, arXiv 2508.10360](https://arxiv.org/pdf/2508.10360)
- **Now Playing music detector:** 8K parameters, under 10 KB, one prediction every 640 ms. It was tested on 12K music regions of 16-40 s within 450 h of audio containing speech, office, street, walking and vehicle noise. Music loudness ranged "from imperceivable by humans to very loud". It achieved **75.5% recall at about one false positive per 20 minutes** of non-silent audio. — [Now Playing 2017](https://arxiv.org/abs/1711.10958)
- **MusicNet** (2021), a compact CNN for real-time background music detection, exists as an alternative. Its numbers were not checked. — [arXiv 2110.04331](https://ar5iv.labs.arxiv.org/html/2110.04331)

### Inferences
- In a bar, music is nearly always present, so a music gate mainly saves battery. If gating is used, set its threshold permissively and calibrate its miss rate separately: P(detect) = P(gate fires) × P(match | gated). Alternatively, run the fingerprinter on a fixed duty cycle without gating.

### Gaps
- No per-class Music or Speech AP for YAMNet or PANNs was retrieved.
- No evaluation of music detection in crowded bars was found.

## 6. Genre and content bias (sparse acoustic, repetitive electronic, heavy bass, call-and-response; African music)

### Takeaway
No peer-reviewed study measuring fingerprinting accuracy by genre was found, and none on African popular music (makossa, bikutsi, afrobeats, coupé-décalé). Mechanistic evidence suggests bias exists, but SoundProof must measure it itself.

### Cited Findings
- **Training data skew:** BAF references are 93% instrumental production music, and benchmarks commonly use FMA (100K songs, 161 genres). Standard benchmarks are therefore not representative of vocal-heavy African pop. — [BAF](https://zenodo.org/record/7372162/files/000109.pdf); [Nikou 2025](https://arxiv.org/pdf/2507.06070)
- **Landmark methods depend on spectral peaks surviving distortion.** Wang found that 1-2% surviving hashes is enough for a 15 s match. Material with few distinct peaks, or peaks masked by noise in the same band, gives fewer survivors. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)
- **Repeated sections:** Now Playing reports that matching sequences "from the same song" includes "local density"-adaptive scoring, used to handle areas of fingerprint space that are densely populated. — [Now Playing 2017](https://arxiv.org/abs/1711.10958)

### Inferences
- Likely harder cases:
  - **Loops and repetition:** heavily looped or repetitive electronic tracks, and songs sharing beats or instrumentals (common in afrobeats and coupé-décalé). These raise confusion between tracks, and the offset within a song becomes ambiguous.
  - **Sparse acoustic material:** few peaks to fingerprint.
  - **Bass-heavy mixes on cheap phones:** phone microphones attenuate below about 100 Hz, and the CDD only "strongly recommends" ±20 dB at 30-100 Hz. Bass energy is largely lost, so the mid and high bands must carry the match.
  - **Crowd singing along (call-and-response):** masks vocal bands with correlated, music-like energy.
- Multiple versions (radio edit, album, extended, live) and regional re-uploads of the same song will split or miss matches unless all versions are in the catalogue.

### Gaps
- Direct evidence for each genre and for African music specifically is absent. This needs to be stated as a key uncertainty in the royalty model.

## 7. Detection range: distance from the speaker, crowd noise, phone microphone processing (AGC, noise suppression, Android audio sources)

### Takeaway
Distance and noise dominate. In the one device-recorded study, moving from the least to the most distorted condition cut NAFP 10 s accuracy from 62.5% to 13% (BEATs+TL: 93% to 46%). On Android, the VOICE_RECOGNITION source is the right default: AGC and noise reduction are disabled by contract. UNPROCESSED is better where supported, but is optional and must be checked.

### Cited Findings
- **Distance-related degradation:** in the real-world protocol, low, mid and high correspond to the noise speaker being at three distances from the microphone. Results are in the Section 2 table (e.g. NAFP-baseline at 10 s: 62.5 / 49.0 / 13.0%). — [Nikou & Giannakopoulos 2025](https://arxiv.org/pdf/2507.06070)
- **Android CDD 5.4.2, VOICE_RECOGNITION:** devices MUST capture at 44.1 or 48 kHz. They MUST, by default, disable any noise reduction and any automatic gain control. They SHOULD be flat to ±3 dB from 100 Hz to 4,000 Hz, and are STRONGLY RECOMMENDED to stay within ±20 dB from 30 to 100 Hz. — [Android 16 CDD](https://source.android.com/docs/compatibility/16/android-16-cdd)
- **Android CDD 5.11, UNPROCESSED:** support is optional and must be reported via `AudioManager.getProperty(PROPERTY_SUPPORT_AUDIO_SOURCE_UNPROCESSED)`, which returns null if unsupported. If supported, the response MUST be flat to ±10 dB from 100 Hz to 7,000 Hz, and within ±20 dB from 5 Hz to 100 Hz. — [Android 16 CDD](https://source.android.com/docs/compatibility/16/android-16-cdd)
- **UNPROCESSED fallback:** the source "behaves like DEFAULT otherwise", i.e. when unsupported. VOICE_COMMUNICATION "will for instance take advantage of echo cancellation or automatic gain control", so it should be avoided. — [Android AudioSource reference](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource)

### Inferences
- **Phone placement:** on cheap phones, CDD compliance may be imperfect. Log the audio source used and check per model whether AGC or noise suppression is active, for example by recording a test sweep or step and comparing levels. Place phones within a few metres of the main speaker and away from the bar counter and crowd. This reproduces the "low.wav" conditions rather than "high.wav".
- **Crowd-noise covariate:** expect the detection rate to depend on time of night. Measure the venue noise floor, for example as RMS energy outside music bands or speech-detector activity, and use it as a covariate in the detection-probability model.

### Gaps
- No published curve of fingerprint recall against metres from the speaker in a real venue was found.
- No measurements of AGC behaviour on specific low-cost Android models (Tecno, Itel, Infinix) were found.

## 8. Practical calibration protocol: estimating per-venue and per-device detection probability

### Takeaway
Literature numbers vary from about 5% to 97% depending on conditions, so detection probability must be estimated empirically per venue (or venue class) and per device. Use planted ground truth plus a statistical model. The protocol below is synthesised from the evidence above; it is not a published standard.

### Cited Findings
- **Published realistic protocol:** play a known concatenated playlist with simultaneous background noise at controlled distances. Record on the target device. Score song-level accuracy by query duration with majority voting. The code is public. — [Nikou & Giannakopoulos 2025](https://arxiv.org/pdf/2507.06070); [benchmark repo](https://github.com/magcil/deep-audio-fingerprinting-benchmark)
- **Scoring by duration as well as count:** BAF scores both match counts and "seconds identified" (precision, recall, F1), with human-annotated ground truth and inter-annotator agreement (Fleiss' kappa). It finds that detections cover only parts of the true music. — [BAF](https://zenodo.org/record/7372162/files/000109.pdf)
- **Threshold setting:** set the threshold from the score distribution of the best wrong match, at a target FPR. — [Wang 2003](https://www.princeton.edu/~cuff/ele201/files/Wang03-shazam.pdf)

### Inferences (proposed protocol)
1. **Lab baseline.** Re-record a stratified sample of catalogue tracks through a typical bar PA and the deployment phone model at 1, 3, 5 and 10 m. Add babble or bar noise at 10, 5, 0 and -5 dB. Estimate per-query recall by query length (5, 10, 15 s) and per-song recall over the full track. Stratify tracks by genre and tempo or density.
2. **Planted test plays (ground truth in situ).** Ask the venue, or a field agent, to play a scheduled list of known catalogue tracks at known times on several nights. The list should include tracks of each genre stratum and be repeated across busy and quiet hours. Detection probability for that venue is p̂ = detected / planted, with a binomial (Wilson or Beta) confidence interval. Fit a hierarchical logistic model, logit(p) = venue + device + genre + noise level + time of night + song length, with venue random effects. Use it to predict p for venues with few plants.
3. **Full-session logging of known playlists.** Collect DJ or playlist logs, or a line-in reference recorder from the venue's mixer at a few audit venues, as a gold standard. Compare phone detections to compute recall, precision and timing error.
4. **Inter-device agreement (capture-recapture).** Deploy two or more phones per venue in different positions. Under an independence assumption, Lincoln-Petersen gives N̂ = n1 × n2 / m, where n1 and n2 are each phone's detections and m is the overlap. This estimates the true number of plays and each phone's p = m / n_other without ground truth. Positive correlation (both phones miss loud-crowd periods) biases N̂ low, so validate against the planted plays.
5. **False-positive audit.** Run the matcher on hours of audio from venues with music known to be out of catalogue, plus talk and TV audio. Estimate false detections per device-hour at the chosen threshold and k-of-n consistency rule. Manually verify a random sample of detections each month for precision (e.g. 200 per stratum).
6. **Royalty estimator.** Estimated plays ≈ (observed detections − expected false positives) / p̂, per venue and stratum, with uncertainty propagated from the p̂ and FPR intervals. Recalibrate when phone model, firmware, placement, catalogue size (which raises FPR) or model version changes.
7. **Ongoing health checks.** Track the daily detection rate per device, the fraction of music-gated time with no match (an indicator of out-of-catalogue songs or a degraded device), and the microphone level. Flag drift.

### Gaps
- No published standard protocol from collective management organisations for calibrating venue-monitoring detection probability was found.
- No published validation of capture-recapture for fingerprint monitoring was found. This is a proposed adaptation.
