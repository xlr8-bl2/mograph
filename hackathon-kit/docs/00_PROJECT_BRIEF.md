# 00 · SoundProof — Project Brief (shared context for BOTH agents)

> Read this file completely before doing anything. Both agents (Person A on Laptop A, Person B on Laptop B)
> work from this same brief. Your own task list is in `docs/PLAN_A_DEVICE_WEB.md` or `docs/PLAN_B_SONACAM_SERVER.md`.
> The exact data formats are in `docs/01_CONTRACT.md`. How to hand off and merge is in `docs/02_WORKFLOW_HANDOFFS_MERGES.md`.

---

## 1. The event and its rules

| Item | Value |
|---|---|
| Event | CIMFEST Hackathon 2026, "Africa's creative economy" |
| Venue | Mountain Hotel, Buea, Cameroon |
| Build window | **Monday 23 Nov 12:00 → Wednesday 25 Nov 12:00** (no extensions) |
| Our internal freeze | **Wednesday 10:00** (everything submitted by 10:00) |
| Judging | Problem fit 25% · Working demo 25% · Viability 20% · Technical execution 15% · Pitch 15% |
| Round 1 | Wed 13:30 at demo tables (every team) |
| Finals | Wed 16:00, 3-minute pitch + 2 minutes of questions |
| Key rule | **All core work must be done during the 48 hours.** Open-source libraries, public APIs, templates and AI coding tools are allowed **but must be declared**. |
| Submission | Name/track/team · problem statement (≤150 words) · working demo (link or ≤2-min video) · repo with README that runs · ≤6-slide deck · paragraph "what we built here vs what existed before" |

**Consequence for you (the agent):** never paste in code from before the event. Everything is written now. Keep a running list
of every library you add in `README.md → Declarations` (name, version, licence, purpose). Note "Built with Claude Code".

### Official schedule (the plans are pinned to it)

| Day | Time | Event |
|---|---|---|
| Mon | 12:00 | BUILD STARTS |
| Mon | 15:00 | Mentor round 1 (problem and scope) |
| Mon | 18:00 | Checkpoint 1: one-line pitch + tech stack |
| Tue | 09:00 | Stand-up (60 s per team) |
| Tue | 10:00 | Mentor round 2 (technical deep dive) |
| Tue | 14:00 | **Industry clinic: artists and promoters test the prototype** |
| Tue | 16:00 | **Checkpoint 2: show a working core feature** |
| Tue | 20:00 | Pitch clinic |
| Wed | 09:00 | Final mentor check-ins |
| Wed | 12:00 | BUILD ENDS, submissions close |

---

## 2. The problem (why this exists)

- **SONACAM** is Cameroon's music rights society (it collects royalties for songwriters/composers).
- On **6 Oct 2025** SONACAM paid a 40,000,000 FCFA distribution as a **flat 27,000 FCFA "advance" to every artist**
  (about 1,480 people), because it had **no data on what was actually played**. Its own release says
  *"un montant forfaitaire de 27 000 frs"* and ends *"le meilleur est à venir"*.
- On **30 Jul 2026** SONACAM announced **100M FCFA collected from bars** via a **12 FCFA levy per beer crate**
  (collected through brewers). The money exists; nobody knows whose music earned it.
- **Sept 2026**: the son of makossa legend Eboa Lotin reported receiving **2,000 FCFA**.
- SONACAM signed for **WIPO Connect** (WIPO's free software for rights societies) on 7 Jul 2021. WIPO Connect *calculates*
  payouts from **usage data**. SONACAM's own website says distribution needs usage data (radio playlists, TV reports,
  digital logs, "feuilles de détection"). **For bars, no usage data source exists.** That gap is SoundProof.
- Other lump-sum pots also lack a split key: venue licences (priced by m²), private-copy levy, CRTV broadcast arrears
  (850M FCFA dispute, 2025). SoundProof is "the split key for every lump-sum pot".

**One-line pitch:** *"SoundProof: music meters that tell SONACAM whose songs were actually played, so artists are paid by
real plays, not a flat 27,000 FCFA."*

---

## 3. What we are building (the demo)

Two Windows laptops:

