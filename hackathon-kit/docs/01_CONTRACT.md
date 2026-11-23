# 01 · SoundProof — The Contract (interfaces between Laptop A and Laptop B)

> This document is the **only** thing A and B must agree on to work in parallel. If something is not here, it is
> internal to one side. **Changing anything here = contract change** (protocol in `02_WORKFLOW_HANDOFFS_MERGES.md §6`).
> Contract version: **c-1.0** (Mon 12:00). Every change bumps the minor version and is logged in `contract/CHANGES.md`.

Ownership of contract artefacts:
| Artefact | Path | Written by | Read by |
|---|---|---|---|
| Constants (locked) | `core/soundproof_core/constants.py` | B commits verbatim from §2 at bootstrap | A, B |
| Batch schema + signing | `core/soundproof_core/schema.py`, `signing.py` | B | A, B |
| Peak extractor | `core/soundproof_core/peaks.py` | A | A, B |
| Landmarks | `core/soundproof_core/landmarks.py` | B | B (A may use in eval) |
| JSON Schemas | `contract/schemas/*.json` | B (batch, ingest) / A (fixture formats) — see §9 | both |
| API response fixtures | `contract/fixtures/api/*.json` | B writes first versions in B-T03; must validate against schemas | A (web dev) |
| Meter public keys | `contract/meters/<meter_id>.pub.json` | A (A-T03 provisioning) | B |
| Venues / mock month | `contract/fixtures/mock_month/*.csv` | A (A-T10) | B (B-T11 importer) |

---

## 1. Versions
- `fp_version`: `"fp-1.0"` (peak extraction + landmark parameters in §2). Device must send it; B rejects other values with 422.
- `schema_version` (field `v` in a batch): `1`.
- Standardisation rule version: `"sr-1.0"` (server-internal, shown on dashboard).
- API prefix: `/v1` for device ingest, `/api/v1` for everything the web uses.

## 2. `core/soundproof_core/constants.py` — commit EXACTLY this
```python
"""Locked constants shared by device and server. Change only via contract change (see docs/02 §6)."""
FP_VERSION = "fp-1.0"
SCHEMA_VERSION = 1

SAMPLE_RATE = 8000          # Hz, after resampling
CAPTURE_RATE = 48000        # Hz, laptop mic capture (resample_poly up=1, down=6)
N_FFT = 512
HOP = 256
BIN_MIN = 7                 # ~109 Hz
BIN_MAX = 224               # ~3500 Hz (inclusive)
PEAK_NEIGHBORHOOD = (21, 31)   # (frames, bins) for maximum_filter
THRESH_NEIGHBORHOOD = (63, 31) # (frames, bins) for uniform_filter
THRESH_DB = 6.0
BLOCK_FRAMES = 32
PEAKS_PER_BLOCK = 30
MAG_OFFSET_DB = 80.0        # mag_u8 = clip(round(2*(mag_db + 80)), 0, 255)
MAG_SCALE = 2.0

WINDOW_S = 10               # seconds per analysis window
WINDOW_SAMPLES = WINDOW_S * SAMPLE_RATE   # 80000
FRAMES_PER_WINDOW = 1 + (WINDOW_SAMPLES - N_FFT) // HOP   # 311

FANOUT = 5
DT_MIN, DT_MAX = 1, 63      # frames
DF_MAX = 63                 # bins
MATCH_MIN_ALIGNED = 8
MATCH_RATIO = 2.0

FRAME_S = HOP / SAMPLE_RATE  # 0.032 s
```

## 3. Batch (Laptop A → Laptop B)

