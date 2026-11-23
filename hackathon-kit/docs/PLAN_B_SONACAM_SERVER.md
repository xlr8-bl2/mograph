# PLAN B — Person B / Laptop B: the SONACAM server (catalogue, matching, plays, ledger, stats, payouts, API)

> You are the agent on **Laptop B (Windows + Docker Desktop/WSL2)**. Read `docs/00_PROJECT_BRIEF.md`,
> `docs/01_CONTRACT.md`, `docs/02_WORKFLOW_HANDOFFS_MERGES.md` first. This plan lists **your** tasks in order with time
> boxes, exact files, schema, steps, tests and Definition of Done (DoD). Section 2 tells you **everything Person A is
> doing**, so you know what will arrive, when, and how to test without it. You are also the **merge captain**.

---

## 0. Your mission in one paragraph
Laptop B **is SONACAM**. You build the server that: accepts signed batches from meters; lets artists upload songs and
fingerprints them with the shared extractor; matches incoming windows to songs; turns matches into plays using event
time; chains plays in a tamper-evident ledger with hourly public anchors; applies the agreed fair-play standardisation
(`sr-1.0`) and the sampling estimation; splits the pot into payouts; and serves the read API + live feed that A's web
pages display. You also host the hotspot, serve `web/`, and own `main`.

Your folders: `server/`, `docker-compose.yml`, `core/soundproof_core/{schema,signing,landmarks,constants}.py` (+ tests),
`contract/schemas/`, `contract/fixtures/api/`, `contract/fixtures/batches/` (initial), `contract/CHANGES.md` (maintainer),
`scripts/*_b.ps1`, `handoffs/B/`, `STATUS_B.md`, repo bootstrap files.
Never edit `device/`, `web/`, `tools/mockgen/`, `core/.../peaks.py`, `contract/fixtures/mock_month/`, `contract/meters/`.

---

## 1. Timeline at a glance

| When | Task | Merge checkpoint |
|---|---|---|
| Mon 12:00–12:20 | B-T00 Bootstrap the repository | |
| Mon 12:20–13:40 | B-T01 core schema + signing + landmarks + JSON Schemas + test vectors | **MC0 13:45** |
| Mon 14:00–15:00 | B-T02 Docker stack + DB migrations + health + firewall | |
| Mon 15:00–15:15 | Mentor round 1 (human) | |
| Mon 15:15–16:00 | B-T03 Ingest API + meter registry + API fixtures | |
| Mon 16:00–16:50 | B-T04 Catalogue upload + fingerprint worker + duplicate check | |
| Mon 16:50–17:15 | B-T05 Matcher worker | **MC1 17:30** (you merge) |
| Mon 18:00 | Official checkpoint 1 | |
| Mon 18:15–19:30 | B-T06 Play logger | |
| Mon 19:30–20:00 | Dinner | |
| Mon 20:00–21:00 | B-T07 Ledger + auditor + FileAnchor + tamper tool | |
| Mon 21:00–22:30 | B-T08 Read API v1 + SSE feed + static hosting of `web/` | |
| Mon 22:30–00:45 | B-T09 Stats part 1: fair-play weights `sr-1.0` | |
| Mon 00:45–06:30 | **Sleep** (handoff first) | |
| Tue 06:45–09:00 | B-T10 Stats part 2: estimation + bootstrap + shares API | |
| Tue 09:00 / 10:00 | Stand-up / Mentor 2 (human) | |
| Tue 09:30–11:00 | B-T11 Mock month importer + compare-flat | |
| Tue 11:00–12:15 | B-T12 Payouts + WIPO CSV + frozen logic | **MC2 12:30** (you merge) |
| Tue 13:15–15:15 | B-T13 Flags, raw/standardised everywhere, demo reset, tamper polish (keep clinic demo running at 14:00) | **MC3 15:30** |
| Tue 16:15–17:30 | B-T14 Calibration intake + catch rates + clock-skew/moved-meter flags | |
| Tue 17:30–19:00 | B-T15 Sepolia anchor (*stretch*) | |
| Tue 20:30–22:00 | B-T16 Robustness, restart safety, performance, demo seed | |
| Tue 22:00–23:00 | B-T17 README (server) + declarations + tools | **MC4 23:00** |
| Wed 07:30–09:30 | B-T18 Fixes only, fresh-clone test, tag v1.0 | **MC5 09:30** |

---

## 2. What Person A is doing (full context)