| Laptop | Plays the role of | Owner |
|---|---|---|
| **A — "the device"** | A $5 meter on a bar wall. Sandboxed Python agent: mic → 8 kHz → spectral **peaks** (the audio signature) → signed batch with metadata → outbox → sends to B. Also runs **virtual meters** (same agent fed WAV files). Person A also builds the **web front-end** (static pages served by B) and the **mock month generator**. | Person A |
| **B — "SONACAM"** | SONACAM's server. Docker Compose: PostgreSQL, ingest API, catalogue upload API, fingerprint worker, matcher, play logger, ledger (hash chain + hourly root + public anchor), stats engine (standardisation + estimation), payout engine, read API + live feed for the dashboard. | Person B |

The network between them (phone hotspot or Windows Mobile Hotspot on B) stands in for 4G.

**Hardware story (honest):** we planned a cheap box (CH32V203 RISC-V MCU + electret mic + 4G Cat-1 module, ~$5–8 ex-works)
but could not get electronics in time, so Laptop A runs **exactly the computation that box would run** (and no more).
A Blender-rendered design dossier exists separately (`SoundProof-meter-design.pdf`).

---

## 4. Architecture (both agents must know all of it)

```
 LAPTOP A — "device" (Python venv, Windows)            LAPTOP B — "SONACAM" (Docker Compose, Windows + WSL2)
┌──────────────────────────────────────┐            ┌──────────────────────────────────────────────────────┐
│ device/agent                         │  HTTPS/HTTP │ api  (FastAPI)                                        │
│  capture: mic 48k → 8 kHz mono       │  POST       │   POST /v1/batches ─► raw_batches (append-only)       │
│  scheduler: demo 10s/10s, field 10s/60s ──────────►│   GET  /api/v1/*   (read API for dashboard)           │
│  core.peaks.extract_peaks (fp-1.0)   │  ◄── ack    │   GET  /api/v1/feed (Server-Sent Events, live)        │
│  core.schema.Batch + Ed25519 sign    │             │   POST /api/v1/songs (catalogue upload)               │
│  outbox (SQLite) + uploader (retry)  │             │   static: serves web/ (A's pages)                     │
│  status TUI (rich)                   │             │ worker_fingerprint: upload → ffmpeg 8k → peaks →      │
│ device/virtual: WAV-fed meters       │             │    core.landmarks hashes → fp_hashes (+ dedupe check)  │
│ device/tools: provision keys,        │             │ worker_matcher: raw_batches → hashes → offset votes → │
│   fake_sonacam, netcheck, calibrate, │             │    match_events                                       │
│   record dataset, eval harness       │             │ worker_playlogger: match_events → plays (event time)  │
│ tools/mockgen: mock month CSVs        │             │ ledger: chain_hash per play, hourly Merkle root,     │
│ web/: dashboard + portal + artist    │             │    anchor adapter (file / Sepolia testnet), auditor   │
│   pages (static HTML/JS/CSS)         │             │ stats: fair-play weights → estimation → shares + CI  │
└──────────────────────────────────────┘             │ payouts: pot × share → owner splits → statements, CSV│
           shared: core/soundproof_core               │ postgres 16 (one DB: data + fp index + job queue)    │
   peaks.py (A) · schema.py, signing.py,              └──────────────────────────────────────────────────────┘
   landmarks.py (B) · constants.py (locked)
```

### Design principles (non-negotiable)

1. **One shared core library** (`core/soundproof_core`). The device and the catalogue use the **same** `extract_peaks`.
   Any change to fingerprint parameters is a **contract change** (see 02 doc).
2. **Raw data is immutable; everything else is recomputed.** `raw_batches`, `match_events`, `plays` are append-only
   (plays are hash-chained). Weights, stats, shares and payouts are **derived** and tagged with a **rule version**.
3. **Event time, not arrival time.** All ordering in the play logger and stats uses the time a window was *heard*
   (`window_start`). Late batches (after network cuts) are slotted in and the affected venue-day is recomputed.
4. **Dumb device, smart server.** The device only: listen → peaks → metadata → sign → queue → send.
5. **Few moving parts.** PostgreSQL is storage, fingerprint index and job queue. No Redis/Kafka.
6. **Everything testable alone.** Each side can run and test without the other using fixtures and fakes (see contract).

