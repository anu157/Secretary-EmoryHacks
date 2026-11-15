# Set up Secretary

## Website and mobile

1. Open the published Secretary URL in a browser.
2. Choose **Sign in / Create account**, then **Create account**. Use a
   Secretary username and a password of at least 12 characters. Sign in
   with the same Secretary account on your other devices.
3. On iPhone, open the site in Safari. Tap **Share → Add to Home Screen**.
   On Android, use the Chrome menu and choose **Install app** or **Add to
   Home screen**. This is an installable web app.
4. In **Live Capture**, choose a source, give permission, and paste or upload
   text. Suggestions update while you type. Select **Add** to move a
   suggestion into your to-do list.

## Connect mail and calendar

1. Sign in and open **Live Capture**.
2. Choose **Connect** beside a supported provider. Approve read access on
   the provider’s own permission screen.
3. Return to Secretary. Open **Today → Your automatic agenda**. Secretary
   reads recent inbox requests and upcoming timed calendar events, then
   suggests 30-minute work blocks during free time. Approved tasks stay in
   your own to-do list.
4. Leave Secretary open to keep sources refreshed about once a minute.
   Disconnect a source in Live Capture to remove its imported items and
   stop future reads.

The site operator must activate each provider before visitors can connect it.
Apple Notes, iMessage, and other apps require separate access methods.
Secretary proposes time blocks in its own agenda.

### Site operator setup

Register the web app with the providers before making a Connect button
available. Add the applicable HTTPS callback URL to each registration:

- Google:
  `https://YOUR-SECRETARY-URL/api/sources/callback/google`
- Microsoft:
  `https://YOUR-SECRETARY-URL/api/sources/callback/microsoft`

Enable Google Calendar and Gmail APIs in Google Cloud. Google’s Gmail
read-only access is a restricted scope, so confirm the verification
requirements for your audience. In Microsoft Entra, configure the app’s
supported account types and delegated Graph permissions Mail.Read and
Calendars.Read.

Set these in the Site’s server environment: `GOOGLE_CLIENT_ID`,
`GOOGLE_CLIENT_SECRET`, `MICROSOFT_CLIENT_ID`,
`MICROSOFT_CLIENT_SECRET`, and `SECRETARY_TOKEN_KEY`. Configure just the
pairs you intend to enable. `SECRETARY_TOKEN_KEY` is a stable random
32-byte hexadecimal encryption key. Keep secrets out of chat and source
files. Redeploy to apply the environment settings.

- [Google OAuth web-server setup](https://developers.google.com/identity/protocols/oauth2/web-server)
- [Microsoft Graph permissions](https://learn.microsoft.com/graph/permissions-reference)

## Use your existing FaceTime scanner

On the Mac used for the hackathon, the latest Secretary worker was
registered with LiveKit and Chert reported the line ready. The open
FaceTime call requires your verified phone. Use these steps before
spending trial minutes:

1. Have a printed schedule or a second screen ready. Include at least two
   legible event titles, start times, and end times on the same page.
2. Call your assigned Chert number (shown in your Chert project) using
   FaceTime Video from your verified iPhone.
3. After the greeting, aim the rear camera at the page and say **check
   schedule**. Hold the full page steady until Secretary answers.
4. If there is an overlap, say **what should I do?** or **I'm not sure**.
   Hang up once you have the result.
5. If it never greets you or the call ends before reading, stop and check
   the Chert call record before using more minutes.

### Start or restart the Mac worker

Use two Terminal windows. In the first, from the `facetime/` folder:

```bash
cd facetime
source .venv/bin/activate
SSL_CERT_FILE=/etc/ssl/cert.pem python secretary_agent.py start
```

In the second:

```bash
cd facetime
export CHERT_HOME="$PWD/.chert-state"
npx --yes --package=node@22.23.2 --package=@trychert/facetime@0.1.0-beta.21 -c 'chert doctor --agent secretary-agent'
npx --yes --package=node@22.23.2 --package=@trychert/facetime@0.1.0-beta.21 -c 'chert dev --agent secretary-agent'
```

Keep both processes running. The connector should report that
`secretary-agent` is in a waiting room. The webpage's personal account and
the Mac FaceTime worker are separate. For optional Apple Calendar and
Reminders actions, see the [worker guide](facetime-worker.md).
