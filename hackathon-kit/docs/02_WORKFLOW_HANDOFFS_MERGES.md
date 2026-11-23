# 02 · Workflow, Handoffs and Merge Checkpoints (rules for BOTH agents)

> Two people, two Windows laptops, two Claude Code sessions (one per laptop), one GitHub repository.
> Each session may be restarted many times (context limits, sleep, crashes). **The handoff files are the memory.**
> If it is not in a handoff, it did not happen.

---

## 1. Repository layout and ownership

```
soundproof/
├─ CLAUDE.md                     # both (locked after bootstrap; changes = decision entry)
├─ README.md                     # B owns structure; A owns "Device" + "Web" sections
├─ .gitignore  .gitattributes    # B (bootstrap)
├─ docker-compose.yml            # B
├─ docs/                         # 00, 01, 02, PLAN_A, PLAN_B — locked (edit only via DECISIONS)
├─ contract/                     # shared: changes only via §6 protocol
│  ├─ CHANGES.md
│  ├─ schemas/*.json             # B
│  ├─ fixtures/batches/          # B creates; A adds real captures after MC0
│  ├─ fixtures/api/              # B
│  ├─ fixtures/mock_month/       # A
│  └─ meters/*.pub.json          # A
├─ core/                         # shared library (pip install -e core)
│  ├─ pyproject.toml             # B (bootstrap)
│  ├─ soundproof_core/__init__.py, constants.py   # B (verbatim from contract §2)
│  ├─ soundproof_core/peaks.py   # A
│  ├─ soundproof_core/schema.py, signing.py, landmarks.py   # B
│  └─ tests/test_peaks.py (A), test_schema.py, test_signing.py, test_landmarks.py (B)
├─ device/                       # A only
├─ web/                          # A only (static HTML/JS/CSS served by B)
├─ tools/mockgen/                # A only
├─ server/                       # B only
├─ scripts/                      # checkpoint scripts: cp*_a.ps1 (A), cp*_b.ps1 (B)
├─ handoffs/A/   handoffs/B/     # each side writes ONLY its own folder
├─ STATUS_A.md   STATUS_B.md     # each side writes ONLY its own file
├─ DECISIONS.md                  # append-only, both
├─ REQUESTS.md                   # append-only, both (cross-side asks)
└─ data/                         # gitignored: audio, recordings, consent screenshots, secrets
```

**Golden rule:** never edit a file owned by the other side. If you need a change there, write a `REQUESTS.md` entry and work around it with a stub/fixture in your own area.

## 2. Git

- Repository: GitHub, private, created by **B at 12:00** (bootstrap task B-T00). A clones it.
- Branches: `main` (only updated at merge checkpoints), `a/work` (A), `b/work` (B).
- Commit message format: `[A-T05] short imperative summary` or `[B-T09] ...`; handoff commits: `[A-HO] T05 done` / `[B-HO] ...`.
- Commit at least every 45 minutes and after every passing test run. Push your branch every commit (`git push origin a/work`).
- Never force-push `main`. Never rebase a pushed branch; use merge.
- Line endings: `.gitattributes` has `* text=auto eol=lf`, `*.ps1 text eol=crlf`, `*.png *.webp *.wav *.mp3 binary`.
- Big files: never commit audio, recordings, model files, `.env`, `device/secrets/*`, `data/*`.
- After every merge checkpoint both sides: `git fetch; git merge origin/main` into their work branch.

## 3. Session start protocol (every time a Claude Code session starts or resumes)

The agent must, in this order:
1. Read `CLAUDE.md`, `docs/00_PROJECT_BRIEF.md`, `docs/01_CONTRACT.md`, this file, and **its own plan** (`docs/PLAN_A_DEVICE_WEB.md` or `docs/PLAN_B_SONACAM_SERVER.md`).
2. Read **its own** `STATUS_X.md`, the **latest 3** files in `handoffs/X/`, `DECISIONS.md`, `REQUESTS.md`, `contract/CHANGES.md`.
3. Read the **other side's** `STATUS_Y.md` and its **latest** handoff (to know what changed and what is coming).
4. Run `git status` and `git log --oneline -10`; if there are uncommitted changes not described in the last handoff, stop and describe them in a new handoff before continuing.
5. Run the side's quick test command (A: `scripts\test_a.ps1`, B: `scripts\test_b.ps1`) and record the result.
6. Continue with the **"Next action"** of the latest handoff. Do not start a different task unless the plan's time box says so.

## 4. Handoff rules

### 4.1 When to write a handoff (any of these)
- A task is finished (Definition of Done met) or abandoned.
- Every **90 minutes** of work, even mid-task.
- Before a merge checkpoint (mandatory, 15 min before).
- Before the human stops (sleep, meal, mentor round) or switches laptops.
- When blocked (with the block described and the workaround taken).
- When the session's context is getting long (the agent judges it is past ~60% of its context) — write the handoff, then tell the human to start a fresh session.