---

## 5. The audio signature (fingerprint) — specification `fp-1.0`

Implemented in `core/soundproof_core/peaks.py` (owner A) and `core/soundproof_core/landmarks.py` (owner B).
Constants live in `core/soundproof_core/constants.py` (locked; content given verbatim in `01_CONTRACT.md §2`).

### 5.1 Peak extraction (device AND catalogue) — `extract_peaks(samples, sr=8000)`

1. Input: `numpy.float32` mono samples in [-1, 1] at **8000 Hz**. (Capture at 48 kHz and resample with
   `scipy.signal.resample_poly(x, up=1, down=6)`; catalogue decodes with ffmpeg straight to 8 kHz mono.)
2. STFT: `N_FFT=512`, `HOP=256`, window `scipy.signal.get_window("hann", 512, fftbins=True)`, no edge padding.
   Number of frames = `1 + (len(x) - 512) // 256`. For a 10 s window (80,000 samples) that is **311 frames** (0..310).
3. Magnitude in dB: `S = 20*log10(abs(rfft(frame*win)) + 1e-9)` → 257 bins (0..256). Bin width = 15.625 Hz.
4. Analysed band: bins **7..224** inclusive (~109–3500 Hz). Set everything outside to `-inf` before peak picking.
5. Local maximum: `S == maximum_filter(S, size=(21, 31), mode="constant", cval=-inf)` (21 frames × 31 bins).
6. Local threshold: `S > uniform_filter(S_band_finite, size=(63, 31), mode="nearest") + 6.0` dB
   (compute the uniform filter on a copy where out-of-band values are replaced by the band's minimum).
7. Density cap: split frames into blocks of **32 frames** (~1.02 s); in each block keep the **30** strongest peaks.
8. Output: structured array of `(frame:int32, bin:int16, mag_db:float32)`, sorted by `(frame, bin)`. Deterministic.
9. For the wire, quantise: `mag_u8 = clip(round(2*(mag_db + 80)), 0, 255)` (0.5 dB steps, −80..+47.5 dB);
   pack as little-endian `<HBB` (frame u16 relative to window start, bin u8, mag u8), concatenate, base64.

### 5.2 Landmarks (hashes) — server and catalogue — `make_hashes(peaks)`

For each anchor peak `(t1,f1)` in time order: candidate targets are peaks with `1 ≤ t2−t1 ≤ 63` and `|f2−f1| ≤ 63`;
sort candidates by `(t2−t1, |f2−f1|)`, take the first **5** (fan-out).
`hash = (f1 << 13) | ((f2 − f1 + 64) << 6) | (t2 − t1)` → 21-bit integer. Emit `(hash, t1)`.
Catalogue rows: `(hash, song_id, t1_frame_in_song)`.

### 5.3 Matching one 10-second window — `match_window(hashes, index)`

```
votes = {}                                     # (song_id, offset_frames) -> count
for (h, tq) in window_hashes:
    for (song_id, ts) in index[h]:
        votes[(song_id, ts - tq)] += 1
score(song, off) = votes[song,off] + votes[song,off-1] + votes[song,off+1]   # ±1 frame tolerance
best = argmax over (song, off); second = best score of any OTHER song
accept if best_score >= MATCH_MIN_ALIGNED (8) and best_score >= MATCH_RATIO (2.0) * max(second, 1)
offset_s = off * 256 / 8000   (position in the song where the window STARTS)
confidence = best_score / max(len(window_hashes), 1)
```
Output a `match_event` (accepted or not; rejected ones stored with `accepted=false` for evaluation).

### 5.4 Duplicate check at upload
Before inserting a new song's hashes, run the matcher on 3 slices of the new song (at 20%, 50%, 80% of its length,
10 s each). If ≥2 slices match the same existing song → mark upload `status=duplicate_suspected`, link to that song,
do not index until an admin approves.

### 5.5 Known limits (say them before a judge does)
Landmarks are not tempo/pitch invariant (DJ speed changes break them). Very loud bass can clip a cheap mic.
We tune thresholds on **real recordings from the hall** (A collects them) and quote only measured numbers.

