# n8n – Daily US IPO Monitor (Same-day, Offer Amount Threshold)

This repository contains an **n8n workflow** that monitors **U.S. IPOs happening today (same-day only)** and emails a list of tickers whose **offer amount** exceeds a configured threshold.

**Offer Amount = IPO Price × Shares Offered**  
(If the data provider returns a direct offer amount field, the workflow uses that.)

---

## What this workflow does
- Runs on a schedule (default: **every day at 09:00 Asia/Dubai**)
- Uses **America/New_York (US/Eastern)** date to ensure “today” matches the U.S. market day
- Fetches IPO calendar data from **Finnhub**
- Filters to **same-day** IPOs only
- Calculates offer amount and filters by threshold (default: **$200,000,000**)
- Sends an email with matching ticker(s)

---

## Files
- `Daily US IPO monitor.json` – n8n workflow export (import into n8n)

---

## Prerequisites
- n8n running (Docker recommended)
- Finnhub API token
- Email sending via SMTP (example: Gmail SMTP)

---

## 1) Set environment variables (Docker)

### If using `docker-compose.yml`
Add these under the n8n service:

```yaml
environment:
  - FINNHUB_TOKEN=REPLACE_WITH_YOUR_FINNHUB_TOKEN
  - ALERT_TO_EMAIL=recipient@email.com
  - ALERT_FROM_EMAIL=sender@gmail.com
  - TZ=Asia/Dubai

docker compose down
docker compose up -d

## 2) Import the workflow into n8n

Open n8n UI

Go to Workflows

Click Import from file

Select Daily US IPO monitor.json

3) Configure SMTP credentials (Gmail example)

In n8n:

Go to Credentials

Create new → SMTP

Use the following settings:

Host: smtp.gmail.com

Port: 465

SSL/TLS: ON

User: yourgmail@gmail.com

Password: Gmail App Password (recommended)

Open the workflow → Send Email node → select this SMTP credential

Notes

Do not put SMTP passwords inside the workflow JSON.

If your Google account doesn’t allow App Passwords, use a Workspace-approved SMTP relay or another mail provider.

4) Configure the offer amount threshold

In the workflow node “Filter & Format Email”, set:

const minOfferAmountUsd = 200_000_000; // $200M


Examples:

$2M → 2_000_000

$200M → 200_000_000

5) Test the workflow

In n8n workflow editor, click Execute Workflow

Confirm:

HTTP node returns IPO calendar data

Filter node outputs matches (or “no matches”)

Email is delivered to ALERT_TO_EMAIL

6) Enable the daily schedule

Ensure the Cron node is configured for:

Every day

09:00

Timezone: Asia/Dubai (or workflow/global timezone set to Asia/Dubai)

Toggle the workflow Active (top-right)

Security

✅ This workflow uses environment variables for secrets:

FINNHUB_TOKEN

ALERT_TO_EMAIL

ALERT_FROM_EMAIL

Do not hardcode API keys or SMTP passwords in the workflow JSON before committing to GitHub.

Troubleshooting

Missing FINNHUB_TOKEN: Verify env var is set in Docker and restart the container.

No matches: There may be no same-day IPOs above the threshold, or IPO entries may be missing share/price fields.

Email fails: Verify SMTP credentials and network access from the n8n container.