### 3.1 JSON shape (`contract/schemas/batch.schema.json`)
```json
{
  "v": 1,
  "fp_version": "fp-1.0",
  "meter_id": "M-07",
  "venue_id": "V-BUEA-CIMFEST",
  "seq": 1842,
  "window_start": "2026-11-24T21:14:00.000Z",
  "window_ms": 10000,
  "loudness_db": -18.4,
  "cell_id": "624-01-0412-01733",
  "fw": "agent-0.1.0",
  "n_peaks": 287,
  "peaks_b64": "AAAHVg8AABRZ...",
  "sig": "base64-ed25519-signature"
}
```
| Field | Type / rule |
|---|---|
| `v` | integer, must be `1` |
| `fp_version` | string, must be `"fp-1.0"` |
| `meter_id` | `^M-[0-9]{2,4}$` |
| `venue_id` | `^V-[A-Z]{3,10}-[A-Z0-9]{3,12}$` |
| `seq` | integer ≥ 1, strictly increasing per meter (persisted across restarts) |
| `window_start` | RFC 3339 UTC with milliseconds and `Z` |
| `window_ms` | integer, `10000` |
| `loudness_db` | float, RMS dBFS of the window (−100..0), 1 decimal |
| `cell_id` | string ≤ 32 chars (laptop: configured fake value) |
| `fw` | string ≤ 32 chars |
| `n_peaks` | integer = number of 4-byte records in `peaks_b64` |
| `peaks_b64` | base64 (standard alphabet, padded) of `n_peaks × <HBB` little-endian records: frame u16 (0..310), bin u8 (7..224), mag u8 |
| `sig` | base64 Ed25519 signature over the canonical bytes (§3.2) |

### 3.2 Canonical bytes for signing
`canonical = json.dumps({all fields except "sig"}, sort_keys=True, separators=(",", ":"), ensure_ascii=False).encode("utf-8")`
Floats are serialised by Python's `json` after rounding `loudness_db` to 1 decimal **before** signing.
Signature: `nacl.signing.SigningKey(seed).sign(canonical).signature` → base64.

### 3.3 Keys (`contract/meters/M-07.pub.json`)
```json
{ "meter_id": "M-07", "venue_id": "V-BUEA-CIMFEST", "public_key_b64": "…32 bytes base64…", "created_at": "2026-11-23T14:30:00Z" }
```
Private seed stays on Laptop A in `device/secrets/M-07.seed` (gitignored). Virtual meters have their own key files.

### 3.4 Test vectors (`contract/fixtures/batches/`)
- `tv_batch_valid.json` — valid signed batch (seed hex in `tv_keys.json`, test-only key).
- `tv_batch_badsig.json` — same with one byte of `peaks_b64` changed → must be rejected 401.
- `tv_batch_badversion.json` — `fp_version: "fp-0.9"` → 422.
B creates these in B-T01 with synthetic peaks; after MC0 A adds `tv_batch_real_*.json` captured from the real mic.

## 4. Ingest endpoints (Laptop B, base `http://<B_IP>:8000`)

### `POST /v1/batches`
Body: one batch (JSON). Responses:
| Code | Body | Meaning / device action |
|---|---|---|
| 200 | `{"ack": <seq>, "status": "stored"}` | Stored. Device deletes it from the outbox. |
| 200 | `{"ack": <seq>, "status": "duplicate"}` | Already had it (same meter_id+seq+identical sig). Device deletes it. |
| 401 | `{"error": "bad_signature"}` | Device logs error, keeps batch, raises alarm on status screen. |
| 404 | `{"error": "unknown_meter"}` | Device keeps batch; operator registers the key. |
| 409 | `{"error": "seq_conflict"}` | Same seq, different content. Device logs and moves batch to `dead` table. |
| 422 | `{"error": "invalid", "detail": "..."}` | Device moves batch to `dead`. |
| 5xx / timeout | — | Device retries with backoff (1, 2, 4, 8, 16, 30 s max, jitter ±20%). |

### `POST /v1/batches/bulk`
Body: `{"batches": [ ... up to 50 ... ]}` → `{"results": [{"seq":..,"status":"stored"|"duplicate"|"error","error":..}]}`. Used when draining a big outbox.

### `POST /v1/heartbeat`
Body: `{"meter_id","venue_id","sent_at","queued":int,"fw","sig"}` (signed like a batch, canonical over fields except sig). → `{"ok":true,"server_time":"...Z"}`. Device sends every 60 s; the server_time lets A show clock skew.

### `POST /v1/meters/register` (admin convenience)
Body: content of a `.pub.json`. → `{"ok": true}`. B also auto-loads all `contract/meters/*.pub.json` at startup.

### `POST /v1/calibration`
Body: `{"meter_id","run_id","started_at","ended_at","expected":[{"song_id","played_at"}], "sig"}` → `{"ok":true}`.
B computes catch rate = matched expected plays / expected plays for that meter (window ±60 s) and stores it.

## 5. Catalogue (upload) endpoints

