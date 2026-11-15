# BriefCheck

> **Repo note:** `inbound.py` and the `samples/` images are not in this repo yet. Run `python make_samples.py` from `facetime/` to generate `samples/proposal-A/B/C.png` (macOS fonts required). Commands below assume you are in `facetime/`.

BriefCheck calls you on FaceTime before a meeting and compares the proposal you
hold up with the approved terms. It shows the observed and approved values
side by side. When it detects a mismatch, it can call back and verify a
corrected copy.

## Try the document check

Use Python 3.12 on a Mac. From this folder:

```sh
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python briefcheck.py samples/proposal-A.png samples/proposal-B.png samples/proposal-C.png
```

A should match. B should flag the price. C should flag the delivery date.
For your own rehearsal, photograph a sample page and give the command that
image path. The [approval fixture](../facetime/approved.json) stands in for the approval
email. The prototype reads labeled fields for one proposal in the format
shown on the sample pages.

## Call BriefCheck from your iPhone

Complete the Chert onboarding flow and connect the Chert CLI to the same
LiveKit project as the worker. Add `LIVEKIT_URL`, `LIVEKIT_API_KEY`, and
`LIVEKIT_API_SECRET` to `.env.local`. Download the voice model using the
command below, then run the worker from this folder.

```sh
python -m pip install -r requirements-inbound.txt
python inbound.py start
```

In a second terminal, from the same folder:

```sh
chert doctor --agent briefcheck-agent
chert dev --agent briefcheck-agent
```

Keep both terminals running. When Chert reports the line is ready, place a
**FaceTime Video** call to the exact assigned number or email. The agent
greets you when Chert signals the bridge is ready. Show a proposal page to
the camera. The result appears on the call. Caller-camera capture must be
enabled for the line.

## Run the outbound callback demo

1. Ask your Emory Hacks organizer for a Chert project and an assigned line
   with **outbound video and caller-camera capture**. Chert enables these
   separately. Verify your recipient in Chert if the line requires it.
2. In a LiveKit Cloud project, get its WebSocket URL and API key/secret.
   In Chert, create a project key with `facetime:read` and
   `facetime:write`. Copy `.env.example` to `.env` and fill it in
   locally. Use your own FaceTime-enabled phone or email as the destination.
3. Download the speech model. On macOS, the command below points Python at
   the system certificate bundle.

```sh
mkdir -p voices
SSL_CERT_FILE=/etc/ssl/cert.pem python -m piper.download_voices --download-dir voices en_US-lessac-low
```

4. Run the agent. This dials the configured recipient. It schedules a second
   call 12 seconds after the first call ends when there's a mismatch.

```sh
SSL_CERT_FILE=/etc/ssl/cert.pem python facetime.py --callback-after 12
```

Bring up B or C on paper or another screen. Hold the whole page still until
the result card appears. On the callback, show A. The agent should say
“Correction verified” and show the matched values. A clean first scan
finishes without a callback. For an unreadable page, the agent asks you to
adjust lighting and try again.

### Optional approved email

Set the optional SMTP values in `.env` and add `--email-alert` to the
FaceTime command. After a mismatch, the terminal shows the recipient and
draft. Type `SEND` to send it, or press Enter to skip. The callback countdown
starts after that choice. For judging, use an inbox you can show on screen.

## Two-minute judging run

- Give the judge the approved price **$10,500** and delivery date **December
  18, 2026**. Let them pick the price-error or date-error page.
- Answer the call and show the selected page. Point out the two columns on
  the result card.
- Answer the callback and hold up the correct page. Show “Correction verified.”
- Keep a clean first-scan run ready if the judge asks for a control case.

The current prototype is a document-checking agent with spoken prompts and a
timer-driven callback. The caller interacts by showing the page. Email
approval is in the terminal. Mood prediction, free-form voice conversation,
and calendar or email inbox ingestion are outside this build. State is held
for the two calls in one running process.

[Chert outbound calling reference](https://docs.trychert.com/facetime/outbound)
· [Caller-camera requirements](https://docs.trychert.com/facetime/audio-video)