---

## 6. From matches to plays — the play logger (owner B, `server/workers/playlogger.py`)

State per meter: `current_song, play_id, first_window_start, last_window_start, last_offset_s, confirmations`.
Process `match_events` of a meter **in `window_start` order**.

- **Same play** if same song AND `| (offset − last_offset) − (window_start − last_window_start) | ≤ 5 s`.
  (Repeated choruses inside a play never look like a restart because the song offset keeps advancing with the clock.)
- **New play** if a different song, OR the offset jumped **back** by more than 20 s, OR
  `window_start − last_window_start > (song_length − last_offset) + 60 s`.
- A play is **confirmed** when it has **≥2 accepted windows** AND covered `≥ min(30 s, 0.3 × song_length)`
  (covered = `last_offset + 10 − first_offset`). Single stray windows stay unconfirmed (no play row).
- Unmatched windows with loudness above the venue's music threshold are counted as **"music, unidentified"** time
  (escrow, not given to other artists).
- Late batches: if a batch arrives whose `window_start` is earlier than the meter's last processed window, re-run the
  logger for that meter from the start of that venue-day (plays are immutable: re-run only creates missing plays;
  if a previously confirmed play would change, write a `play_correction` row, never edit the original).

---

## 7. Standardisation ("fair-play weights") — rule version `sr-1.0` (owner B, `server/stats/fairplay.py`)

Applied per **(venue, song)** over confirmed plays in **event-time order**. Raw plays are always kept; only weights change.

### 7.1 Repeat streak and cooldown (agreed by the team)

| Play in the streak | Weight |
|---|---|
| 1st | 1 |
| 2nd | 1 |
| 3rd | 0.5 |
| 4th | 0.25 |
| After the 4th → **cooldown** | **0** for every play of that song in that venue during cooldown |

- **Cooldown ends** only when **both**: `≥ 2 hours` since the 4th play **and** `≥ 3 other confirmed songs` in that venue since then. Then the streak resets (next play weight 1).
- **Normal streak reset (before reaching 4):** when `≥ 3 other confirmed songs` **and** `≥ 30 min` since this song's last play.
- **No nightly cap.**
- Every zero-weight play is kept and flagged `reason="cooldown"`.

Reference algorithm (must be implemented exactly; unit tests use the tables below):
```
state[venue][song] = {streak:0, last_play:None, cooldown_until:None, others_since:0}
for play in plays_of_venue ordered by started_at:
    for s in state[venue] where s != play.song: state[venue][s].others_since += 1
    st = state[venue][play.song]
    if st.cooldown_until is not None:
        if play.started_at >= st.cooldown_until and st.others_since >= 3:
            st.cooldown_until = None; st.streak = 0
        else:
            emit(play, 0, "cooldown"); st.last_play = play.started_at; continue
    if st.streak > 0 and st.others_since >= 3 and play.started_at - st.last_play >= 30 min:
        st.streak = 0
    st.streak += 1
    w = [1, 1, 0.5, 0.25][st.streak - 1]
    emit(play, w, "streak_%d" % st.streak)
    if st.streak == 4: st.cooldown_until = play.started_at + 2 h; st.others_since = 0
    st.last_play = play.started_at; st.others_since = 0
```
**Test vector T-SR-1 (one venue, one night)** — other songs B,C,D,… are distinct and each confirmed:

| started_at | song | expected weight | reason |
|---|---|---|---|
| 21:00 | A | 1 | streak_1 |
| 21:04 | A | 1 | streak_2 |
| 21:08 | A | 0.5 | streak_3 |
| 21:12 | A | 0.25 | streak_4 (cooldown until ≥23:12 and 3 others) |
| 21:16 | B | 1 | streak_1 |
| 21:20 | A | 0 | cooldown |
| 21:24 | C | 1 | streak_1 |
| 21:28 | D | 1 | streak_1 |
| 22:00 | A | 0 | cooldown (time not reached even though 3 others) |
| 23:15 | A | 1 | cooldown ended → streak_1 |
Total raw A = 7, counted A = 3.75.

