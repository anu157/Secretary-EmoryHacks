# AGENTS.md

Guidance for AI coding agents working in this repo. Humans: see CONTRIBUTING.md.

## Project

Secretary is a daily assistant. Two surfaces share one idea: check what a new commitment costs before agreeing to it.

- `web/workspace/`: built web app bundle. **Do not hand-edit the bundle.** Source is not in this repo.
- `web/promise-check/`: plain HTML/CSS/JS demo. No build step. `planner.js` holds the scheduling logic, `app.js` the UI.
- `facetime/`: Python 3.12 worker (LiveKit agents, Chert FaceTime, local Whisper + Piper).

## Rules that matter

1. **Never read, print, or commit secrets.** `.env`, `.env.local`, `.env.secretary`, API keys, approval keys, phone numbers. Edit only the `*.example` files.
2. **Never invent schedule data.** `secretary_schedule.py` must not guess missing times. If a row is unreadable, say so.
3. **Never move existing meetings.** Plans book around them.
4. **Every Calendar, Reminders, or iMessage action requires explicit owner approval** through `secretary_approval.py`. Do not add a path that skips it.
5. Mail and calendar scopes stay **read-only**.
6. Keep speech recognition and synthesis local.
7. Do not hardcode personal paths or phone numbers in code or docs.

## Commands

```sh
# Syntax check Python
python -m compileall facetime

# Syntax check JS
node --check web/promise-check/planner.js
node --check web/promise-check/app.js

# Try the scheduler offline on a photo
cd facetime && python secretary_schedule.py schedule-samples/overlap.jpg
```

No automated test suite exists yet. When you change scheduling logic in `planner.js` or `secretary_schedule.py`, describe how you verified it in the PR.

## Style

- Python: standard library first, type hints where cheap, small functions, no new dependencies without a reason in the PR.
- JS: no frameworks or build tooling in `web/promise-check`.
- Docs: plain and specific. Say what a step does and what the user should see.

## Environment notes

Calendar/Reminders actions and the Swift helper only run on macOS. Whisper model files (`models/`) and Piper voices (`voices/`) are downloaded locally and are git-ignored.
