# CLAUDE.md — SoundProof (read me first, every session)

You are one of **two** Claude Code agents building SoundProof during the CIMFEST Hackathon 2026 (Mon 23 Nov 12:00 → Wed 25 Nov 12:00).
- **Agent A** runs on Laptop A (Windows): device agent, virtual meters, mock month, web pages, demo. Plan: `docs/PLAN_A_DEVICE_WEB.md`.
- **Agent B** runs on Laptop B (Windows + Docker): SONACAM server. Plan: `docs/PLAN_B_SONACAM_SERVER.md`.

Ask your human which side you are if it is not obvious from the branch (`a/work` = A, `b/work` = B).

## Session start protocol (mandatory, in order)
1. Read `docs/00_PROJECT_BRIEF.md`, `docs/01_CONTRACT.md`, `docs/02_WORKFLOW_HANDOFFS_MERGES.md`, and your own plan.
2. Read your `STATUS_X.md`, the latest 3 files in `handoffs/X/`, `DECISIONS.md`, `REQUESTS.md`, `contract/CHANGES.md`.
3. Read the other side's `STATUS_Y.md` and its latest handoff.
4. `git status` and `git log --oneline -10`. Undocumented local changes → write a handoff describing them first.
5. Run your quick tests (`scripts\test_a.ps1` or `scripts\test_b.ps1`) and note the result.
6. Continue with the "Next action" of your latest handoff.

## Hard rules
- Only edit files your side owns (`docs/02 §1`). Cross-side needs → `REQUESTS.md` + stub/fixture, never wait.
- The contract (`docs/01_CONTRACT.md`, `core/soundproof_core/constants.py`, `contract/schemas/`) changes only via `docs/02 §6`.
- Write a handoff (`docs/02 §4`) at the end of every task, every 90 minutes, before every merge checkpoint, before the human stops, and when your context gets long. Update your STATUS file with it. Commit and push.
- Commit format `[A-T05] …` / `[B-T09] …`; push your work branch after every commit. Only B merges into `main`, at merge checkpoints.
- Windows: use PowerShell commands; paths with backslashes in commands; Python 3.11 venv at `.venv`.
- Never commit audio, recordings, keys, `.env`, `data/`.
- Never claim "done" without running the task's tests in this session and pasting their summary in the handoff.
- All code is written during the hackathon. Record every library in README → Declarations. We declare: Built with Claude Code.
- Do not show any number in the product or pitch that the code did not compute.
