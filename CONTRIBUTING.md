# Contributing to Secretary

Thanks for helping. This is a small prototype, so the process is light.

## Ways to help

- Report bugs or confusing docs (use the issue templates).
- Improve schedule parsing for printed timetables in `facetime/secretary_schedule.py`.
- Add tests. There is no automated suite yet, so this is the most valuable contribution.
- Share the web app source or backend if you maintain it, so `web/workspace` can stop being a bundle.

## Setup

- **Web demo:** open `web/promise-check/index.html`. No tooling.
- **FaceTime worker:** Python 3.12 on a Mac. See [docs/facetime-worker.md](docs/facetime-worker.md).

## Before you open a PR

1. Branch from `main`. Keep the change focused.
2. Run the checks CI runs:
   ```sh
   python -m compileall facetime
   node --check web/promise-check/planner.js
   node --check web/promise-check/app.js
   ```
3. Say how you tested it. For scheduling changes, include the input and the result.
4. **No secrets.** Never commit `.env*` files (other than `*.example`), keys, tokens, real phone numbers, or personal file paths.
5. Update docs if behavior or setup changes.

## Safety rules for changes

Secretary can touch someone's calendar and send messages on their behalf, so these are not negotiable:

- Calendar, Reminders, and text actions need explicit owner approval.
- Existing meetings are never moved.
- Mail and calendar access stays read-only.
- Missing data is reported, never guessed.

## Conduct

Be kind and assume good faith. Harassment or personal attacks aren't tolerated; maintainers may remove comments or contributors who cross that line.