### 4.2 Where and how
- File name: `handoffs/A/2026-11-23_1530_A-T03.md` (date, 24 h local time, side, task).
- Also update `STATUS_A.md` / `STATUS_B.md` (overwrite: it is a snapshot, not a log).
- Commit both together: `[A-HO] T03 partial — keys + batch build done`. Push.

### 4.3 Handoff template (copy exactly; every field required, write "none" if empty)
```markdown
# Handoff A-T03 — 2026-11-23 15:30 — Person A / Laptop A
Task: A-T03 Batch builder + key provisioning   Status: DONE | PARTIAL | BLOCKED
Contract version in use: c-1.0   fp_version: fp-1.0   Branch: a/work   Last commit: 3f2a9c1

## What was done (facts only)
- …

## Files created/changed
- device/agent/batcher.py (new) — builds and signs Batch from window peaks
- …

## How to run it (exact commands, PowerShell, from repo root)
```
.\.venv\Scripts\Activate.ps1
python -m device.agent.batcher --selftest
```

## Tests run and results (paste the summary lines)
- `pytest device/tests -q` → 14 passed in 2.31s
- Manual: …

## Decisions taken (also appended to DECISIONS.md if they affect the other side)
- …

## Contract impact
- none   (or: proposed change c-1.1 in contract/CHANGES.md — waiting for ack)

## Known issues / risks
- …

## Uncommitted or local-only state
- none   (or: data/recordings/hall_*.wav on Laptop A only, 12 files, not in git)

## Next action (one concrete step the next session does first)
- Implement device/agent/outbox.py `Outbox.put()` and its test `test_outbox_survives_restart`.

## Notes for the other side
- B: real signed batches are now in contract/fixtures/batches/tv_batch_real_01..05.json.
```

### 4.4 Quality rules for handoffs
1. **Facts, not hopes.** "Done" only if the task's Definition of Done in the plan is met and tests were run in this session.
2. **Exact commands** that work from a fresh PowerShell in the repo root.
3. **Paste real test output** summary lines, never "tests pass" alone.
4. **Name every file** created or changed.
5. **One "Next action"** that can be started immediately without re-reading the whole plan.
6. **Never delete or rewrite old handoffs.** Corrections go in a new handoff.
7. Keep it under ~80 lines; link to files rather than pasting large code.

### 4.5 STATUS file format (`STATUS_A.md`, overwritten each handoff)
```markdown
# STATUS A — updated 2026-11-23 15:30
Current task: A-T04 Outbox + uploader (in progress, ~40%)
Done: A-T00, A-T01, A-T02, A-T03
Next up: A-T05, A-T06
Next merge checkpoint: MC1 at Mon 17:30 — my items ready: capture ✅ batches ✅ outbox ⏳ uploader ⏳
Blockers: none
Open requests to B: R-003 (see REQUESTS.md)
Health: scripts\test_a.ps1 → 22 passed
```

### 4.6 DECISIONS.md (append-only)
```markdown
## D-007 — 2026-11-23 16:10 — B — Use FileAnchor by default, Sepolia as stretch
Why: no funded test key yet; demo must work offline. Affects A: dashboard shows `via` field.
```

### 4.7 REQUESTS.md (cross-side asks, append-only; the other side answers by appending)
```markdown
## R-003 — 2026-11-23 16:20 — A → B — Need `meters[].queued` in GET /api/v1/venues
Workaround meanwhile: A's web reads `queued` from SSE `meter` events.
Answer (B, 16:45): already in contract §6; will be live at MC2.
```
Rule: **never wait** for an answer. Use a stub/fixture and keep going.

## 5. Independence rules (so neither side blocks the other)
- A tests the device against **`device/tools/fake_sonacam.py`** (a 60-line FastAPI that verifies signatures and acks), not against B.
- B tests ingest/matcher with **`contract/fixtures/batches/*.json`** and with batches generated by `server/tools/make_batches_from_wav.py` (uses `core.peaks` after MC0; before MC0 uses synthetic peaks).
- A builds the web against **`contract/fixtures/api/*.json`** through `web/js/api.js` with `?mock=1` (loads fixture files instead of calling the API). B serves the real API at the same paths.
- A's mock month is a **file deliverable** (CSV) that B imports with its own importer.
- Shared code (`core/`) is finished and merged at **MC0 (Mon 13:45)** — after that, both sides only import it.

## 6. Contract change protocol
1. Proposer appends to `contract/CHANGES.md`: `## c-1.1 (PROPOSED) — time — side — what — why — impact on A / on B`.
2. Proposer writes `REQUESTS.md` entry pointing to it and tells their human, who tells the other human (WhatsApp/aloud).
3. The other side appends `ACK c-1.1 — time — side` (or objects with reason).
4. Only after ACK: implement on both sides; the change is merged at the next checkpoint; versions bump.
5. **Fingerprint parameter changes after MC1 are forbidden** unless both humans agree in person (they invalidate the catalogue index; B must re-run fingerprinting: `python -m server.tools.reindex_all`).