| When | A task | What it gives you | How you test without it |
|---|---|---|---|
| Mon 12:00–12:20 | **A-T00** Setup, clone, mic check | — | — |
| Mon 12:20–13:40 | **A-T01** `core/peaks.py` (`resample_to_8k`, `spectrogram_db`, `extract_peaks`, `pack_peaks`, `unpack_peaks`, `peaks_to_b64`, `b64_to_peaks`, `rms_dbfs`) | The extractor your fingerprint worker uses | Before MC0: write your landmark tests on synthetic peak arrays |
| Mon 14:00–15:00 | **A-T02** Mic capture + scheduler (demo 10s/10s, field 10s/60s) | — | — |
| Mon 15:20–15:50 | **A-T03** Key provisioning (`contract/meters/M-07.pub.json`, M-11..13) + batch builder | Real public keys + batches | Your test vectors / `server/tools/make_batches_from_wav.py` |
| Mon 15:50–16:15 | **A-T04** `fake_sonacam.py` (A's stand-in for you) | — | — |
| Mon 16:15–17:15 | **A-T05** Outbox + uploader + heartbeat (uses `/v1/batches`, `/bulk`, `/heartbeat`) | Retries, duplicates, late batches will hit you | Send fixtures with `server/tools/send_fixtures.py` |
| Mon 17:30 | **MC1** | | |
| Mon 18:15–19:00 | **A-T06** Agent runner + status TUI (subscribes to your SSE feed later) | | |
| Mon 19:30–20:45 | **A-T07** Virtual meters (WAV-fed; realtime or **simulated time** → late, out-of-order event times by design) + loop and network-cut scenarios | Stress for your event-time logic | `make_batches_from_wav.py --simulated` |
| Mon 20:45–21:30 | **A-T08** netcheck/clockcheck; installs Docker on A as insurance | Expects `/health` with `server_time` | — |
| Mon 21:30–23:00 | **A-T09** Hall recordings (≥30 labelled WAVs, copied to you by USB) + `tv_batch_real_01..05.json` | Real noisy test data for the matcher | Synthetic noise mixes |
| Mon 23:00 → Tue 07:30 | **A-T10** Mock month CSVs in `contract/fixtures/mock_month/` (contract §7), pushed to `a/work` by Tue 07:30 | Input for B-T11 importer | Make a 2-venue mini CSV yourself for unit tests |
| Tue 07:30–10:45 | **A-T11** Web foundation + SONACAM dashboard (`web/index.html`), uses your read API + SSE | Consumer of your API | — |
| Tue 10:45–12:15 | **A-T12** Upload portal (`web/portal.html`) → `POST /api/v1/songs`, `GET /api/v1/songs` | | |
| Tue 12:30 | **MC2** | | |
| Tue 14:00 | Industry clinic demo (A runs it, you keep the server stable) | | |
| Tue 14:45–15:20 | **A-T13** Money + Trust tabs (payouts, compare-flat, ledger banner, raw/standardised toggle) | | |
| Tue 15:30 | **MC3** | | |
| Tue 16:15–17:30 | **A-T14** Artist page (phone) | uses `/api/v1/artists/*` + SSE | |
| Tue 17:30–18:30 | **A-T15** Calibration tool → `POST /v1/calibration` | | |
| Tue 18:30–20:00 | **A-T16** Evaluation harness (uses your `core.landmarks`) → measured accuracy; may request parameter change *in person before 21:00* | | |
| Tue 20:30–21:15 | **A-T17** Admin page (meters, register key, run stats, demo reset, approve duplicates) | | |
| Tue 21:15–23:00 | **A-T18** Rehearsal ×2, video, README A sections | | |
| Tue 23:00 | **MC4** | | |
| Wed 07:30–09:30 | **A-T19** Fixes, submission material | | |

---

## 3. Your tasks in detail

### B-T00 · Bootstrap the repository — Mon 12:00–12:20
1. Create private GitHub repo `soundproof`; add Person A as collaborator.
2. Create the layout from 02 §1 with empty `__init__.py` files and `.gitkeep`s.
3. Copy `docs/*` (these plans) and the `repo-root/` kit files (`CLAUDE.md`, `STATUS_A.md`, `STATUS_B.md`, `DECISIONS.md`, `REQUESTS.md`, `handoffs/TEMPLATE.md`) into the repo.
4. `.gitignore`: `.venv/ __pycache__/ *.pyc data/ device/secrets/ device/state/ server/anchors/*.jsonl .env *.wav *.mp3 *.m4a *.flac *.seed node_modules/ .pytest_cache/ media/`.
5. `.gitattributes`: as in 02 §2.
6. `core/pyproject.toml` (name `soundproof-core`, version 0.1.0, deps `numpy==2.1.3 scipy==1.14.1 pynacl==1.5.0 pydantic==2.9.2`), `core/soundproof_core/constants.py` **verbatim** from contract §2, `core/soundproof_core/__init__.py` exporting `FP_VERSION`.
7. `contract/CHANGES.md` with `## c-1.0 …`; `README.md` skeleton (title, one-line pitch, Quick start placeholders, Declarations table, "Built with Claude Code").
8. `scripts/test_b.ps1` (runs `pytest core/tests/test_schema.py core/tests/test_signing.py core/tests/test_landmarks.py server/tests -q`).
9. Commit `[B-T00] bootstrap`, push `main`, create and push `a/work` and `b/work` from `main`. Tell A.
**DoD:** A has cloned; both branches exist.

### B-T01 · Core: schema, signing, landmarks, schemas, test vectors — Mon 12:20–13:40
**Files:** `core/soundproof_core/schema.py`, `signing.py`, `landmarks.py`, `core/tests/test_schema.py`, `test_signing.py`, `test_landmarks.py`, `contract/schemas/*.json` (all listed in contract §9), `contract/fixtures/batches/tv_*.json`, `tv_keys.json`.
- `schema.py`: pydantic v2 `Batch` with validators per contract §3.1 (regexes, `v==1`, `fp_version==FP_VERSION`, `window_ms==10000`, `loudness_db` rounded to 1 decimal, `n_peaks == len(b64decode(peaks_b64))/4`, frame ≤ 310, bin in 7..224). `canonical_bytes(d: dict) -> bytes` per contract §3.2. `Heartbeat`, `Calibration` models.
- `signing.py`: `generate_seed() -> bytes`, `public_key_b64(seed)`, `sign_dict(seed, d) -> str` (adds nothing; returns b64 sig over canonical bytes of d without `sig`), `verify_dict(pub_b64, d) -> bool`, `load_pub(path)`, `load_seed(path)`.
- `landmarks.py`: `make_hashes(peaks) -> np.ndarray[(hash uint32, t int32)]` per brief §5.2 (vectorise with numpy where practical; correctness first); `class MemoryIndex` (dict hash → list of (song_id, t)); `match_window(window_hashes, index) -> MatchResult(song_id|None, offset_frames, best_score, second_score, confidence, accepted)` per brief §5.3.
- Tests: canonical bytes stable regardless of key order; sign/verify roundtrip; tampered payload fails; schema rejects bad fp_version / seq 0 / frame 400; `make_hashes` on a tiny hand-made peak list gives expected hashes (write them out by hand); `match_window`: build index from synthetic peaks of 3 "songs" (random but seeded), query with a slice of song 2 shifted by 100 frames + 30% peaks dropped + 20% random extra peaks → accepted, correct song, offset 100±1; query with random peaks → not accepted.
- JSON Schemas (draft 2020-12) for every contract object; test vectors: valid, bad signature, bad version (synthetic peaks).
**DoD:** all core tests green. Handoff by 13:35.

### MC0 (13:45) — you are merge captain
Merge `b/work` then `a/work` into `main`; run `scripts\cp0_b.ps1` (installs core, runs all core tests, prints the golden SHA of `extract_peaks` on `core/tests/data/tone_mix.wav`, verifies a batch signed on A). Tag `cp-0`.

### B-T02 · Docker stack, DB, health — Mon 14:00–15:00
**Files:** `docker-compose.yml`, `server/Dockerfile`, `server/requirements.txt`, `server/config.py`, `server/db/__init__.py`, `server/db/migrate.py`, `server/db/migrations/001_init.sql`, `server/api/main.py` (skeleton), `.env.example`, `scripts/firewall_b.ps1`.
- `server/requirements.txt`: `fastapi==0.115.5 uvicorn[standard]==0.32.1 psycopg[binary]==3.2.3 python-multipart==0.0.17 pydantic==2.9.2 numpy==2.1.3 scipy==1.14.1 pandas==2.2.3 sse-starlette==2.1.3 jsonschema==4.23.0 pynacl==1.5.0 httpx==0.27.2 pytest==8.3.3` (+ `web3==7.6.0` only in B-T15).
- `Dockerfile`: `python:3.11-slim`, `apt-get install -y ffmpeg`, copy `core/` and `server/`, `pip install -e core -r server/requirements.txt`, workdir `/app`.
- `docker-compose.yml` services (all same image, different commands): `db` (postgres:16, volume `pgdata`, env from `.env`, healthcheck `pg_isready`), `api` (`uvicorn server.api.main:app --host 0.0.0.0 --port 8000`, ports `8000:8000`, mounts `./web:/app/web:ro` and `./contract:/app/contract:ro` and `./media:/app/media`), `worker_fp` (`python -m server.workers.fingerprint`), `worker_match` (`python -m server.workers.matcher`), `worker_play` (`python -m server.workers.playlogger`), `scheduler` (`python -m server.workers.scheduler`: hourly anchors, stats every 5 min, meter online/offline). All depend on `db` healthy; `restart: unless-stopped`.
- Migrations: `migrate.py` applies `server/db/migrations/*.sql` in order, recording them in `schema_migrations`; run at api start.
- **Schema `001_init.sql`** (types: `text` IDs, `timestamptz`):
```sql
CREATE TABLE venues (venue_id text PRIMARY KEY, name text, city text, lat double precision, lon double precision,
  size_class text, stratum text, bars_in_stratum int, open_hour int DEFAULT 18, close_hour int DEFAULT 2, source text DEFAULT 'live');
CREATE TABLE meters (meter_id text PRIMARY KEY, venue_id text REFERENCES venues, public_key_b64 text NOT NULL,
  last_seq bigint DEFAULT 0, last_seen timestamptz, queued int DEFAULT 0, cell_id text, catch_rate double precision DEFAULT 0.8,
  clock_skew_s double precision, source text DEFAULT 'live');
CREATE TABLE songs (song_id text PRIMARY KEY, title text, artist_name text, isrc text, iswc text, duration_s double precision,
  status text, n_hashes int DEFAULT 0, file_path text, uploaded_at timestamptz DEFAULT now(), duplicate_of text, source text DEFAULT 'upload');
CREATE TABLE owners (id bigserial PRIMARY KEY, song_id text REFERENCES songs, owner_name text, role text, share_pct double precision, momo text);
CREATE TABLE fp_hashes (hash int NOT NULL, song_id text NOT NULL, t int NOT NULL);
CREATE INDEX fp_hashes_hash ON fp_hashes(hash);
CREATE TABLE raw_batches (id bigserial PRIMARY KEY, meter_id text, venue_id text, seq bigint, window_start timestamptz,
  received_at timestamptz DEFAULT now(), body jsonb, late boolean DEFAULT false, processed boolean DEFAULT false,
  UNIQUE(meter_id, seq));
CREATE TABLE match_events (id bigserial PRIMARY KEY, raw_batch_id bigint REFERENCES raw_batches, meter_id text, venue_id text,
  window_start timestamptz, song_id text, offset_s double precision, best_score int, second_score int, confidence double precision,
  accepted boolean, loudness_db double precision, created_at timestamptz DEFAULT now());
CREATE TABLE plays (play_id text PRIMARY KEY, meter_id text, venue_id text, song_id text, started_at timestamptz,
  ended_at timestamptz, duration_s double precision, first_offset_s double precision, n_windows int, loudness_db double precision,
  source text, prev_chain_hash text, chain_hash text, inserted_at timestamptz DEFAULT now(), seq_no bigserial UNIQUE);
CREATE TABLE play_corrections (id bigserial PRIMARY KEY, play_id text, reason text, detail jsonb, created_at timestamptz DEFAULT now());
CREATE TABLE play_weights (play_id text, rule_version text, stats_run_id text, weight double precision, reasons text[],
  PRIMARY KEY (play_id, stats_run_id));
CREATE TABLE stats_runs (stats_run_id text PRIMARY KEY, period text, mode text, rule_version text, started_at timestamptz,
  finished_at timestamptz, params jsonb);
CREATE TABLE shares (stats_run_id text, song_id text, raw_plays int, weighted_plays double precision, share double precision,
  ci_low double precision, ci_high double precision, flags text[], PRIMARY KEY (stats_run_id, song_id));
CREATE TABLE flags (flag_id bigserial PRIMARY KEY, type text, song_id text, venue_id text, meter_id text, detail text,
  created_at timestamptz DEFAULT now(), stats_run_id text);
CREATE TABLE anchors (hour timestamptz PRIMARY KEY, root text, n_plays int, via text, tx_ref text, created_at timestamptz DEFAULT now());
CREATE TABLE calibrations (run_id text PRIMARY KEY, meter_id text, started_at timestamptz, ended_at timestamptz,
  expected jsonb, matched int, total int, catch_rate double precision);
CREATE TABLE payouts (stats_run_id text, owner_name text, role text, song_id text, amount_fcfa bigint, momo text, status text);
CREATE TABLE jobs (id bigserial PRIMARY KEY, kind text, ref text, status text DEFAULT 'queued', attempts int DEFAULT 0,
  error text, created_at timestamptz DEFAULT now(), updated_at timestamptz DEFAULT now());
CREATE TABLE windows (meter_id text, window_start timestamptz, loudness_db double precision, received boolean, source text,
  PRIMARY KEY (meter_id, window_start));
```
- Job queue pattern for workers: `UPDATE jobs SET status='running', attempts=attempts+1 WHERE id = (SELECT id FROM jobs WHERE kind=%s AND status='queued' ORDER BY id FOR UPDATE SKIP LOCKED LIMIT 1) RETURNING *` ; poll every 0.5 s; on error set `status='error'`, `error=...`; idempotent processing (unique constraints).
- `GET /health` → `{"ok":true,"server_time":"…Z","db":true,"version":"0.1.0"}`.
- `scripts/firewall_b.ps1` (admin): `New-NetFirewallRule -DisplayName "SoundProof 8000" -Direction Inbound -Protocol TCP -LocalPort 8000 -Action Allow -Profile Private`. Document Mobile Hotspot setup in README (brief §11).
**DoD:** `docker compose up -d` → all services healthy; `curl http://localhost:8000/health` OK; from A: `scripts\netcheck.ps1` green.

### B-T03 · Ingest API + meter registry + API fixtures — Mon 15:15–16:00
**Files:** `server/api/ingest.py`, `server/api/meters.py`, `server/registry.py`, `server/tests/test_ingest.py`, `server/tools/send_fixtures.py`, `contract/fixtures/api/*.json` (one per endpoint in contract §6, realistic content, ~15 venues, ~20 songs, ~30 plays; must validate against schemas), `contract/fixtures/api/feed_sample.txt`.
- On startup: load `contract/meters/*.pub.json` → upsert `meters` (+ create the venue row if unknown with `source='live'`, name = venue_id).
- `POST /v1/batches`: validate with `core.schema.Batch` (422 on error), look up meter (404), verify signature (401), check `(meter_id, seq)`: same body → 200 duplicate; different → 409. Insert `raw_batches` (late = window_start < meter's max window_start), update `meters.last_seq,last_seen`, insert `windows` row, enqueue job `match` with ref raw_batch_id, publish SSE `batch` and `meter`.
- `/bulk`, `/heartbeat` (update `last_seen`, `queued`, `clock_skew_s` = server_now − sent_at), `/meters/register`.
- SSE bus: in-process `asyncio` broadcaster in the api; workers publish via Postgres `NOTIFY sp_events, '<json>'` and the api `LISTEN`s and forwards (so workers in other containers can emit events).
**Tests:** contract test vectors → expected codes; duplicate and conflict; late flag; 100 concurrent posts.
**DoD:** A's real agent (or `send_fixtures.py`) → 200 stored; rows visible; SSE `batch` events seen with `curl -N http://localhost:8000/api/v1/feed`.

### B-T04 · Catalogue upload + fingerprint worker + duplicate check — Mon 16:00–16:50
**Files:** `server/api/songs.py`, `server/workers/fingerprint.py`, `server/fp/index.py`, `server/tests/test_songs.py`.
- `POST /api/v1/songs`: validate fields (contract §5), save file to `media/songs/<song_id>.<ext>`, insert `songs(status='uploaded')` + `owners`, enqueue job `fingerprint`. IDs `S-000001` sequential.
- Worker: `ffmpeg -i in -ac 1 -ar 8000 -f f32le -` → numpy; `duration_s`; `extract_peaks` (A's) on the **whole track**; `make_hashes`; **duplicate check** (brief §5.4) using the current in-DB index; if duplicate → `status='duplicate_suspected', duplicate_of=...` and stop; else bulk insert into `fp_hashes` with `COPY`, `n_hashes`, `status='ready'`; NOTIFY event `song` (A's portal polls anyway).
- `server/fp/index.py`: `DbIndex` loads `fp_hashes` into an in-memory dict on first use and refreshes on NOTIFY `song_ready` (fast enough for ≤ 300 songs); provides `lookup(hashes)`.
- `GET /api/v1/songs`, `GET /api/v1/songs/{id}`, `POST /api/v1/songs/{id}/approve`.
- `server/tools/reindex_all.py` (re-fingerprint every song; needed if parameters ever change).
**DoD:** upload 3 songs via curl → ready within ~20 s each; re-upload one → duplicate_suspected.

### B-T05 · Matcher worker — Mon 16:50–17:15
**File:** `server/workers/matcher.py`, `server/tests/test_matcher.py`.
- Takes `match` jobs: load raw batch → `b64_to_peaks` → `make_hashes` (frames relative to window) → `match_window` against `DbIndex` → insert `match_events` (also when rejected), set `processed=true`, enqueue job `play` with ref meter_id, NOTIFY `match` event.
- Test with `make_batches_from_wav.py` (`server/tools/`, uses `core.peaks` to cut a WAV into signed batches with a test key) on an indexed song: ≥90% windows accepted on clean audio.
**DoD (MC1):** A's live agent + song on speaker → accepted matches for ≥2 of 3 songs.

### MC1 (17:30) — merge captain
Merge, run `scripts\cp1_b.ps1` (compose up, health, upload 3 songs, run fixture batches), joint test per 02 §7. Tag `cp-1`.

### B-T06 · Play logger — Mon 18:15–19:30
**File:** `server/workers/playlogger.py`, `server/tests/test_playlogger.py`.
Implement brief §6 exactly. Process per meter in `window_start` order. Persist logger state in a table `playlogger_state(meter_id PK, state jsonb, last_window_start)` so restarts are safe.
- Confirmed play → insert `plays` with `started_at` = the wall time the song started = `first_window_start − first_offset_s` (never earlier than `first_window_start − duration_s`). Store `first_offset_s`, `n_windows`, mean loudness and `source`: `live` for M-07, `virtual` for meters M-11 and above, `mock` for rows written by the mock importer.
- Ledger hook: on insert compute chain hash (B-T07; leave a function stub `ledger.append(play)` now).
- Late batches: if a match event's window_start < state.last_window_start for that meter, reprocess that meter's events for that venue-day from scratch in a temporary state; insert plays that are new; if an already-inserted play differs, write `play_corrections`.
- "Music, unidentified": rejected windows with loudness > −35 dBFS (config) → add to `unidentified` counter per meter/day (table `unidentified(meter_id, day, seconds)`).
- NOTIFY `play`.
**Tests:** (1) a 4-min song heard in 4 consecutive windows with offsets 20,30,40,50 and times +10 s each → 1 play; (2) same song, offsets jump 200→15 → 2 plays; (3) chorus repeat: offsets 60 then 140 while clock +80 s → same play; (4) single stray window → no play; (5) late batch inserted earlier in time → play created in the right order, no duplicate; (6) restart in the middle → no duplicates.
**DoD:** live song → `plays` row within one window after the second accepted window.

### B-T07 · Ledger, auditor, anchors, tamper — Mon 20:00–21:00
**Files:** `server/ledger/chain.py`, `server/ledger/merkle.py`, `server/ledger/anchor.py`, `server/api/ledger.py`, `server/tools/tamper.py`, `server/tests/test_ledger.py`.
- `chain.append(play)`: in the same transaction as the play insert, take `prev = last chain_hash ORDER BY seq_no` (advisory lock `pg_advisory_xact_lock(42)`), `chain_hash = sha256(prev + canonical(core fields: play_id, meter_id, venue_id, song_id, started_at ISO, duration_s rounded 1, n_windows))`.
- Hourly (scheduler): Merkle root (pairwise sha256 of sorted chain hashes; odd → duplicate last) of plays inserted in that hour → `anchors`; `FileAnchor` appends JSON line to `server/anchors/anchors.jsonl` with `via='file'`.
- `GET /api/v1/ledger/status`: recompute all chain hashes in `seq_no` order and compare; recompute last anchors' roots; return contract shape; cache 1 s. Scheduler checks every 2 s and NOTIFYs `ledger` when the state flips.
- `tamper.py --play-id P-… [--restore]`: direct SQL `UPDATE plays SET song_id='…'` (saves original to `server/anchors/tamper_backup.json`), `--restore` puts it back.
**Tests:** intact chain → intact; modify one row → `broken_at_play_id` correct; restore → intact; Merkle root deterministic.

### B-T08 · Read API v1 + SSE + static web — Mon 21:00–22:30
**Files:** `server/api/read.py`, `server/api/feed.py`, `server/tests/test_read_api.py`.
- Implement every endpoint in contract §6 (shares/payouts/compare-flat return the latest stats run; before B-T10 return an empty-but-valid shape).
- Validate each response against `contract/schemas/*.json` in tests.
- `GET /api/v1/feed` with `sse-starlette`, keep-alive 15 s.
- Mount `StaticFiles(directory="web", html=True)` at `/` **after** API routes. CORS not needed (same origin); for A's dev server allow `http://localhost:8080` via CORSMiddleware.
- `period` semantics: `live` = plays with source live/virtual since Mon 12:00 local; `mock_month` = source mock; `all`.
**DoD:** `web/index.html?mock=1` and `web/index.html` (real) both load; responses validate.

### B-T09 · Stats part 1: fair-play weights — Mon 22:30–00:45
**Files:** `server/stats/fairplay.py`, `server/stats/modifiers.py`, `server/tests/test_fairplay.py`.
- `compute_weights(plays_df, songs_df, venues_df, windows_df, rule_version="sr-1.0") -> DataFrame[play_id, weight, reasons]` — pure function, no DB access.
- Implement brief §7.1 exactly (reference algorithm) and §7.2 modifiers (loop flag, audience factor, concentration cap) with config in `server/config.py`:
  `STREAK_WEIGHTS=[1,1,0.5,0.25]`, `COOLDOWN_H=2`, `COOLDOWN_OTHER_SONGS=3`, `RESET_MIN=30`, `RESET_OTHER_SONGS=3`, `LOOP_MIN_RUN=4`, `LOOP_GAP_TOL_S=20`, `AUDIENCE_PCTL=20`, `AUDIENCE_FACTOR=0.5`, `ACTIVE_HOUR_MIN_SHARE=0.10`, `CONC_MIN_VENUES=5`, `CONC_TOP2_SHARE=0.60`, `CONC_CAP_MULT=3`, `CONC_CAP_MIN=5`.
- Flags are produced as rows (type, song, venue, detail) for the flags table.
**Tests:** T-SR-1, T-SR-2, T-SR-3 from the brief (exact weights); loop scenario; low-audience halving; concentration cap; ordering by event time even when input is shuffled; weights are deterministic.
**DoD:** all tests green; handoff before sleep with the next action = B-T10.

### B-T10 · Stats part 2: estimation, ranges, shares API — Tue 06:45–09:00
**Files:** `server/stats/estimate.py`, `server/stats/run.py`, `server/tests/test_estimate.py`.
- Implement brief §7.3: listening fraction from `windows` (received windows × 10 s ÷ open seconds in period), catch rate from `meters.catch_rate`, expansion, strata from `venues`, national totals, shares, **bootstrap 200** within strata (numpy, seeded), escrow share from `unidentified`.
- `run.py`: `run_stats(period, mode)`: `mode=raw` → weights all 1 (no fair-play), no audience/concentration; `mode=standardised` → full. Writes `stats_runs`, `play_weights`, `shares`, `flags`; NOTIFY `stats`. Endpoint `POST /api/v1/stats/run`; scheduler runs both modes every 5 min for `live` and once after mock import for `mock_month`.
- Performance target: mock month (≈ 300k plays) < 20 s per mode (use pandas groupby; vectorise the streak algorithm per venue with a tight Python loop over sorted arrays if needed — it is O(n)).
**Tests:** tiny 2-stratum dataset with known answer; raw vs standardised differ on the loop scenario; ranges contain the point estimate.

### B-T11 · Mock month importer + compare-flat — Tue 09:30–11:00
**Files:** `server/tools/import_mock.py`, `server/stats/compare.py`, `server/tests/test_import_mock.py`.
- Read A's CSVs (contract §7). Before MC2 they are on A's branch: `git fetch; git checkout origin/a/work -- contract/fixtures/mock_month` **into a scratch worktree** or read via `git show origin/a/work:contract/fixtures/mock_month/venues.csv` — never commit A's files from your branch; after MC2 they are in main.
- Insert venues (`source='mock'` except the live venue), meters (catch_rate from `catch_rate_true` → stored as `catch_rate` *only after* calibration step; for mock use the true value to emulate calibrated meters), songs + owners (`source='mock'`, status ready, no hashes), plays from `plays_observed.csv` (`source='mock'`, go through `ledger.append` in time order), windows from `windows_observed.csv` (expand slots or store slot aggregates in a `window_slots` table — choose one, document in DECISIONS).
- `compare.py`: using `plays_true.csv` (load into `mock_truth` table): true shares; misallocation of flat split and of the SoundProof estimate: `0.5 × pot × Σ|S_est − S_true|`; expose `GET /api/v1/compare-flat`.
- Idempotent: re-import wipes `source='mock'` rows first (except ledger chain → instead, mock plays go into a separate chain namespace `chain='mock'` column; add migration `002_chain_namespace.sql` adding `chain text DEFAULT 'live'` to plays and anchors; live chain is never wiped).
**DoD:** dashboard (mock period) shows ~200 songs with ranges; loop scenario song flagged.

### B-T12 · Payouts + WIPO CSV + frozen — Tue 11:00–12:15
**Files:** `server/payouts/compute.py`, `server/api/payouts.py`, `server/tests/test_payouts.py`.
- Brief §9: pot × share (escrow excluded), owner splits, largest-remainder rounding to integers, total == pot (assert); `status='sent_mock'` after a fake 1 s "transfer"; `frozen=true` if ledger not intact (no statements generated while frozen).
- `GET /api/v1/payouts`, `GET /api/v1/export/usage_report.csv` (columns: `song_id,isrc,iswc,title,artist_name,period_start,period_end,estimated_plays,share,amount_fcfa,rule_version,stats_run_id`; header comment "prototype format for WIPO Connect import").
**Tests:** sum exactly pot for random shares; splits; frozen behaviour.

### MC2 (12:30) — merge captain
Joint test per 02 §7. Tag `cp-2`. Keep the stack running for the 14:00 clinic; do not deploy risky changes 13:30–14:45.

### B-T13 · Flags, raw/standardised everywhere, demo reset, tamper polish — Tue 13:15–15:15
- `mode` param honoured by `shares`, `plays` (weight/reasons from the chosen run), `artists`, `payouts`.
- Flags endpoint complete (types in contract §6) incl. `duplicate_suspected` songs.
- `POST /api/v1/demo/reset`: deletes live/virtual raw_batches, match_events, windows, plays **of chain 'live'**, play_weights/stats of period live, restarts logger state; keeps catalogue, meters, mock month. (Ledger: start a new live chain epoch — store `epoch` in anchors; document.)
- Tamper polish: SSE `ledger` within 2 s; payouts frozen; `tamper.py --restore`.
**DoD (MC3):** joint test passes.

### B-T14 · Calibration intake, catch rates, skew and moved-meter flags — Tue 16:15–17:30
- `POST /v1/calibration` (contract §4): verify signature; for each expected `{song_id, played_at}` look for an accepted `match_event` or play of that song for the meter within ±90 s → matched; `catch_rate = matched/total`; update `meters.catch_rate`; insert `calibrations`.
- Flags: `clock_skew` if |skew| > 5 s; `meter_moved` if `cell_id` in a batch differs from the meter's registered cell_id.

### B-T15 · Sepolia anchor — Tue 17:30–19:00 (*stretch; skip if behind*)
- `SepoliaAnchor` with `web3==7.6.0`: env `SEPOLIA_RPC_URL`, `ANCHOR_PRIVATE_KEY` (test key funded from a faucet; never a real-value key); tx to self with `data=0x<root>`; store tx hash; `via='sepolia'`; fallback to FileAnchor on any error. Document the explorer link format `https://sepolia.etherscan.io/tx/<hash>`. Declare the library.

### B-T16 · Robustness, performance, demo seed — Tue 20:30–22:00
- Kill/restart each container during traffic → no lost or duplicated plays (test script `scripts/chaos_b.ps1`).
- Index load time and matcher latency < 300 ms/window with 50 songs (log it).
- `server/tools/demo_seed.py`: ensures demo venue/meters registered, mock month imported, stats run for both periods, catalogue ready; idempotent; used before each rehearsal.

### B-T17 · README (server), declarations, tools — Tue 22:00–23:00
README sections: Quick start (Windows): `docker compose up -d`, firewall script, hotspot, seed, open dashboard; architecture diagram; API list; "How the fair-play rules work" (the table + test vectors); "Limits"; Declarations table (every library + licence + purpose, "Built with Claude Code", fonts OFL, data sources).
MC4 23:00 (merge captain).

### B-T18 · Wednesday — 07:30–09:30
Fixes only. Fresh clone test on Laptop A (`git clone`, `docker compose up`, seed, open dashboard). Tag `v1.0` at MC5. Submit by 10:00.

---

## 4. Your test and quality checklist (before every handoff)
- `scripts\test_b.ps1` green; `docker compose ps` all healthy.
- Every API response used by the web validates against its schema (test).
- No secrets in git (`.env`, keys); `.env.example` updated.
- Raw tables never updated in place (only inserts), except `tamper.py` (demo) and `demo/reset`.

## 5. Things you must never do
- Edit A's files, locked docs or constants (without the contract protocol).
- Change fingerprint parameters after MC1 without an in-person agreement and a `reindex_all`.
- Silently drop plays or batches; always store and flag.
- Show a number in the UI that the code did not compute.