### `POST /api/v1/songs` (multipart/form-data)
Fields: `file` (mp3/wav/m4a/flac, ≤ 30 MB), `title`, `artist_name`, `isrc` (optional), `iswc` (optional),
`duration_s` (optional; server measures anyway), `owners` (JSON string: `[{"name","role":"singer|composer|producer|publisher","share_pct":number,"momo":"+2376XXXXXXXX"}]`, shares must sum to 100), `consent` ("true" required).
→ `201 {"song_id": "S-000123", "status": "uploaded"}`; 400 on validation errors with `{"error","detail"}`.

### `GET /api/v1/songs?status=&q=`
→ `[{"song_id","title","artist_name","isrc","iswc","duration_s","status","n_hashes","uploaded_at","duplicate_of":null|"S-..."}]`
Status values: `uploaded → processing → ready | duplicate_suspected | failed`.

### `GET /api/v1/songs/{song_id}` → one object as above plus `"owners": [...]`.
### `POST /api/v1/songs/{song_id}/approve` (admin) → approves a `duplicate_suspected` song for indexing.

## 6. Read API for the web front-end (B implements, A consumes)

All return JSON; times are UTC ISO strings; money is integer FCFA. Query `period` = `live` (today since 12:00 local) or `mock_month` or `all` (default `all`). `mode` = `raw` | `standardised` (default `standardised`).

| Endpoint | Response shape (fixture file) |
|---|---|
| `GET /api/v1/overview?period=` | `{"meters_online":int,"meters_total":int,"venues":int,"plays_raw":int,"plays_counted":number,"songs_ready":int,"unidentified_music_min":number,"rule_version":"sr-1.0","ledger_intact":bool,"last_anchor":{"hour":"...","root":"hex","via":"file|sepolia","tx_ref":str|null}}` (`overview.json`) |
| `GET /api/v1/venues` | `[{"venue_id","name","city","lat","lon","stratum","bars_in_stratum":int,"meters":[{"meter_id","online":bool,"last_seen":"...","queued":int,"catch_rate":number}]}]` (`venues.json`) |
| `GET /api/v1/plays?period=&venue_id=&song_id=&limit=50` | `[{"play_id","venue_id","meter_id","song_id","title","artist_name","started_at","duration_s","source":"live|virtual|mock","weight":number,"reasons":[str]}]` newest first (`plays.json`) |
| `GET /api/v1/shares?period=&mode=` | `{"stats_run_id","rule_version","pot_fcfa":int,"escrow_share":number,"rows":[{"song_id","title","artist_name","raw_plays":int,"weighted_plays":number,"share":number,"ci_low":number,"ci_high":number,"amount_fcfa":int,"flags":[str]}]}` sorted by share desc (`shares_standardised.json`, `shares_raw.json`) |
| `GET /api/v1/flags?period=` | `[{"flag_id","type":"loop|cooldown|concentration|low_audience|duplicate_suspected|clock_skew|meter_moved","song_id","venue_id","detail":str,"created_at"}]` (`flags.json`) |
| `GET /api/v1/payouts?period=&pot=` | `{"pot_fcfa":int,"frozen":bool,"rows":[{"owner_name","role","song_id","title","amount_fcfa":int,"momo":str,"status":"pending|sent_mock"}],"total_fcfa":int}` (`payouts.json`) |
| `GET /api/v1/compare-flat?pot=` | `{"pot_fcfa":int,"n_artists":int,"flat_each_fcfa":int,"misallocated_flat_fcfa":int,"misallocated_soundproof_fcfa":int,"basis":"mock_month_truth"}` (`compare_flat.json`) |
| `GET /api/v1/ledger/status` | `{"intact":bool,"broken_at_play_id":str|null,"plays_checked":int,"last_root":"hex","last_anchor":{...}}` (`ledger_status.json`) |
| `GET /api/v1/artists` | `[{"artist_name","songs":[song_id]}]` (`artists.json`) |
| `GET /api/v1/artists/{artist_name}` | `{"artist_name","songs":[{"song_id","title"}],"plays_by_venue":[{"venue_id","name","city","plays_raw":int,"plays_counted":number}],"plays_by_day":[{"date","plays_raw":int,"plays_counted":number}],"amount_fcfa":int,"recent":[...plays...]}` (`artist.json`) |
| `POST /api/v1/stats/run?period=` | `{"stats_run_id","status":"done","duration_ms":int}` — recompute now |
| `POST /api/v1/demo/reset` (admin) | resets live data (keeps catalogue, keys, mock month) → `{"ok":true}` |

