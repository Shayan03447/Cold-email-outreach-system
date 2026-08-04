# Cold Email Outreach System

Automated cold email system built with **n8n** + **Google Sheets**.

**Scale:** 4000+ leads · 20 sender emails · expandable

---

## What this is

A production-minded outreach engine that:

- Picks pending leads from a sheet
- Rotates across sender inboxes (daily limits)
- Personalizes templates (`{Name}`)
- Sends email
- Marks each lead as sent so it never goes twice

---

## Why it exists

Without a system:

- Too many emails in one day → spam / bans
- One inbox overloaded → reputation damage
- Same person emailed twice → broken trust
- Manual sending does not scale

This system spreads sends over time, respects limits, and tracks status.

---

## How it works

```
Schedule / Manual run
  → Load Senders (once)
  → Load Templates (once)
  → Load Outreachers (once)
  → Filter out SENT / REPLIED / BOUNCED / UNSUB
  → Take max 50 leads this run
  → For each lead:
        Pick best sender (under daily limit)
        Pick random template
        Personalize body
        Send email
        Mark lead SENT
        Increment sender Sent_Today
```

**Rule of thumb:** 20 senders × 40 emails/day ≈ 800/day → ~5 days for 4000 leads.

---

## Data (Google Sheet — 3 tabs)

| Tab | Purpose |
|-----|---------|
| `Outreachers` | Leads + `Send Status` |
| `Senders` | Inboxes + `Sent_Today` + `Daily_Limit` |
| `Templates` | Subject + Body |

**Statuses:** `PENDING` · `SENT` · `FAILED` · `REPLIED` · `BOUNCED` · `UNSUB`

---

## Production rules (non-negotiable)

1. **Batch** — max ~50 emails per run
2. **Caps** — never exceed each sender’s `Daily_Limit`
3. **Idempotency** — update sheet to `SENT` right after send
4. **Execute Once** on Get Senders / Templates / Outreachers
5. Don’t re-read senders/templates inside the loop

Full node checklist: see `PRODUCTION-WORKFLOW.md`

---

## Credentials (add when client provides)

| Credential | Count |
|------------|-------|
| Google Sheets OAuth | 1 |
| Gmail OAuth (one per sender) | up to 20 |

Or replace Gmail routing with one cold-email platform API later.

---

## Build status

| Done | Next |
|------|------|
| Read sheets → filter → batch → loop | Edit Fields → Route → Send |
| Pick Sender + Template + Combine | Update Outreach + Update Sender |
| Execute Once on sheet nodes | Schedule trigger for production |
| | Loop close after updates |

---

## Stack

- **n8n** — orchestration
- **Google Sheets** — source of truth
- **Gmail** — sending (or SMTP / Instantly later)

No custom backend. No microservices. Keep it simple.

---

## Docs

- `PRODUCTION-WORKFLOW.md` — node-by-node production blueprint + match checklist
