# PLAN A — Person A / Laptop A: the Device, the Web front-end, the Mock Month, the Demo

> You are the agent on **Laptop A (Windows)**. Read `docs/00_PROJECT_BRIEF.md`, `docs/01_CONTRACT.md`,
> `docs/02_WORKFLOW_HANDOFFS_MERGES.md` first. This plan lists **your** tasks in order, with time boxes, exact files,
> steps, tests and Definition of Done (DoD). Section 2 tells you **everything Person B is doing**, so you always know
> what exists on the other side, when, and how to test without it.

---

## 0. Your mission in one paragraph
Laptop A **is the meter**. You build the sandboxed agent that listens through the laptop mic, does only what the $5 board
would do (8 kHz → spectrogram → peaks), attaches metadata (meter, venue, time, seq, loudness, cell id), signs, queues
and sends batches to Laptop B, and survives network cuts. You also build: virtual meters (WAV-fed agents), the
recordings dataset from the hall, the 30-day **mock month** CSVs, **all web pages** (SONACAM dashboard, upload portal,
artist page, admin page — static files served by B), the calibration and evaluation tools, and you own the demo
rehearsal and the 2-minute video.

Your folders: `device/`, `web/`, `tools/mockgen/`, `core/soundproof_core/peaks.py` (+ its test), `contract/fixtures/mock_month/`,
`contract/meters/`, `scripts/*_a.ps1`, `handoffs/A/`, `STATUS_A.md`, `notes/` (A's notes).
Never edit `server/`, `docker-compose.yml`, `core/.../schema.py|signing.py|landmarks.py|constants.py`, `contract/schemas/`, `contract/fixtures/api/`.

---

## 1. Timeline at a glance (local time, Buea)

| When | Task | Merge checkpoint |
|---|---|---|
| Mon 12:00–12:20 | A-T00 Setup + clone | |
| Mon 12:20–13:40 | A-T01 `core/peaks.py` + tests | **MC0 13:45** |
| Mon 14:00–15:00 | A-T02 Audio capture + scheduler | |
| Mon 15:00–15:20 | Mentor round 1 (human) | |
| Mon 15:20–15:50 | A-T03 Key provisioning + batch builder | |
| Mon 15:50–16:15 | A-T04 Fake SONACAM (test server) | |
| Mon 16:15–17:15 | A-T05 Outbox + uploader + heartbeat | |
| Mon 17:15–17:30 | Handoff + `scripts\cp1_a.ps1` | **MC1 17:30** |
| Mon 18:00 | Official checkpoint 1 (human) | |
| Mon 18:15–19:00 | A-T06 Agent runner + status screen | |
| Mon 19:00–19:30 | Dinner | |
| Mon 19:30–20:45 | A-T07 Virtual meters + scenarios | |
| Mon 20:45–21:30 | A-T08 Network/clock tools + Docker insurance | |
| Mon 21:30–23:00 | A-T09 Hall recordings dataset + real test vectors | |
| Mon 23:00–00:45 | A-T10 Mock month generator (part 1) | |
| Mon 00:45–06:30 | **Sleep** (handoff first) | |
| Tue 06:45–07:30 | A-T10 Mock month (part 2) + validation, push | |
| Tue 07:30–10:45 | A-T11 Web: foundation + SONACAM dashboard | |
| Tue 09:00 / 10:00 | Stand-up / Mentor round 2 (human, 30 min total) | |
| Tue 10:45–12:15 | A-T12 Web: upload portal | **MC2 12:30** |
| Tue 13:00–14:00 | Lunch + demo prep for clinic | |
| Tue 14:00–14:45 | Industry clinic (run demo, collect feedback in `notes/clinic.md`) | |
| Tue 14:45–15:20 | A-T13 Web: payouts, compare-flat, ledger banner, raw/standardised | **MC3 15:30** |
| Tue 16:00 | Official checkpoint 2 (human) | |
| Tue 16:15–17:30 | A-T14 Web: artist page | |
| Tue 17:30–18:30 | A-T15 Calibration tool | |
| Tue 18:30–20:00 | A-T16 Evaluation harness + measured accuracy report | |
| Tue 20:00–20:30 | Pitch clinic (human) | |
| Tue 20:30–21:15 | A-T17 Web: admin page | |
| Tue 21:15–23:00 | A-T18 Rehearsal ×2 + 2-min video + README sections | **MC4 23:00** |
| Wed 07:30–09:30 | A-T19 Fixes only, submission material | **MC5 09:30** → submit 10:00 |

Buffers are built into each time box. If a task overruns by >30 min, write a handoff, cut the task's optional items
(marked *optional*), and move on. Priority if time runs out: see 02 §9.

---

## 2. What Person B is doing (full context, so you never guess)

| When | B task | What it gives you | How you test without it |
|---|---|---|---|
| Mon 12:00–12:20 | **B-T00** Bootstrap repo (GitHub, folders, CLAUDE.md, docs, constants.py, pyproject, scripts skeleton, branches `a/work` `b/work`) | The repo you clone | — |
| Mon 12:20–13:40 | **B-T01** `core/schema.py` (Batch model, canonical bytes), `core/signing.py` (Ed25519 sign/verify, key files), `core/landmarks.py` (hashes + `match_window`), JSON Schemas, batch test vectors | The Batch class and signing you use in A-T03 | Until MC0 you only need `constants.py` (in bootstrap) for A-T01 |
| Mon 14:00–15:00 | **B-T02** Docker Compose (postgres, api, workers), DB migrations, `/health` | B's server running | — |
| Mon 15:15–16:00 | **B-T03** Ingest API `POST /v1/batches`, `/bulk`, `/heartbeat`, `/meters/register`; loads `contract/meters/*.pub.json`; API fixtures in `contract/fixtures/api/` | The real endpoint your uploader talks to | Your `device/tools/fake_sonacam.py` (A-T04) implements the same behaviour |
| Mon 16:00–16:50 | **B-T04** Catalogue upload API `POST /api/v1/songs` + fingerprint worker + duplicate check | Songs become "ready" (indexed) | Portal page uses `?mock=1` fixtures |
| Mon 16:50–17:15 | **B-T05** Matcher worker → `match_events` + SSE `match` events | Proof your batches are recognised | Your eval harness (A-T16) after MC2 |
| Mon 17:30 | **MC1** | | |
| Mon 18:15–19:30 | **B-T06** Play logger (event-time, chorus-safe, late batches) | `plays` + SSE `play` events | Fixture `plays.json`, `feed_sample.txt` |
| Mon 19:30–20:45 | **B-T07** Ledger (hash chain, hourly Merkle root, FileAnchor, auditor) + `server/tools/tamper.py` | `GET /api/v1/ledger/status`, SSE `ledger` | Fixture `ledger_status.json` |
| Mon 20:45–22:15 | **B-T08** Read API v1 (`overview`, `venues`, `plays`, `songs`, `flags`, `artists`) + SSE feed | Real data for your pages | Fixtures |
| Mon 22:15–00:30 | **B-T09** Stats part 1: fair-play weights `sr-1.0` with test vectors T-SR-1..3, loop/audience/concentration | `weight`, `reasons` on plays | Fixtures |
| Tue 07:30–09:30 | **B-T10** Stats part 2: estimation, bootstrap ranges, `GET /api/v1/shares`, `POST /api/v1/stats/run` | Shares with ranges | `shares_*.json` fixtures |
| Tue 09:30–11:00 | **B-T11** Mock month importer (reads YOUR CSVs from `contract/fixtures/mock_month/`, via `git show origin/a/work:...` before merge) + `compare-flat` | Mock month visible in all endpoints | — |
| Tue 11:00–12:15 | **B-T12** Payouts (largest remainder, owner splits, frozen when ledger broken), WIPO CSV export | `GET /api/v1/payouts` | `payouts.json` |
| Tue 12:30 | **MC2** | | |
| Tue 13:15–15:15 | **B-T13** Flags endpoint complete, raw/standardised everywhere, tamper polish, demo reset endpoint | | |
| Tue 15:30 | **MC3** | | |
| Tue 16:15–17:30 | **B-T14** Calibration intake → catch rates; clock-skew and moved-meter flags | Your calibrate tool talks to it | |
| Tue 17:30–19:00 | **B-T15** Sepolia anchor (stretch) | `last_anchor.via = "sepolia"` | |
| Tue 20:30–22:00 | **B-T16** Robustness, restart safety, demo seed/reset | | |
| Tue 22:00–23:00 | **B-T17** README (server), declarations, reindex tool | | |
| Tue 23:00 | **MC4** | | |
| Wed 07:30–09:30 | **B-T18** Fixes only, tag v1.0 | | |

B's database tables (so you understand the API data): `meters, venues, songs, owners, fp_hashes, raw_batches,
match_events, plays, play_corrections, play_weights, stats_runs, shares, flags, anchors, calibrations, payouts, jobs`.

---

## 3. Your tasks in detail

### A-T00 · Setup and clone — Mon 12:00–12:20
**Steps**
1. Check prerequisites from 02 §8 (Python 3.11, Git, ffmpeg, Claude Code, PowerShell policy, mic permission, mic enhancements OFF).
2. When B says "repo is up": `git clone <url> soundproof; cd soundproof; git checkout a/work`.
3. `py -3.11 -m venv .venv; .\.venv\Scripts\Activate.ps1; pip install -U pip`.
4. Create `device/requirements.txt` (pin): `numpy==2.1.3 scipy==1.14.1 sounddevice==0.5.1 soundfile==0.12.1 pynacl==1.5.0 httpx==0.27.2 pydantic==2.9.2 rich==13.9.4 fastapi==0.115.5 uvicorn==0.32.1 jsonschema==4.23.0 pytest==8.3.3 tomli-w==1.1.0`.
5. `pip install -e core -r device/requirements.txt`.
6. Create `scripts/test_a.ps1`: activates venv, runs `pytest core/tests/test_peaks.py device/tests tools/mockgen/tests -q`.
7. Create `scripts/mic_check.py`: prints `sounddevice.query_devices()`, the default WASAPI input, records 3 s at 48 kHz, prints RMS dBFS.
**DoD:** `python scripts/mic_check.py` prints a level that rises when you clap. First handoff written (`[A-HO] T00`).

### A-T01 · `core/soundproof_core/peaks.py` — Mon 12:20–13:40 (shared code, MC0)
Implement exactly brief §5.1 using constants from `constants.py`.
**Public API (others depend on these names):**
```python
def resample_to_8k(x48: np.ndarray) -> np.ndarray              # float32, resample_poly(up=1, down=6)
def spectrogram_db(x8: np.ndarray) -> np.ndarray               # shape (frames, 257) float32
def extract_peaks(x8: np.ndarray) -> np.ndarray                # structured: frame int32, bin int16, mag_db float32
def pack_peaks(peaks) -> bytes                                 # <HBB per peak, mag_u8 quantised
def unpack_peaks(b: bytes) -> np.ndarray                       # same dtype, mag_db de-quantised
def peaks_to_b64(peaks) -> str ; def b64_to_peaks(s: str) -> np.ndarray
def rms_dbfs(x: np.ndarray) -> float                            # 20*log10(rms+1e-12), rounded 1 decimal
```
**Steps:** implement; vectorise (no Python loops over frames except the per-block cap); make `extract_peaks` work for any
length ≥ 512 samples (catalogue tracks use the full song); frames in `pack_peaks` must be < 65536 (assert; device windows have 311).
**Tests (`core/tests/test_peaks.py`):**
1. Sine at 1000 Hz (bin 64) for 10 s + low noise → most peaks have bin 63–65.
2. Two tones (440 Hz, 2000 Hz) alternating every 0.5 s → peaks at bins ≈28 and 128, alternating.
3. Frame count for 80,000 samples == 311.
4. `unpack_peaks(pack_peaks(p))` frames/bins identical, mag within 0.25 dB.
5. Determinism: run twice → identical bytes; write `core/tests/golden/tone_mix.sha256` and assert.
6. Density: no 32-frame block has > 30 peaks.
7. Performance: one window < 150 ms on the laptop (print it; warn only).
8. Out-of-band: no bin < 7 or > 224.
**DoD:** all tests green; `pytest core -q` green (including B's tests once merged); handoff written by 13:35.

### MC0 (13:45) — your part
Run `scripts\cp0_a.ps1`: installs core, runs core tests, computes `sha256(pack_peaks(extract_peaks(load('core/tests/data/tone_mix.wav'))))`, prints it. B prints the same on Laptop B → must be equal. Sign `contract/fixtures/batches/tv_batch_valid.json` content with `core.signing` and verify B's signature works on your machine.

### A-T02 · Audio capture + scheduler — Mon 14:00–15:00
**Files:** `device/agent/capture.py`, `device/agent/scheduler.py`, `device/tests/test_capture.py`.
**Design**
- `MicSource(device=None, samplerate=48000, blocksize=4800)`: opens `sounddevice.InputStream(channels=1, dtype="float32")` on the WASAPI host API if available (find via `sd.query_hostapis()`), pushes blocks into a thread-safe ring buffer holding 30 s.
- `FileSource(path, realtime=True|False)`: decodes WAV/MP3 (soundfile, or ffmpeg subprocess for mp3) to 48 kHz mono float32 and yields blocks (used by virtual meters and tests).
- `Scheduler(mode)`: `demo` = a 10 s window every 10 s (continuous); `field` = 10 s every 60 s. Yields `Window(start_utc, samples48)` where `start_utc` is the wall-clock UTC time of the first sample (use `time.time_ns()` captured in the stream callback with the input latency subtracted: `callback time_info.inputBufferAdcTime` if available, else `time.time()`).
- Per window: `x8 = resample_to_8k(x48)`, `loudness = rms_dbfs(x48)`, `peaks = extract_peaks(x8)`.
- `--save-debug-wav` writes each window to `data/debug/` (gitignored) for inspection.
**Tests:** FileSource of a test WAV through Scheduler(demo) yields windows of exactly 480,000 samples (48k×10) and 80,000 after resample; start times increase by 10 s in demo, 60 s in field.
**DoD:** `python -m device.agent.capture --seconds 25 --mode demo` prints 2 windows with peak counts (≥50 when music plays, few when silent) and loudness.

### A-T03 · Key provisioning + batch builder — Mon 15:20–15:50
**Files:** `device/tools/provision.py`, `device/agent/batcher.py`, `device/agent/state.py`, `device/config/M-07.toml`, `device/tests/test_batcher.py`.
- `provision.py --meter M-07 --venue V-BUEA-CIMFEST [--cell 624-01-0412-01733]`: generates a 32-byte seed (`nacl.signing.SigningKey.generate()`), saves `device/secrets/M-07.seed` (hex), writes `contract/meters/M-07.pub.json` (contract §3.3) and `device/config/M-07.toml`:
```toml
meter_id = "M-07"
venue_id = "V-BUEA-CIMFEST"
cell_id = "624-01-0412-01733"
fw = "agent-0.1.0"
mode = "demo"                    # demo | field
sonacam_url = "http://192.168.137.1:8000"
input = "mic"                    # mic | file:<path>
heartbeat_s = 60
```
- Provision also meters `M-11, M-12, M-13` (virtual) with venues `V-DLA-AKWA01`, `V-YDE-BASTO1`, `V-BUEA-MOLYK1` (these venue IDs must exist in the mock month `venues.csv`, A-T10).
- `state.py`: SQLite `device/state/<meter>.db`, table `meta(key TEXT PRIMARY KEY, value TEXT)` holding `last_seq`; `next_seq()` is atomic (transaction) and persists before the batch is built.
- `batcher.py`: `build_batch(cfg, window, peaks, loudness) -> dict` using `core.schema.Batch` and `core.signing.sign_batch(seed, batch_dict)`.
**Tests:** signature verifies with `core.signing.verify_batch(pub, batch)`; seq survives process restart; tampering one char in `peaks_b64` fails verification; JSON validates against `contract/schemas/batch.schema.json`.
**DoD:** `python -m device.agent.batcher --selftest` prints a valid signed batch; pub keys committed for M-07, M-11..M-13.

### A-T04 · Fake SONACAM — Mon 15:50–16:15
**File:** `device/tools/fake_sonacam.py` (FastAPI, port 8001).
Implements `POST /v1/batches`, `/v1/batches/bulk`, `/v1/heartbeat` exactly as contract §4 (verify signatures from `contract/meters/*.pub.json`, duplicate/seq_conflict logic in memory, 422 on bad fp_version). Extra controls for tests: `POST /_chaos {"fail_rate":0.5,"latency_ms":800,"down":true}`.
**DoD:** `uvicorn device.tools.fake_sonacam:app --port 8001` and the contract test vectors produce the expected codes (write `device/tests/test_fake_sonacam.py`).

### A-T05 · Outbox + uploader + heartbeat — Mon 16:15–17:15
**Files:** `device/agent/outbox.py`, `device/agent/uploader.py`, `device/tests/test_outbox.py`, `device/tests/test_uploader.py`.
- Outbox SQLite (same db as state): `outbox(seq INTEGER PRIMARY KEY, json TEXT NOT NULL, created_at TEXT, attempts INT DEFAULT 0, last_error TEXT)`, `dead(seq INTEGER PRIMARY KEY, json TEXT, reason TEXT, moved_at TEXT)`.
- `put(batch)` inserts; `peek(n)` oldest first; `ack(seq)` deletes; `to_dead(seq, reason)`.
- Uploader thread: loop — if queue > 10 use `/v1/batches/bulk` with ≤50; else single POST; handle each response code exactly per contract table; backoff 1,2,4,8,16,30 s with ±20% jitter on errors/timeouts (httpx timeout 5 s); never delete before ack.
- Heartbeat every `heartbeat_s`: signed, includes `queued`; store `server_time` skew = server_time − local now.
**Tests:** with fake_sonacam: 100 batches with `fail_rate 0.5` → all eventually acked, none lost, no duplicates counted; `down:true` for 30 s then up → drain; process kill mid-upload → restart resumes; 409 → dead table.
**DoD:** tests green; manual: agent running against fake server, toggle `down` and watch queue go up and drain.

### MC1 prep (17:15) and joint test (17:30)
`scripts\cp1_a.ps1`: runs tests, `netcheck`, then starts the agent against B. Joint test per 02 §7 (MC1 row).
If B is not ready, record result against fake_sonacam and continue.

### A-T06 · Agent runner + status screen — Mon 18:15–19:00
**Files:** `device/agent/__main__.py`, `device/agent/tui.py`.
- `python -m device.agent run --config device/config/M-07.toml [--mode demo|field] [--input file:...]` wires Source → Scheduler → extract → Batcher → Outbox; Uploader + Heartbeat threads.
- `rich.live` panel (refresh 4 Hz): meter/venue, mode, mic level bar (dBFS), last window peaks count, windows sent, **queued**, last ack seq, last error, clock skew vs server, network state (OK/RETRYING/DOWN), and *optional after MC2*: last match title from SSE `GET /api/v1/feed` filtered by `meter_id` (use httpx stream; ignore if unavailable).
- Ctrl+C = graceful stop (flush state).
**DoD:** run for 5 minutes with music → no errors, queue stays 0 when online.

### A-T07 · Virtual meters + scenarios — Mon 19:30–20:45
**Files:** `device/virtual/__main__.py`, `device/virtual/noise.py`, `scenarios/*.ps1`, `data/playlists/*.m3u` (gitignored content; commit example `.m3u.example`).
- `python -m device.virtual --config device/config/M-11.toml --playlist data/playlists/akwa.m3u [--noise data/recordings/crowd.wav --snr-db 10] [--time simulated --start 2026-11-24T19:00:00Z --speed 20]`.
- **Realtime mode:** decodes songs back-to-back, analyses windows like the mic agent, sends with real timestamps.
- **Simulated time mode:** processes as fast as `--speed` allows, timestamps = simulated clock (used to replay a whole evening in minutes; batches are "late" by design; B handles event time).
- Noise mixing at a target SNR; optional clock offset `--clock-offset-s`.
- Scenarios:
  - `scenarios\loop_artist.ps1`: virtual meter M-12 plays song X on repeat 8 times, with 3 other songs inserted after the 4th, simulated time → expect B weights 1,1,.5,.25,0,… (shows cooldown live).
  - `scenarios\network_cut.ps1`: instructions + script that disables the Wi-Fi adapter for 120 s (`Disable-NetAdapter`/`Enable-NetAdapter`, needs admin) while the agent runs.
**DoD:** one virtual meter sends to fake_sonacam in both modes; loop scenario produces the expected batch sequence (check counts in fake server log).

### A-T08 · Network, clock, insurance — Mon 20:45–21:30
**Files:** `scripts/netcheck.ps1`, `device/tools/clockcheck.py`, README "Device → Networking" section (your section).
- `netcheck.ps1 -Url http://192.168.137.1:8000`: `Test-NetConnection -ComputerName <ip> -Port 8000`, `Invoke-RestMethod <url>/health`, prints Wi-Fi profile category (must be Private: `Get-NetConnectionProfile`), suggests `Set-NetConnectionProfile -InterfaceAlias "Wi-Fi" -NetworkCategory Private`.
- `clockcheck.py`: compares local UTC with B's `/health` `server_time` and with an NTP query (`time.windows.com`) if internet; prints skew.
- Install Docker Desktop on Laptop A (insurance, 02 §9) and test `docker run hello-world`; do NOT run B's stack unless needed.
**DoD:** netcheck green against B (or documented why not).

### A-T09 · Hall recordings dataset + real test vectors — Mon 21:30–23:00
**Files:** `device/tools/record.py`, `data/recordings/*` (gitignored), `data/recordings/labels.csv` (gitignored; copy to B via USB/shared folder), `contract/fixtures/batches/tv_batch_real_01..05.json` (committed).
- For each consented demo song (target 8–12 songs): play on the Bluetooth speaker in the hall; record 60 s at **1 m, 2 m, 3 m**, conditions **quiet** and **noisy** (crowd noise played from a second phone, or the real hall). `record.py --song "<title>" --dist 2 --cond noisy --seconds 60`.
- `labels.csv`: `file,song_title,distance_m,condition,song_offset_start_s,recorded_at`.
- Also record **5 minutes of crowd/hall noise without music** → `crowd.wav` (used by virtual meters and to test "no music" behaviour).
- Produce 5 real signed batches from these recordings → commit as fixtures (B uses them to test matcher before/after MC2).
**DoD:** ≥30 labelled recordings exist; shared with B; fixtures committed.

### A-T10 · Mock month generator — Mon 23:00–00:45 and Tue 06:45–07:30
**Files:** `tools/mockgen/generate.py`, `tools/mockgen/cities.py`, `tools/mockgen/tests/test_mockgen.py`, output `contract/fixtures/mock_month/*.csv` + `scenarios.md` (format: contract §7).
**Model (document all numbers as "illustrative" in scenarios.md):**
- **Venues/meters (30 meters):** Douala 8, Yaoundé 8, Buea 3 (+ the live `V-BUEA-CIMFEST`), Limbe 2, Bamenda 3, Bafoussam 2, Garoua 2, Maroua 1, Kribi 1. Size mix S/M/L ≈ 50/35/15%. Coordinates = city centre + random jitter ≤ 0.03°. City centres: Douala 4.0511,9.7679; Yaoundé 3.8480,11.5021; Buea 4.1527,9.2410; Limbe 4.0186,9.2043; Bamenda 5.9631,10.1591; Bafoussam 5.4778,10.4176; Garoua 9.3014,13.3977; Maroua 10.5910,14.3159; Kribi 2.9395,9.9095.
- **bars_in_stratum** (illustrative; union press figures: Douala 8,500+, Yaoundé 6,500+): Douala S 4250 / M 2975 / L 1275; Yaoundé S 3250 / M 2275 / L 975; other cities total 400–1,500 split the same way. Mark "illustrative".
- **Songs:** 200 synthetic songs, popularity Zipf (s=1.05), durations 2:40–5:30; 120 artists (some with several songs); owners: singer 50% / composer 30% / producer 20% with fake MoMo `+2376XXXXXXXX` patterns (`+237 6 99 00 0xx xx`).
- **Nights:** venue open hours from `venues.csv` (local), Fri/Sat ×1.4 plays, Sun ×0.8; 13–16 songs per hour; each venue has a "taste" (random Dirichlet over genres) blended 70/30 with global popularity.
- **Truth → observed:** each meter has `catch_rate_true` 0.70–0.92; observed plays = true plays kept with that probability; loudness_db per play from venue size and hour (L busy −14 dB, S quiet −28 dB, noise σ 3 dB).
- **Scenarios (must exist, described in scenarios.md):**
  1. `loop`: song `S-M0150` (low popularity) looped at `V-DLA-AKWA01` every Fri/Sat 22:00–01:00 (≈ 12 loops/night) — B's rules must neutralise it.
  2. `promo`: `S-M0077` "new release" heavily rotated in 5 venues during the last 7 days (real but bought plays).
  3. `power_cut`: meter `M-15` offline 2 nights (no windows → listening fraction drops).
  4. `moved_meter`: `M-22` cell_id changes on day 18 (B flags `meter_moved` if it gets cell ids — include a `cell_id` column in windows file for M-22 only, or document as not modelled).
  5. `quiet_hours`: a venue plays one song at 04:00–05:00 to an empty room (low loudness).
- **Windows:** `windows_observed.csv` in 10-minute slots: `windows_expected` = 60 (demo-equivalent) or 10 (field), `windows_received` = expected unless outage/power cut.
- Seeded (`--seed 42`) and deterministic; prints summary (rows per file, plays per scenario, top 10 songs truth).
**Tests:** CSV headers exactly as contract; IDs unique; all `venue_id` in meters exist; owner shares per song sum to 100; deterministic hash of outputs for seed 42.
**DoD:** CSVs committed (size ≤ 25 MB total; if bigger, gzip and document) and pushed to `a/work` by **Tue 07:30** (B imports from `origin/a/work` at 09:30). Tell B in your handoff.

### A-T11 · Web foundation + SONACAM dashboard — Tue 07:30–10:45
**Files:** `web/index.html`, `web/css/app.css`, `web/js/api.js`, `web/js/dashboard.js`, `web/js/map.js`, `web/js/feed.js`, `web/js/format.js`, `web/assets/cameroon.svg`, `web/assets/fonts/Inter-*.woff2` (+ `OFL.txt`).
**Rules:** vanilla HTML/CSS/ES modules, **no CDN** (works offline), no build step. White background, Inter font (bundled, OFL — declare), one accent teal `#12897a`, warning amber `#b5770f`, error red `#c0392f`. Numbers tabular. Must look good at 1920×1080 on the projector and readable at 1280×720.
**`api.js`:** `get(path, params)` → fetch `/api/v1/...`; if URL has `?mock=1`, loads `contract/fixtures/api/<name>.json` instead (serve repo root during dev with `python -m http.server 8080` and open `http://localhost:8080/web/index.html?mock=1`). `feed(onEvent)` → `EventSource('/api/v1/feed')`; in mock mode replays `feed_sample.txt` every 2 s.
**Dashboard layout (single screen, no scrolling at 1080p):**
1. Header: "SoundProof · SONACAM view", period switch (Live / Mock month), rule version, **ledger banner** (green "Log intact · last anchor 21:00 (file)" / red "Log mismatch at play P-… · payouts frozen").
2. KPI row: meters online/total, plays raw, plays counted, songs ready, unidentified music minutes.
3. Left: **map** (`cameroon.svg` outline drawn from the CMR polygon in `tools/mockgen/cities.py`, projected lon/lat → x/y) with venue dots sized by plays; on SSE `play` the venue dot pulses (CSS animation) and a label shows the title for 4 s. The live venue `V-BUEA-CIMFEST` has a distinct marker.
4. Middle: **live feed** (last 12 events: match/play/flag/ledger, newest on top, colour-coded).
5. Right: **shares table** top 15: rank, title, artist, raw plays, counted plays, share % with a range bar (ci_low–ci_high), amount FCFA, flag chips; toggle **Raw | Standardised** re-fetches `mode=`.
6. On SSE `stats` → refetch shares; on `ledger` → update banner.
**DoD:** with `?mock=1` everything renders from fixtures; against B (after MC2) shows real data; no console errors.

### A-T12 · Upload portal — Tue 10:45–12:15
**Files:** `web/portal.html`, `web/js/portal.js`.
- Form: file, title, artist name, ISRC, ISWC, owners repeater (name, role select, share %, MoMo), consent checkbox (required) with text "I own or represent the rights to this recording and agree to its use for royalty monitoring". Validation: shares sum 100, file ≤ 30 MB.
- Submit via `fetch` multipart `POST /api/v1/songs`; show progress (XHR upload progress or a spinner).
- Below: catalogue list (`GET /api/v1/songs`) polling every 2 s while any song is `processing`; status chips: uploaded (grey) / processing (amber, animated) / ready (teal) / duplicate_suspected (red, "possible duplicate of S-… — waiting for SONACAM review") / failed.
**DoD:** upload of a real song on B goes to ready; a second upload of the same file shows duplicate_suspected.

### MC2 (12:30) — your part
`scripts\cp2_a.ps1`. Joint test per 02 §7. Then prepare the clinic demo (13:00–14:00): pick 4 demo songs, check speaker volume, run agent in demo mode, dashboard on the projector/second screen.

### Industry clinic 14:00–14:45
Run the live demo for artists. Lead (human) asks the three questions (do you pay bars to play new songs? how do you know they did? would you pay to see it?) and films answers with consent. You write observations in `notes/clinic.md` (yours): what they understood, what confused them, feature requests. Do not code during the clinic.

### A-T13 · Payouts, compare-flat, ledger banner, toggle polish — Tue 14:45–15:20
**Files:** `web/payouts.html` (or a panel/tab inside index.html — choose a tab inside index to keep one screen: "Shares | Money | Trust"), `web/js/money.js`, `web/js/trust.js`.
- Money tab: pot input (default 100,000,000), payouts table grouped by song → owners, total = pot (show check mark), MoMo status "sent (simulated)"; "Flat 27,000 vs SoundProof" card from `compare-flat`.
- Trust tab: ledger status, last anchors list, a "How to verify" box; when `frozen` show a red overlay on Money tab.
**DoD (MC3):** toggle changes ranking; payouts total equals pot; tamper → red within 2 s via SSE.

### A-T14 · Artist page — Tue 16:15–17:30
**Files:** `web/artist.html`, `web/js/artist.js`. `GET /api/v1/artists`, `/api/v1/artists/{name}`.
- Phone-friendly (this is what the artist's phone shows in the demo): name, total counted plays, amount FCFA, plays by venue (list with city), plays by day (small SVG bar chart), recent plays (live via SSE filtered by the artist's songs) with a subtle notification banner "Your song was just played at CIMFEST, Buea".
- Proof-of-play section: "Bars that played your song" with counts — the second product (artists who pay bars for promotion).
**DoD:** opens on a phone browser over the hotspot (`http://192.168.137.1:8000/artist.html?name=...`) and updates live.

### A-T15 · Calibration tool — Tue 17:30–18:30
**File:** `device/tools/calibrate.py`.
- Plays a list of known catalogue songs (local files) through the laptop/speaker output (`sounddevice.play`) one after another (60 s each) while the agent runs, logs `expected=[{song_id, played_at}]`, then signs and POSTs `/v1/calibration`. Prints B's computed catch rate when available (`GET /api/v1/venues` → meter.catch_rate).
**DoD:** calibration run of 4 songs results in a catch rate stored on B for M-07.

### A-T16 · Evaluation harness + accuracy report — Tue 18:30–20:00
**Files:** `device/tools/eval.py`, `notes/eval_report.md`.
- Builds a local index from `data/songs/*` using `core.peaks` + `core.landmarks` (B's module, available since MC0), runs every recording in `data/recordings/` in 10 s windows, records accepted/rejected, correct/incorrect, by distance and condition. Also runs the 5-minute crowd noise → must produce **zero** accepted matches (false-positive check).
- Report table: hit rate per distance × condition, false positives per hour, median confidence. These are the **only** accuracy numbers allowed in the pitch.
- If hit rate at 2 m noisy < 70%, propose parameter tweaks to B **in person** (02 §6 rule 5) — e.g. `THRESH_DB` 6→4 or `PEAKS_PER_BLOCK` 30→40 — and only if both agree before 21:00; B re-indexes.
**DoD:** report committed with real numbers.

### A-T17 · Admin page — Tue 20:30–21:15 (*optional if late*)
`web/admin.html`, `web/js/admin.js`: meters table (online, last seen, queued, catch rate), upload a `.pub.json` (POST `/v1/meters/register`), buttons: "Run stats now", "Reset live demo data" (POST `/api/v1/demo/reset`, confirm in-page — no `confirm()` dialogs), approve duplicate-suspected songs.

### A-T18 · Rehearsal, video, README — Tue 21:15–23:00
- Rehearse the brief §12 demo script **twice** end-to-end with B, timing it (live part ≤ 70 s).
- Record the 2-minute demo video (OBS Studio screen capture + phone camera for the speaker/meter shot; voice-over by the lead). Export 1080p MP4, ≤ 100 MB. Store outside git; upload link in README.
- README sections you own: "Device (Laptop A)", "Web", "Networking", "Running a virtual meter", "Recording the dataset", "Accuracy (measured)". Add every library you used to "Declarations".
**DoD (MC4):** demo twice without code changes; video file exists.

### A-T19 · Wednesday — 07:30–09:30
Fix only bugs found in rehearsal. Help the lead with screenshots for the 6 slides (problem, solution, demo, users, business model, team). Write the "what we built during the hackathon vs before" paragraph draft: *"Before Monday 12:00: research, design documents and hardware renders only. All code in the repository was written during the 48 hours with Claude Code (declared), using the open-source libraries listed in Declarations."*
MC5 at 09:30 → submit by 10:00.

---

## 4. Your test and quality checklist (run before every handoff)
- `scripts\test_a.ps1` green.
- No secrets or audio in `git status` (seed files, `.wav`, `.mp3`, `data/`).
- Contract fixtures still validate (`python -m jsonschema`-based test in `device/tests/test_contract_fixtures.py`).
- The web opens with `?mock=1` without console errors.

## 5. Things you must never do
- Edit B's files or the locked docs/constants.
- Change fingerprint parameters on your own.
- Commit audio, recordings, consent screenshots, keys.
- Claim an accuracy number you did not measure in A-T16.
- Use songs without written consent in the demo.