### 6.1 Live feed — `GET /api/v1/feed` (Server-Sent Events)
Each message: `event: <type>` and `data: <json>`. Types:
- `batch` `{"meter_id","venue_id","seq","window_start","late":bool}`
- `match` `{"meter_id","venue_id","song_id","title","offset_s":number,"confidence":number,"accepted":bool,"window_start"}`
- `play` `{"play_id","venue_id","song_id","title","artist_name","started_at","source"}`
- `flag` (same as a flags row)
- `ledger` `{"intact":bool,"broken_at_play_id":str|null}`
- `meter` `{"meter_id","online":bool,"queued":int,"last_seen"}`
- `stats` `{"stats_run_id"}` (front-end refetches shares)
Keep-alive comment every 15 s. Fixture: `contract/fixtures/api/feed_sample.txt` (raw SSE text).

### 6.2 Static web hosting
B serves `web/` (A's folder) at `/` via FastAPI `StaticFiles(directory="web", html=True)`. Pages: `/index.html` (SONACAM dashboard), `/portal.html` (upload), `/artist.html?name=...`, `/admin.html`. The web uses relative URLs (`/api/v1/...`) only.

## 7. Mock month (A generates, B imports)

Folder `contract/fixtures/mock_month/` (CSV, UTF-8, comma, header row, LF):
- `venues.csv`: `venue_id,name,city,lat,lon,size_class(S|M|L),stratum,bars_in_stratum,open_hour,close_hour`
  Must include the live venue `V-BUEA-CIMFEST,CIMFEST demo table,Buea,4.1527,9.2410,S,BUEA-S,<n>,18,2`.
- `meters.csv`: `meter_id,venue_id,catch_rate_true,installed_at`
- `songs_synthetic.csv`: `song_id,title,artist_name,isrc,duration_s,popularity_rank` — IDs `S-M0001…`, titles "Mock Song 001", artists "Mock Artist 01" (clearly fake), ISRC `CM-SPF-26-0xxxx`.
- `owners_synthetic.csv`: `song_id,owner_name,role,share_pct,momo`
- `plays_true.csv`: the ground-truth plays (what really played): `true_play_id,venue_id,song_id,started_at,duration_s,scenario`
- `plays_observed.csv`: what the meters "confirmed" (after simulated misses/outages): `play_id,venue_id,meter_id,song_id,started_at,duration_s,loudness_db,source(=mock),scenario`
- `windows_observed.csv`: per meter per window: `meter_id,window_start,loudness_db,received(bool)` (for listening fraction; may be aggregated per 10 min: then columns `meter_id,slot_start,slot_minutes,windows_received,windows_expected,mean_loudness_db`)
- `scenarios.md`: description of injected scenarios (loop artist, paid promotion spike, power-cut meter, moved meter).
Period: 30 days ending the day before the hackathon (2026-10-24 → 2026-11-22). Times UTC (Cameroon is UTC+1; venue hours in `venues.csv` are local).

## 8. Error and naming conventions
- IDs: songs `S-000001` (uploaded) / `S-M0001` (mock); plays `P-<uuid4 short 12>`; stats runs `R-<yyyymmddHHMMSS>`.
- All timestamps stored and sent in UTC; display in local time (Africa/Douala, UTC+1) in the web.
- JSON keys: snake_case. Money: integer FCFA. Shares: float 0..1.

## 9. JSON Schemas to create in `contract/schemas/`
`batch.schema.json`, `heartbeat.schema.json`, `calibration.schema.json`, `song_upload_response.schema.json`, `overview.schema.json`, `venues.schema.json`, `plays.schema.json`, `shares.schema.json`, `flags.schema.json`, `payouts.schema.json`, `compare_flat.schema.json`, `ledger_status.schema.json`, `artist.schema.json`.
B writes all schemas in B-T01/B-T03 (they mirror §3–§6). Both sides validate fixtures and real responses in tests (`jsonschema` package).

## 10. Change log
`contract/CHANGES.md` starts with:
```
## c-1.0 — 2026-11-23 12:00 — initial contract (A+B)
```
