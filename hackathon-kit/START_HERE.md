# SoundProof execution kit — START HERE (for the two humans)

## What is in this kit
| File | For |
|---|---|
| `docs/00_PROJECT_BRIEF.md` | Both agents: the whole project, algorithms, rules, demo script |
| `docs/01_CONTRACT.md` | Both agents: exact formats and APIs between the laptops |
| `docs/02_WORKFLOW_HANDOFFS_MERGES.md` | Both agents: ownership, git, handoffs, merge checkpoints MC0–MC5 |
| `docs/PLAN_A_DEVICE_WEB.md` | Agent on Laptop A |
| `docs/PLAN_B_SONACAM_SERVER.md` | Agent on Laptop B |
| `repo-root/CLAUDE.md, STATUS_A.md, STATUS_B.md, DECISIONS.md, REQUESTS.md, handoffs/TEMPLATE.md` | Copied into the new repo at bootstrap |

## Tonight / before 12:00 (no product code)
- Both: Python 3.11, Git, Claude Code, PowerShell policy (`docs/02 §8`). `w32tm /resync`.
- B: Docker Desktop + WSL2 working (`docker run hello-world`). Turn on Windows Mobile Hotspot once to test.
- A: ffmpeg, mic permission on, mic enhancements off, Bluetooth speaker charged.
- Both: collect consent for 8–12 demo songs (WhatsApp screenshot), put audio files on a USB stick (not in git).

## Monday 12:00 — what each person types into Claude Code
**Person B (Laptop B), first message:**
> You are Agent B. The execution kit is in this folder: <path>. Read START_HERE.md, docs/00, 01, 02 and docs/PLAN_B_SONACAM_SERVER.md. Then do B-T00 (bootstrap the GitHub repo) exactly as written, copying docs/ and repo-root/ files into it. Follow the session start protocol and the handoff rules from now on.

**Person A (Laptop A), first message (after B says the repo is up):**
> You are Agent A. Clone <repo url>, check out a/work. Read CLAUDE.md, docs/00, 01, 02 and docs/PLAN_A_DEVICE_WEB.md. Do A-T00 then A-T01 exactly as written. Follow the session start protocol and the handoff rules from now on.

**Any later session (after a break, crash or long context):**
> Read CLAUDE.md and follow the session start protocol. Continue from the latest handoff's Next action.

## Merge checkpoints (B merges; both test together)
MC0 Mon 13:45 · MC1 Mon 17:30 · MC2 Tue 12:30 · MC3 Tue 15:30 · MC4 Tue 23:00 · MC5 Wed 09:30 (submit 10:00).
