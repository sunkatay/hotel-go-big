# Tech Stack & Hosting Proposal

| | |
|---|---|
| Status | **Proposal**, awaiting founder decision |
| Date | 2026-10-23 |
| Related | [Decision log](./decision-log.md) · [Phase 0 spec](./phase-0-spec.md) |

The **tech stack** is the set of tools the product is built with: programming language, database,
frameworks. **Hosting** is where it runs: which company's computers ("the cloud") keep it online.

---

## 1. What guides the choice

Our situation decides most of this:

1. **The team is the founder and Claude.** No one is on call at night and no one is a full-time
   server administrator. So we choose **managed services**, where the provider handles servers,
   security patches and backups, over running our own servers.
2. **Mainstream, proven tools.** Widely used tools have the most documentation, the fewest surprises,
   and are what Claude works with most reliably. They also make it easy to hire a developer later.
3. **One language everywhere.** Fewer things to learn, test and keep up to date.
4. **Cheap to start, room to grow.** Low monthly cost while we have few customers, with a clear path
   to bigger infrastructure in Phases 2–4 without rewriting the product.
5. **Security and reliability built in**: backups, encryption and automatic security updates from the provider.

---

## 2. Recommended stack

| Layer | Recommendation | What it is, in plain words | Why |
|---|---|---|---|
| Language | **TypeScript** | The programming language for everything: app screens, server, background jobs | One language end to end; catches many mistakes before the code runs; very widely used |
| Owner & staff app | **Next.js (React)**, installable web app (PWA) | The phone app owners and cleaners use, opened from a browser and installed to the home screen | One app for Android, iPhone and computers; no app-store approval; works offline for key screens (see §4) |
| Booking website | **Next.js** (same codebase) | The property websites and booking pages guests see | Pages load fast and show up well on Google |
| Server (API) | **Node.js with a structured framework (NestJS)**, as **one application with clear modules** | The "brain" that handles bookings, payments and sync | One application is far simpler to run than many small services; modules keep it organised as it grows |
| Background jobs | **Worker process + job queue stored in the database** | Tasks that run on their own: sending availability to booking sites, charging balances, the 3 a.m. day close, emails | No extra system to manage; jobs survive restarts and retry on failure |
| Database | **PostgreSQL** | Where all data lives: properties, bookings, payments | The most trusted open-source database; strong guarantees that the last room can't be sold twice; built-in protection so one hotel can never see another's data |
| File storage | **S3-compatible object storage** | Photos, invoices, ID scans | Cheap, durable, encrypted |
| Email | A transactional email service (e.g. Amazon SES, Postmark, Resend) | Sends confirmations and pre-arrival messages, reports bounces | Good delivery to guests' inboxes |
| Error tracking | **Sentry** (free tier to start) | Tells us when something breaks, with details | Problems found before customers report them |
| Uptime alerts | An uptime monitor (e.g. Better Stack, UptimeRobot) | Checks the site every minute; texts/emails the founder if it's down | You hear about outages first |
| Code & checks | **GitHub** + **GitHub Actions** | Where the code lives; runs all tests and security scans on every change | Already set up; nothing merges unless checks pass |
| Testing | **Vitest** (unit/integration), **Playwright** (end-to-end, phone-sized) | Automatic tests, including a robot clicking through the app | Matches the security & quality plan |

---

## 3. Hosting options

There is **no major cloud data centre in Sri Lanka** (AWS, Google Cloud and Azure have none). The nearest
are **Singapore** and **Mumbai**; either is fast enough for users in Sri Lanka.