## 7. Merge checkpoints (MC)

**Merge captain: Person B** (owns `main`). Procedure for every MC:
1. T−15 min: both write a handoff, run their checkpoint script, commit, push.
2. B: `git fetch; git checkout main; git merge --no-ff origin/b/work; git merge --no-ff origin/a/work`. Conflicts may only occur in shared files (`README.md`, `DECISIONS.md`, `REQUESTS.md`): keep both sides' text. If a conflict appears in an owned file, the owner resolves it.
3. B runs `scripts\cpN_b.ps1` on main; A pulls `main` and runs `scripts\cpN_a.ps1`.
4. Both run the **joint test** for that MC (below) with the two laptops connected.
5. If green: `git tag cp-N` and push tags. Both merge `main` into their work branch.
6. If red after 20 minutes: tag anyway as `cp-N-partial`, write the failure in both handoffs and DECISIONS, continue; the failing item becomes the top task of its owner.

| MC | When | What must be in | Joint test (pass criteria) |
|---|---|---|---|
| **MC0** | Mon **13:45** | Repo bootstrap, `core/` complete: constants, peaks (A), schema+signing+landmarks (B), core tests | On BOTH laptops: `pip install -e core` and `pytest core -q` all green. A signs a batch with `core.signing`, B verifies it (copy JSON file). `extract_peaks` on the same WAV gives identical output on both laptops (compare SHA-256 of the packed bytes). |
| **MC1** | Mon **17:30** | A: capture, batcher, keys, outbox, uploader, fake_sonacam. B: Docker stack, DB, ingest API, catalogue upload, fingerprint worker, matcher | Hotspot up. B uploads 3 demo songs (status ready). A runs the agent in demo mode; a song played on the speaker produces `match` events on B with `accepted=true` for ≥2 of 3 songs. Pull the network for 2 minutes → A queues → reconnect → all batches arrive (no seq gaps). |
| **MC2** | Tue **12:30** | A: status TUI, virtual meters, network tools, recordings dataset, mock month CSVs, web dashboard v1 + portal page. B: play logger, ledger + auditor, read API v1 + SSE, stats engine (fair-play + estimation), mock importer | Live song → `play` appears on dashboard map + feed within 30 s of confirmation. Mock month loaded: shares table shows 200 songs with ranges. Portal upload works from the browser. |
| **MC3** | Tue **15:30** | B: payouts, raw/standardised toggle server side, tamper tool, flags. A: raw/standardised toggle UI, payouts page, ledger status banner, artist page v1 | Toggle changes ranking; loop scenario artist drops; payouts total = pot exactly; tamper → red banner in ≤2 s; restore → green. (Industry clinic at 14:00 uses the MC2 build.) |
| **MC4** | Tue **23:00** | Everything: anchors (file; Sepolia if ready), compare-flat, calibration, artist page, admin page, demo reset, README run instructions, eval report | Full demo script (brief §12) runs twice end-to-end without code changes. Video recorded. |
| **MC5** | Wed **09:30** | Fixes only, README declarations, submission texts | Fresh clone on Laptop A → `docker compose up` → README steps work. `git tag v1.0`. Submit by 10:00. |

## 8. Common Windows setup (both laptops, do on Sunday night or Mon 12:00–12:20)
- Python **3.11** (python.org installer, tick "Add to PATH") — check `py -3.11 --version`.
- Git for Windows; `git config --global core.autocrlf false`.
- PowerShell: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.
- Claude Code installed and logged in.
- VS Code (optional).
- Repo venv: `py -3.11 -m venv .venv; .\.venv\Scripts\Activate.ps1; pip install -U pip; pip install -e core -r requirements-dev.txt`.
- Time: admin PowerShell `w32tm /resync` (if it fails: Settings → Time → Sync now).
- B only: Docker Desktop with WSL2 (enable virtualisation in BIOS if needed; test `docker run hello-world` the night before).
- A only: ffmpeg (`winget install Gyan.FFmpeg`), microphone privacy permission for desktop apps, disable mic "audio enhancements"/noise suppression/"voice clarity"/Studio Effects.

## 9. Emergency rules
- **Laptop B dies:** Laptop A can run the server: `docker compose up` needs Docker Desktop on A — install it on A on Monday evening as insurance (task A-T08).
- **Mic fails:** use virtual meters with the hall recordings (A-T07/T09).
- **Network fails:** run both A and B services on Laptop B and play the song near B's mic with the agent running on B (`--meter M-99`).
- **Agent session goes off-plan:** human stops it, reads the latest handoff, starts a fresh session with: *"Read CLAUDE.md and follow the session start protocol."*
- **Time pressure:** cut in this order: Sepolia anchor → admin page → calibration UI → artist page polish → virtual meters count. Never cut: live match, play on map, raw vs standardised, tamper demo, payouts total.