**Test vector T-SR-2 (normal reset):** A 20:00 (1), A 20:05 (1), B 20:10, C 20:15, D 20:20, A 20:40 → weight 1 (reset: 3 others and 35 min). 
**Test vector T-SR-3 (no reset, too soon):** A 20:00, A 20:04, B 20:06, C 20:08, D 20:10, A 20:12 → weight 0.5 (only 8 min).

### 7.2 Additional modifiers (applied after 7.1, multiplicatively)
- **Loop flag:** ≥4 consecutive plays of the same song where each start gap is within `song_length ± 20 s` → flag `loop` on all of them (weight already limited by 7.1; flag goes to SONACAM review list).
- **Audience factor:** if the play's mean `loudness_db` < venue's 20th percentile of loudness over the period **or** the play starts outside the venue's active hours (hours where the venue has ≥10% of its plays) → ×0.5, reason `low_audience`. Demo default thresholds in `server/config.py`.
- **Concentration check (per song, per period):** if a song is heard in **< 5 venues** and its **top 2 venues hold > 60%** of its weighted plays → cap each venue's contribution for that song at `max(5, 3 × median per-venue weighted plays of all songs)` and flag `concentration` for review.
- Weights and reasons are written to `play_weights (play_id, rule_version, weight, reasons[])`.

### 7.3 Estimation (after weights) — `server/stats/estimate.py`
1. Per meter **listening fraction** `L = (sum of window durations received) / (venue open hours in period)`; default open hours 18:00–02:00 unless the meter's own activity profile says otherwise.
2. Per meter **catch rate** `c` from calibration (default 0.8 until calibrated; see A-T14).
3. Per meter **expanded weighted plays** for each song: `E = weighted_plays / (c × L)`.
4. **Strata** = (city, size class). Each meter represents `N_stratum / n_meters_in_stratum` bars (`venues.csv` gives `bars_in_stratum`).
5. National estimate per song: `T_song = Σ_meters E × bars_represented`.
6. **Share** `S_song = T_song / Σ T`. **Range:** bootstrap — resample meters with replacement within each stratum, 200 times, 5th–95th percentile of S.
7. Unidentified music time → escrow share reported separately (not redistributed).
8. Output `shares(stats_run_id, song_id, raw_plays, weighted_plays, share, ci_low, ci_high, flags)`.

### 7.4 Flat vs SoundProof comparison (demo number)
`misallocated_flat = 0.5 × pot × Σ |1/n_artists − S_true|` is not computable on real data (no truth), so on the **mock month** (which has a known "true" play table) compute misallocation of (a) flat split and (b) SoundProof estimate vs truth. Only show numbers computed by the code.

---

## 8. Ledger, public checkpoint, tamper demo (owner B)

- On insert of each play: `chain_hash = sha256(prev_chain_hash || canonical_json(play_core_fields))` (hex). First play uses 64 zeros as prev.
- Every hour (and on demand): Merkle root of that hour's `chain_hash`es → `anchors(hour, root, anchored_via, tx_ref)`.
- **Anchor adapter**: `FileAnchor` (default: writes root to `server/anchors/anchors.jsonl` and shows it) and `SepoliaAnchor` (stretch: sends a 0-value tx with the root in `data` on the Ethereum Sepolia testnet; needs an RPC URL and a funded test key in `.env`; never mainnet; no token).
- **Auditor** `GET /api/v1/ledger/status`: recomputes the chain from the DB and compares to stored hashes and anchors → `{intact: bool, broken_at_play_id, last_root, last_anchor}`.
- **Tamper demo**: `server/tools/tamper.py --play-id N` changes a row directly in Postgres (bypassing the app). Dashboard turns red within 2 s (SSE `ledger` event) and payouts show "frozen".

---

## 9. Payouts (owner B)

- Pot configurable (default **100,000,000 FCFA**, label "bar levy pot (example)").
- Song amount = pot × share (escrow share excluded). Split to owners by registered splits (must sum to 100%).
- Integer FCFA with **largest-remainder rounding** so the total equals the pot exactly.
- Statements per owner; mock mobile money "sent" status (never real money).
- Export `usage_report_wipo_connect_prototype.csv` with ISRC/ISWC columns (prototype format, label it as such).