| | A. Render (Singapore) — **recommended to start** | B. AWS or Google Cloud (Singapore / Mumbai) | C. Vercel + Supabase |
|---|---|---|---|
| What it is | All-in-one hosting platform: runs our app, worker, scheduled jobs and database | The big cloud providers: every building block, full control | Two services: Vercel for the app, Supabase for database, login and storage |
| Effort to run | **Low**: Render handles servers, security updates, certificates | **High**: many settings to get right; needs real cloud expertise | Low |
| Backups | Managed Postgres with point-in-time recovery: **3 days** on the Hobby plan, **7 days** on Pro | As much as we configure | Included on paid plans |
| Fits our design | Yes: app, background worker, cron jobs and Postgres in one place | Yes, fully | Partly: background workers and long-running jobs are awkward |
| Cost at start | Low (see §5) | Low to medium, easy to overspend | Low |
| When to move | When Phase 2–3 customers need more control, certifications or a specific region | Phase 2+ | — |

**Recommendation: start on Render in its Singapore region** with its managed PostgreSQL, on the
Pro workspace plan (7-day recovery window, meeting the Phase 0 backup requirement). Because the stack
is standard (TypeScript, Node, PostgreSQL, Docker-style deployment), moving to AWS or Google Cloud
later is a planned migration, not a rewrite.

### Where the data is stored, and the law
- Sri Lanka's **Personal Data Protection Act** core obligations, including the rules on sending
  personal data abroad, are scheduled to take effect on **1 January 2027**.
- After a 2025 amendment, transfers abroad appear to be allowed when we follow the Act and adopt
  safeguards set by the Data Protection Authority. Those directives were **still in draft** at the
  last reports found, so this is **not verified**.
- Hosting in Singapore is therefore **likely acceptable with the right safeguards**, but **confirm
  with a Sri Lankan lawyer before the beta**. We keep the design portable so data could move if
  rules or a large customer require it.

---

## 4. Poor connectivity (the cabana property)

The owner and staff app will be built so key screens keep working **without internet**:
- today's arrivals and departures, the calendar for the coming days, and the cleaning list are kept on the phone;
- changes made offline (a new booking, a room marked clean, a price change) are saved on the phone
  and **sent automatically when the connection returns**;
- the app clearly shows "Offline" and how many changes are waiting.

Limit: while the phone is offline, **booking sites can't be updated from it**. Bookings arriving from
Booking.com and the others are still handled by our servers, so the booking sites stay correct; the
owner sees new bookings when their phone reconnects. A booking made offline on the phone could clash
with an OTA booking that arrived meanwhile; the app checks on reconnect and flags any clash immediately.

The prototype's "Simulate no internet" switch shows this behaviour.

---

## 5. Estimated monthly costs (USD, rough)

Prices change often and some couldn't be verified; treat as estimates.

| Item | While building | Live, first ~50 properties |
|---|---|---|
| Render workspace (Pro) | ~$25 | ~$25 |
| App + API services | ~$7–15 | ~$25–50 |
| Background worker + scheduled jobs | ~$7 | ~$10–25 |
| PostgreSQL database | ~$6–20 | ~$20–60 |
| File storage | ~$0–5 | ~$5–15 |
| Email sending | ~$0 | ~$10–20 |
| Error tracking, uptime monitor | ~$0 (free tiers) | ~$0–30 |
| Domain names | ~$1–2 (≈ $15/year) | ~$1–2 |
| **Total hosting** | **≈ $45–90** | **≈ $100–230** |
| Channex (separate, from §decision log) | — | ~$130 + ~$7/property |

At ~LKR 332/USD: building ≈ LKR 15,000–30,000/month; live ≈ LKR 33,000–76,000/month plus Channex.

---

## 6. What the founder needs to do

1. **Create the accounts in your name** (or the company's, once registered): Render, a domain
   registrar, the email service, Sentry, the uptime monitor. Turn on two-step login for every one.
2. **Give Claude a limited-access login** where needed (deploy and read logs; no billing or account deletion).
3. **Keep billing details current**, so nothing is switched off for an unpaid bill.
4. **Choose a product name and domain** (the prototype uses a sample name).

## 7. Decision needed

| Question | Recommendation |
|---|---|
| Tech stack | Approve §2 |
| Hosting | Render, Singapore region, Pro workspace |
| Data location | Singapore, pending a lawyer's confirmation under the PDPA |
