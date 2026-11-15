# Secretary on FaceTime

## Read a printed schedule

1. Start the worker and connect Chert as described below. Schedule scanning starts without an access phrase.
2. Join FaceTime Video and hold up a printed schedule. Say “check schedule.”
   Keep two or more complete event rows in view until it responds.
3. Say “I’m not sure” or “what should I do?” if Secretary finds a conflict.
   It recommends keeping a likely fixed event and moving flexible work to a
   free slot on the page. If priorities are unclear, it asks which event is
   harder to move.

The scanner reads unfamiliar pages at runtime. Use clear event titles and
start/end times, optional weekday headings, and AM/PM or 24-hour times.
Schedules with unreadable rows, separate columns on grids, missing times,
or unconventional layouts may need to be shown one section at a time.

To check an actual phone photo offline:

```sh
python secretary_schedule.py path/to/schedule.jpg
```

## Optional Apple app actions

Secretary listens for a scheduling request, checks Apple Calendar, and
speaks the conflict and an alternative. After the caller says “confirm
plan,” it creates a work block and a reminder. If the caller also asked
for a text to a configured contact, Secretary requests submission through
Chert iMessage after the Apple updates succeed.

## Set up on your Mac

Use the same LiveKit project and Chert project as BriefCheck. Keep the
existing .env.local for LiveKit. Use Python 3.12 and run these from the
BriefCheck source folder in Terminal.

1. Install the worker dependencies with
   `python -m pip install -r requirements-secretary.txt`.
2. Verify that the Piper voice from the BriefCheck README is available.
   Put Whisper's `ggml-base.en.bin` in `models/`, or set
   `SECRETARY_WHISPER_MODEL` to its full path. Speech recognition and
   speech generation run locally.
3. For Calendar actions, pair this Mac with the signed-in Secretary website.
   In **Approvals**, create a pairing key, then run
   `./pair-secretary-approval.sh` and paste the key at the private prompt.
4. For Calendar and Reminders actions, build the Apple apps helper and run the permission check:

   ```sh
   ./setup-secretary-calendar.sh
   printf '%s' '{"action":"authorize"}' | .secretary-build/secretary-calendar
   ```

   Approve Calendar and Reminders access when macOS prompts. The check
   creates no items. Use the same Terminal launch context for the worker
   so it can use the permissions you grant. You need a default writable
   calendar and Reminders list.
5. In Terminal, start `python secretary_agent.py start`. Point the
   installed Chert beta.21 connector at `secretary-agent`, using the
   same project, CLI sign-in store, and LiveKit env file as before.
   Release the BriefCheck connector before binding Secretary to the line.

   ```sh
   chert doctor --agent secretary-agent
   chert dev --agent secretary-agent
   ```

   Keep both processes running. Call only once Chert reports the agent
   is in a waiting room.

For the optional iMessage action, set the contact name, their
international phone number, Chert project API key, project ID, and line
ID in .env.secretary. Use a test contact who has agreed to receive the
message and is eligible under your Chert line's messaging policy. The
recipient needs to be able to receive messages from this line. A spoken
request without the configured contact still works for Calendar and
Reminders. Keys stay in the local environment file.

## Rehearse one conflict

Put a mock busy meeting in Apple Calendar for 5 to 6 p.m. tomorrow, and
leave at least 45 minutes open after it. With the agent ready:

1. Call on FaceTime Video and say **check schedule**.
2. Show the sample schedule and ask what you should do.
3. Say “book that tomorrow” if the printed day matches. Include “and text me”
   only if you configured your own verified test number.
4. Review the exact time and text recipient under **Approvals** on the website.
   Tap **Approve** while the call is open. Check Calendar and Reminders.
   Verify iMessage separately if enabled.

The supported call flow uses a printed schedule, a spoken request to book
a recommended change, and approval from the signed-in account. One calendar block
and one reminder are created per approved plan. Secretary keeps existing
meetings in place and books around them. It can only text the configured
contact. The message is submitted through Chert, and submission does
not verify delivery. The agent uses Chert's bridge-ready signal before
greeting and relies on the bridge to deliver caller audio.

- [Chert setup guide](https://app.trychert.com/skills.md)
- [Whisper model files](https://huggingface.co/ggerganov/whisper.cpp/tree/main)

## FaceTime demo controls

Use **Call Secretary** on the website to open FaceTime on a compatible
Apple device. Use a caller verified for the Chert line.

- At the greeting, ask for a **morning news brief**. Say “brief me on
  science,” or say “yes” and give a topic after Secretary asks.
- Say **voice one** or **voice two** to switch between the Lessac and Amy
  local voices. The second voice needs en_US-amy-low in the voices folder.
- Say **color green**, **color blue**, or **color plum** to change the
  FaceTime card. Voice and color choices apply during the current call.
- For the real Calendar demo, run **start-secretary-demo.sh** in the
  Terminal where Apple app access was granted. Show the schedule and ask
  “what should I do?” Then say “book that today” or “book that tomorrow,”
  matching the printed day. Review the proposed change on the website’s
  **Approvals** tab and tap **Approve**.
- For a follow-up to your own verified test phone, first run
  **configure-secretary-text.sh** in Terminal. Enter a Chert project key
  authorized for messaging and your own test number. Then say “book that
  tomorrow and text me.” Approve once on the website. Check Calendar, Reminders, and
  Messages for separate evidence of each result. Chert submission is
  distinct from message delivery.