---

## 10. Metadata each batch carries (and why)

| Field | Why |
|---|---|
| `meter_id`, `venue_id` | Which box, which bar (venue's city/size/type are looked up on B, not sent) |
| `seq` | Detect gaps (= unmonitored time) and replays/duplicates |
| `window_start`, `window_ms` | Event time — all rules depend on it |
| `loudness_db` | One number per window (RMS dBFS), never audio; used for audience factor and "music present" |
| `cell_id` | On a real device the 4G module knows its tower for free → detects a moved meter. On laptops: configured string |
| `fp_version` | Guarantees device and catalogue used the same extractor |
| `fw` | Firmware/agent version |
| `sig` | Ed25519 signature by the meter's private key |
Exact JSON in `01_CONTRACT.md`.

---

## 11. Networking for the demo (Windows)

- Best: **Windows Mobile Hotspot on Laptop B** (Settings → Network & internet → Mobile hotspot → share B's Wi-Fi). A joins it. No router, no client isolation, B keeps internet. Fallback: a phone hotspot both join, or an Ethernet cable.
- Venue Wi-Fi usually blocks laptop-to-laptop traffic — **do not rely on it** for A→B.
- B exposes port **8000**. Allow it in Windows Defender Firewall on the **Private** profile (command in PLAN_B). Mark the hotspot network as **Private** on A.
- Find B's IP with `ipconfig` (Mobile Hotspot host is usually `192.168.137.1`). A's config: `SONACAM_URL=http://192.168.137.1:8000`.
- Clocks: both run `w32tm /resync` (admin PowerShell) at the start of each day; B flags batches whose `received_at − window_end` is negative by >5 s or > 6 h (late arrivals are allowed but labelled).

---

## 12. The demo script (what the judges see) — both sides build toward this

1. *(Slide)* SONACAM's 6 Oct 2025 release on screen: "montant forfaitaire de 27 000 frs".
2. **Upload**: on the portal page, an artist (teammate) uploads a consented song → status Processing → Ready (fingerprints indexed). A duplicate upload of the same song is flagged.
3. **Live play**: a judge picks a song from our list; it plays on the Bluetooth speaker. Laptop A (the "meter", status screen visible) hears it → batch sent → on the big screen (dashboard on B) a pin pulses at "CIMFEST, Buea", the play appears in the live feed, the artist's phone (artist page open) shows the play.
4. **Network cut**: unplug/disable hotspot → A keeps queueing (status shows N queued) → reconnect → queued plays flow in, slotted at the correct times.
5. **The month**: dashboard switches to the mock month (30 meters): shares with ranges; toggle **Raw vs Standardised** → the looping artist drops from #1, label "15 plays → 3.75 counted · cooldown".
6. **Money**: 100M FCFA split by share → owners' splits → mock mobile money statements; "Flat 27,000 vs SoundProof" misallocation numbers.
7. **Trust**: run the tamper script → ledger status red, payouts frozen; show the public anchor of the hour.
8. Close: "Le meilleur est à venir. We agree. Here it is."

Backup: a 2-minute screen recording of steps 2–7 (recorded Tue night), and virtual meters if the mic fails.

---

## 13. Song rights for the demo
Only songs from artists who agreed in writing (a WhatsApp message is fine — keep screenshots in `data/consent/`, gitignored), or the team's own recordings. Test material during development: CC0 / public-domain music. Never commit audio files to Git (`data/` is gitignored).

---

## 14. Glossary
- **Window**: 10 s of audio the meter analyses. **Batch**: one window's peaks + metadata, signed.
- **Peaks**: the loudest points of the spectrogram (time frame, frequency bin, loudness). **Hash/landmark**: a pair of peaks packed into an integer.
- **Match event**: result of matching one window. **Play**: a confirmed song play built from ≥2 windows.
- **Weight**: how much a play counts after standardisation. **Share**: a song's fraction of all estimated plays.
- **Stratum**: group of similar bars (city × size). **Catch rate**: fraction of real plays a meter detects.
- **Escrow**: identified-music-but-unregistered time, held, not redistributed.
- **Handoff**: the note an agent writes so the next session can continue (see 02 doc).
