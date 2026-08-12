# AI Event Management Platform

An end-to-end event management automation built entirely in **n8n** — registration, QR-code check-in, automated certificates, reminders, and AI-powered feedback analysis, with Google Sheets as the backing data store.

## What it does

- **Registration** — a webhook captures signups, appends them to Google Sheets, emails a confirmation, and generates + emails a QR code for check-in.
- **Attendance** — scanning the QR code hits a second webhook that looks up the participant, validates the entry, and marks attendance in real time.
- **Reminders** — a daily cron job checks who has an event "tomorrow" and sends reminder emails automatically.
- **Certificates** — after the event ends, a scheduled workflow reads attended participants, generates a certificate (HTML → PDF), uploads it to Drive, and emails it out.
- **Feedback & sentiment** — a feedback webhook appends responses to Sheets; an hourly workflow pulls new feedback, runs it through **Groq** for sentiment analysis, and updates an analytics sheet.

## Architecture

```
Webhook (Register) → Google Sheets (append) → Gmail (confirm) → QR generation → Drive → Gmail (QR)
Webhook (Scan)      → Sheets lookup → validate → mark attendance
Cron (daily)         → Sheets read → filter "event tomorrow" → Gmail (reminder)
Cron (post-event)    → Sheets read → HTML→PDF certificate → Drive → Gmail (certificate)
Webhook (Feedback)   → Sheets append
Cron (hourly)        → Sheets read → Groq sentiment analysis → Sheets (analytics)
```

## Screenshots

See [`docs/screenshots`](docs/screenshots) for the full set — workflow canvases, the generated QR/certificate output, an audit alert example, and the Google Sheet backend.

## Tech stack

- **n8n** — workflow orchestration (webhooks, cron, HTTP, code nodes)
- **Google Sheets / Drive / Gmail APIs** — data store, file storage, notifications
- **Groq API** — LLM-based sentiment analysis on feedback
- **goqr.me** — QR code generation

## Setup

1. Import [`workflows/event-platform.json`](workflows/event-platform.json) into your n8n instance.
2. Configure credentials for: Google Sheets OAuth2, Gmail OAuth2, Google Drive OAuth2, and an HTTP Header Auth credential for Groq.
3. Replace `YOUR_GOOGLE_SHEET_ID` (in the Google Sheets nodes) with your own spreadsheet ID. The sheet needs tabs matching: master registrations, attendance, feedback, and analytics.
4. Activate the webhooks and note their URLs for your registration form / QR scanner / feedback form to POST to.
5. Replace `YOUR_WEBHOOK_ID` placeholders with the IDs n8n generates when you activate each webhook node.

## Future work

- Move reminders/notifications to WhatsApp in addition to email
- Add a lightweight dashboard over the analytics sheet
- Swap the free goqr.me call for a self-hosted QR generator

## License

MIT — see [LICENSE](LICENSE).
