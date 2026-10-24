# Phase 0 MVP Spec — "Run the Small Property"

| | |
|---|---|
| Status | Draft v0.1 |
| Last updated | 2026-10-22 |
| Parent doc | [PRD](./PRD.md) |
| Next phase | [Phase 1 — Run the Rooms](./phase-1-spec.md) |

---

## 1. Summary

Phase 0 is the first product we sell. It targets the **smallest properties: B&Bs, guesthouses,
inns and boutique hotels with 5–20 rooms**, typically run by the owner plus one or two staff,
with no 24-hour front desk and most bookings coming from OTAs.

**Product promise:** *Never double-book. Get more direct bookings. Get paid on time. Let guest
messages and check-in run themselves. Set it up in an afternoon and run it from your phone.*

Phase 0 is deliberately **simple on the surface and complete underneath**: it uses the same
multi-tenant data model as later phases (bookings are reservations with room stays; a booking's
balance is a hidden folio with immutable entries). Moving a customer up to Phase 1 features is a
plan upgrade, not a migration.

### What these customers buy on (in order)
1. Channel manager that keeps Booking.com, Agoda, Airbnb and Expedia in sync — no double bookings.
2. A direct booking website that cuts OTA commission.
3. Online payments: deposits, automatic balance collection, payment links.
4. Automation: confirmation, pre-arrival, check-in instructions, review requests.
5. Price, self-serve setup and a phone-first UI.

### Phase 0 exit criteria
1. **50 paying properties** live, of which ≥ 80% completed setup **without talking to us**.
2. Median **signup-to-first-synced-booking** < 1 day; median active setup time < 4 hours.
3. **Zero double bookings** caused by our sync over 60 consecutive days (OTA-side delays outside our control are tracked separately).
4. Monthly logo churn < 3% over the last 3 months of the phase.
5. Direct-booking share of room revenue up ≥ 3 points for properties live ≥ 90 days.

---

## 2. Scope

### In scope
| Area | Included |
|---|---|
| Signup & onboarding | Self-serve signup, free trial, setup wizard, in-app help |
| Property & rooms | One property; room types; rooms or single-unit listings |
| Calendar | Rooms × dates calendar with drag-and-drop |
| Rates | Seasonal prices, weekday/weekend, min stay, one non-refundable variant, extra-guest fee, per-channel markup |
| Channel manager | Booking.com, Agoda, Airbnb, Expedia via a connectivity aggregator; iCal sync for other channels |
| Direct booking | Booking engine + template website with custom domain |
| Reservations | Create, modify, cancel; multi-room bookings; blocks (owner use, closed dates) |
| Payments | Online card payments, deposits, automatic balance charge, payment links, refunds, OTA virtual cards, manual cash/transfer records |
| Guest messaging | Automated, templated emails; manual email from a booking |
| Online check-in | Pre-arrival form, guest registration data, house rules acceptance, balance payment |
| Housekeeping | Daily cleaning list, shareable with cleaners, clean/dirty status |
| Day close | Fully automatic: no-shows, check-outs, room status, daily summary |
| Guests | Guest list built from bookings, notes, repeat-guest detection, data-protection tools |
| Receipts & invoices | PDF receipt / invoice per booking |
| Reports | Dashboard + a handful of reports; CSV export |
| Our own billing | Subscription plans, trial, payment for our product |

### Out of scope (Phase 1 and later)
Front-desk mode (arrival lists, room assignment rules, card terminals, cash drawer) · split folios
and routing · visible night audit · roles beyond Owner/Staff/Cleaner · maintenance work orders and
out-of-order management · upsells · smart locks · review aggregation · accounting integrations ·
groups · events · F&B · company billing · GDS / metasearch · guest app · HR · multi-property ·
dynamic pricing · public API.

---

## 3. Users & roles

| Role | Can do |
|---|---|
| **Owner** | Everything, including billing, payment-gateway settings, channel connections, user management, data export/erasure |
| **Staff** | Calendar, bookings, payments (no refunds above a limit set by the owner), guests, messages, housekeeping, reports |
| **Cleaner** | Today's cleaning list and marking rooms clean — via the app or a **login-free magic link** that rotates daily |

- **P0-AUTH-01** Email + password or "sign in with Google / Apple"; MFA available, required for Owner when payments are enabled.
- **P0-AUTH-02** Every change is written to the audit log, and so is every **view or export of sensitive guest data** (ID documents, guest exports). Not exposed in the UI in Phase 0 except on the booking timeline; available to us and to the property owner on request (see P0-PRIV-06).

---

## 4. Core concepts

### 4.1 Rooms and units
- Properties have **room types** (e.g. "Double with sea view") and **rooms** (e.g. "Room 3").
- A property can also model **unique units** (each room its own type) — common for B&Bs listed room-by-room on Airbnb.

### 4.2 Availability
Availability per room type per night = rooms − blocked rooms − rooms booked. Every booking,
cancellation, block or modification triggers an availability push to all connected channels.
Overbooking is not allowed in Phase 0.

### 4.3 Money
- Each booking has a **balance** = total price − payments. Underneath it is a single folio with immutable charge, payment and reversal entries (see PRD §8), so Phase 1 can expose full folios without data changes.
- Prices are stored per night at booking time.
- Taxes: one VAT/sales-tax rate on accommodation and one on extras, plus an optional tourist tax (per person per night or %). Inclusive or exclusive.

### 4.4 Automatic day close
There is no night audit screen. At a configurable time (default 03:00 property time) the system:
marks yesterday's arrivals that never arrived as **no-show candidates** (owner confirms with one
tap, or auto-confirms after 24 h), checks out today's departures (if not done manually), sets
departed rooms to dirty, generates today's cleaning list, and emails the owner a daily summary.

---

## 5. Booking states

```
 (create) ──► confirmed ──► checked_in ──► checked_out
                 │
                 ├─────────► cancelled
                 └─────────► no_show
```
- `checked_in` is set manually, by the guest completing online check-in and the arrival date starting, or automatically at check-in time — configurable per property.
- `checked_out` is set manually or automatically at day close.
- Payment status is shown alongside: `unpaid` · `deposit paid` · `paid` · `refunded` · `payment failed`.

---

## 6. Functional requirements

### 6.1 Signup & onboarding
- **P0-ONB-01** Self-serve signup with email; 14-day free trial without a card.
- **P0-ONB-02** Setup wizard, resumable, with progress bar: property details → rooms → prices → photos & description → connect channels → payments → website → go live.
- **P0-ONB-03** **Import from a listing**: pull property name, description, photos and room types from an existing Booking.com or Airbnb listing where the aggregator supports content import; otherwise manual entry.
- **P0-ONB-04** **Import existing bookings** from the connected OTAs on connection, and via CSV for bookings from other sources, so the calendar is right from day one.
- **P0-ONB-05** Go-live checklist that blocks activation of channel sync until rooms are mapped and future bookings imported (prevents double bookings at switch-over).
- **P0-ONB-06** In-app help centre, contextual tips, and chat support during business hours.
- **P0-ONB-07** Sample "demo property" to explore before entering real data.

### 6.2 Property & rooms
- **P0-PROP-01** Property: name, address, map pin, time zone, currency, check-in/out times, contact details, house rules, cancellation policy, photos, description (multi-language).
- **P0-PROP-02** Room type: name, description, photos, max guests, bed setup, amenities, base price.
- **P0-PROP-03** Room: name/number, room type, cleaning notes.

### 6.3 Calendar
- **P0-CAL-01** Rooms × dates calendar (default 14 days on phone, 30 on desktop) with bookings colour-coded by channel and status.
- **P0-CAL-02** Tap an empty cell to create a booking or a block (owner stay, maintenance, closed).
- **P0-CAL-03** Drag to move a booking to another room or dates (re-price prompt, availability check, channel push).
- **P0-CAL-04** Price and availability row per room type, editable inline.
- **P0-CAL-05** Sync status indicator per channel (green / delayed / error) always visible.

### 6.4 Rates
- **P0-RATE-01** Base price per room type; bulk edit by date range and days of the week; weekend price.
- **P0-RATE-02** Min stay per date range; closed dates.
- **P0-RATE-03** Optional **non-refundable** variant at a % discount, sold on the website and on OTAs that support it.
- **P0-RATE-04** Extra-guest fee above base occupancy; child price.
- **P0-RATE-05** **Per-channel markup** (e.g. OTA prices +15% to cover commission) — keeps the direct website cheapest.
- **P0-RATE-06** Simple discounts: length-of-stay (e.g. 7+ nights −10%) and promo codes for the website.

### 6.5 Channel manager
- **P0-CH-01** Two-way connections to **Booking.com, Agoda, Airbnb and Expedia** via a certified connectivity aggregator (Agoda matters for Asian source markets).
- **P0-CH-02** Push availability, prices and min stay on every change; full resync on demand and nightly.
- **P0-CH-03** Receive new, modified and cancelled bookings; create/update them automatically, including guest details and OTA virtual-card details (tokenized).
- **P0-CH-04** **iCal** import/export for other channels (e.g. Vrbo, local portals). The UI states clearly that iCal is availability-only and can lag, so double bookings are possible on those channels.
- **P0-CH-05** Mapping screen: our room types and rate variants ↔ OTA rooms and rates, with validation.
- **P0-CH-06** Exceptions inbox for bookings that couldn't be applied automatically (unmapped room, no availability), with a push notification and a one-tap fix.
- **P0-CH-07** Push of availability completes within **60 s** of a change for API channels (P95).
- **P0-CH-08** OTA commission % per channel for reporting.

### 6.6 Direct booking engine & website
- **P0-WEB-01** Template website (3–5 themes) generated from property data: home, rooms, gallery, location, contact, policies, booking. No design skills needed.
- **P0-WEB-02** Custom domain or free subdomain; HTTPS automatic.
- **P0-WEB-03** Embeddable booking widget/button for properties that already have a website.
- **P0-WEB-04** Booking flow: dates & guests → available rooms with photos and price variants → extras (e.g. breakfast, parking) → guest details → payment → confirmation. Multi-room in one booking.
- **P0-WEB-05** Total price including taxes and fees shown before payment.
- **P0-WEB-06** Languages: English first; Sinhala and Tamil for staff-facing screens; more guest languages (e.g. German, French, Russian, Chinese) as demand shows; guest-selectable.
- **P0-WEB-07** Basic SEO (titles, meta, structured data for hotels), Google Analytics / Tag Manager, Meta pixel.
- **P0-WEB-08** Guest **manage-booking** link to view, pay balance, and cancel within policy.
- **P0-WEB-09** Mobile page load < 2 s on 4G; WCAG 2.1 AA.
- **P0-WEB-10 (stretch)** Google free booking links via the aggregator.

### 6.7 Reservations
- **P0-RES-01** Create a booking from the calendar or a "New booking" button: dates, room(s), guests, price (auto, editable), source (phone, email, walk-in, other), notes.
- **P0-RES-02** Modify dates, rooms, guests and price; cancel with the policy fee shown before confirming.
- **P0-RES-03** Booking page: guest, stay, price per night, payments, balance, messages sent, online check-in status, timeline of changes.
- **P0-RES-04** Search by name, email, phone, booking number, OTA reference.
- **P0-RES-05** Concurrency: two simultaneous bookings for the last room → exactly one succeeds (applies across website, OTAs and staff).

### 6.8 Payments
- **P0-PAY-01** Integrated payment gateway with **embedded onboarding** (owner verifies identity and bank account inside our wizard); payouts to the owner's bank account.
- **P0-PAY-02** Per-property payment rules: deposit (% or first night) at booking, balance charged automatically **N days before arrival** or on arrival, or full prepayment for non-refundable.
- **P0-PAY-03** Card saved securely by the gateway (SCA / 3-D Secure) for later charges and no-show/cancellation fees.
- **P0-PAY-04** **Payment links** by email or messaging app for phone/email bookings.
- **P0-PAY-05** Charge OTA virtual cards on or after the date the OTA allows (reminder + one-tap charge).
- **P0-PAY-06** Refunds (full / partial) against previous card payments.
- **P0-PAY-07** Record cash and bank-transfer payments manually.
- **P0-PAY-08** Failed automatic charges notify owner and guest; guest gets a link to update their card.
- **P0-PAY-09** The platform never stores full card numbers or CVC.

### 6.9 Guest messaging (automated)
- **P0-MSG-01** Automated emails with editable templates and placeholders (`{{guest_first_name}}`, `{{check_in_time}}`, `{{door_code}}`, `{{directions}}`, `{{online_checkin_link}}`, `{{manage_booking_link}}`):
  - booking confirmation (direct bookings),
  - pre-arrival (N days before) with online check-in link,
  - arrival-day instructions (check-in time, directions, access code),
  - post-stay thank you with review request (links to Google / TripAdvisor / the OTA).
- **P0-MSG-02** Rules per template: which channels it applies to (e.g. don't send confirmation for OTA bookings, OTAs already do), timing, language.
- **P0-MSG-03** Send a one-off email from a booking; replies go to the owner's inbox (reply-to).
- **P0-MSG-04** Delivery status (sent / bounced) on the booking timeline.
- **P0-MSG-05 (stretch)** SMS / WhatsApp delivery for arrival instructions.

### 6.10 Online check-in & guest registration
- **P0-OCI-01** Pre-arrival form: guest details for all adults, ID document data and nationality where legally required, estimated arrival time, special requests, house rules acceptance with signature, balance payment.
- **P0-OCI-02** Completed check-in shown on the calendar and booking.
- **P0-OCI-03** **Guest registration export/submission** in the format required by the launch country's authorities.
- **P0-OCI-04** ID data is field-level encrypted and auto-deleted after the legally required retention period.
- **P0-OCI-05** Optional: show access/door code only after online check-in is completed and the balance is paid.

### 6.11 Housekeeping
- **P0-HK-01** Today's cleaning list: departures (full clean), stay-overs (per property cleaning frequency), arrivals, with notes and next arrival time.
- **P0-HK-02** Rooms become **dirty** at check-out; cleaner marks **clean**; status visible on the calendar.
- **P0-HK-03** Cleaners use a login-free magic link or the Cleaner role; list works on a cheap phone.
- **P0-HK-04** Owner can assign rooms to cleaners and message them.

### 6.12 Day close (automatic)
- **P0-DAY-01** Runs automatically at the configured time per §4.4; idempotent and safe to re-run.
- **P0-DAY-02** Daily summary email/push to owner: today's arrivals and departures, unpaid balances, failed payments, sync problems, yesterday's revenue.
- **P0-DAY-03** Posts accommodation and tourist-tax charges to the hidden folio per night, so revenue reports are by stay date, as in Phase 1.

### 6.13 Guests
- **P0-GST-01** Guest list auto-built from bookings: contact details, stays, total spent, notes, "repeat guest" badge.
- **P0-GST-02** Automatic duplicate matching by email/phone; manual merge.
- **P0-GST-03** Marketing consent captured on the website; CSV export of consenting guests.
- **P0-GST-04** Data protection (PDPA): export a guest's data; anonymize on request (financial records retained as legally required).

### 6.14 Receipts & invoices
- **P0-INV-01** PDF receipt per booking, emailed on payment or at check-out.
- **P0-INV-02** Invoice with sequential numbering, legal fields and tax breakdown where the launch country requires it; credit note for corrections.

### 6.15 Dashboard & reports
- **P0-REP-01** Home dashboard (mobile-first): today's arrivals, departures, in-house, rooms to clean, unpaid balances, sync status, next 30 days occupancy.
- **P0-REP-02** Reports: occupancy and revenue by month; ADR and RevPAR; bookings and revenue by channel with **commission paid**; **direct vs OTA share**; cancellations; tourist tax collected.
- **P0-REP-03** CSV export of bookings, payments and guests (for the accountant).

### 6.16 Notifications to the owner
- **P0-NOT-01** Push (installed web app) and email for: new booking, modification, cancellation, sync error, failed payment, exception needing action. Configurable per type.

### 6.17 Mobile & installability
- **P0-MOB-01** The whole staff product is a responsive **installable web app (PWA)**; every function in Phase 0 is usable on a 6" phone.
- **P0-MOB-02** Native apps are not built in Phase 0.

### 6.18 Subscription & billing (our product)
- **P0-SUB-01** Plans with trial, monthly/annual billing, card on file, invoices for our fee.
- **P0-SUB-02** Plan limits enforced in software (e.g. number of rooms, channels), so later tiers unlock modules without a new product.
- **P0-SUB-03** Self-serve upgrade, downgrade and cancellation; data export on cancellation (see P0-PRIV-07).

### 6.19 Data protection (Sri Lanka PDPA)
Sri Lanka's Personal Data Protection Act (No. 9 of 2022, as amended) has core obligations scheduled
from **1 January 2027**. For guest data, **each property is the data controller** and **we are the
data processor**; for owners' and staff accounts and our own billing, **we are the controller**.
Requirements below are based on founder research and must be **confirmed by a Sri Lankan lawyer**.
- **P0-PRIV-01 Tenant isolation.** One property can never read or change another property's data. Enforced in the database (row-level security) as well as in the application, and covered by automated tests and the VAPT.
- **P0-PRIV-02 Data minimisation.** Collect only what a booking, payment or legal duty needs. ID/passport fields appear only where guest registration requires them (P0-OCI-01).
- **P0-PRIV-03 Retention settings.** Per data type: ID documents deleted after the guest-registration retention period (P0-OCI-04); guest names and contact details anonymized after a configurable period (default to be set with the lawyer and accountant, e.g. after the tax-record period); financial records kept as tax law requires, with personal details removed.
- **P0-PRIV-04 Privacy notices.** Template privacy notice each property shows on its booking website and online check-in (controller: the property; processor: us; hosting outside Sri Lanka), plus our own privacy policy for owner accounts. Templates reviewed by the lawyer.
- **P0-PRIV-05 Data processing terms.** Standard data processing terms in our Terms of Service, accepted by each property at signup: we process guest data only to run the service, list our sub-processors (hosting, email, payments, channel connectivity), notify the property of a breach without delay, and delete data on termination.
- **P0-PRIV-06 Staff access by role.** Cleaners see room names and status only, never guest contact or ID details; staff see what their role needs (§3). Sensitive-data views are logged (P0-AUTH-02).
- **P0-PRIV-07 Export and offboarding.** On request or cancellation, the owner can download all their data (CSV/JSON). After cancellation and a grace period, the property's data is **hard-deleted** from live systems; copies in backups expire automatically within the backup retention window (30 days), which the data processing terms state.
- **P0-PRIV-08 Breach response.** A written procedure: detect, contain, assess, notify affected properties without delay, and support them in notifying the Data Protection Authority and guests as the law requires.
- **P0-PRIV-09 Sub-processor agreements.** Accept the data processing agreements of each provider we use (hosting, email, error tracking, payments, Channex) and keep a list of them.
- **P0-PRIV-10 Encryption.** HTTPS/TLS everywhere; encryption at rest for database, backups and files; field-level encryption for ID data. No credentials in code or the repository (automated secret scanning).

---

## 7. Data model (Phase 0 subset)

Phase 0 uses the entities defined in the PRD and [Phase 1 §7](./phase-1-spec.md#7-data-model-phase-1-entities). The table shows how each surfaces in Phase 0.

| Entity | Phase 0 use |
|---|---|
| Organization, Property | One property per organization |
| User, UserRole | Roles: owner, staff, cleaner |
| RoomType, Room | As in Phase 1; `hk_status` only `clean`/`dirty` |
| RoomBlock | Owner stay / closed / maintenance blocks |
| TaxRule | Accommodation VAT, extras VAT, tourist tax |
| ChargeItem | Extras sold online (breakfast, parking…) |
| Policy | One cancellation policy, one deposit/payment rule set per property |
| RatePlan | Standard + optional non-refundable (derived) |
| RatePrice, Restriction | Prices, min stay, closed |
| PromoCode | Website promo codes |
| GuestProfile | Built from bookings |
| Reservation, RoomStay, StayNight, StayGuest | "Booking" in the UI |
| Folio, FolioEntry, Payment | One hidden folio per booking |
| Invoice | Receipts and invoices |
| Channel, ChannelMessage | OTAs via aggregator, iCal feeds, website |
| HousekeepingTask | Daily cleaning list |
| NightAuditRun | Records each automatic day close |
| OnlineCheckIn | **New**: booking, status, submitted data, signature, completed_at |
| MessageTemplate, EmailMessage | **New/extended**: templates, rules, delivery status |
| Website | **New**: theme, domain, pages, languages, tracking IDs |
| Subscription | **New**: plan, status, trial_end, limits |
| AuditLog | All writes |

---

## 8. Key user flows

### 8.1 Signup to first synced booking (target: one afternoon)
1. Owner signs up, picks "Import from Booking.com", and connects their account through the aggregator.
2. Rooms, photos and description are pre-filled; owner adjusts prices for the next 12 months with the bulk editor.
3. Existing future bookings are imported from Booking.com and Airbnb; owner checks the calendar.
4. Go-live checklist passes → sync is switched on → OTAs now take availability from us.
5. Owner completes payment onboarding and publishes the template website on a subdomain.
6. That evening, a booking arrives from Airbnb: the calendar updates, Booking.com and Expedia availability drops within 60 s, and the owner gets a push notification.

### 8.2 Direct booking guest journey
1. Guest books on the property website, pays a 30% deposit, receives confirmation.
2. 7 days before arrival the balance is charged automatically; receipt emailed.
3. 3 days before: pre-arrival email → guest completes online check-in (ID, ETA, rules).
4. Arrival day: email with directions and door code; booking auto-marked checked in at 15:00.
5. Day after departure: automatic check-out at day close, room marked dirty, cleaner's list updated; thank-you email with Google review link.

### 8.3 Sync exception
1. A Booking.com reservation arrives for a room the owner has blocked for maintenance.
2. It's created and flagged in the exceptions inbox with "Room 2 is blocked; Room 4 is free — move it?"; owner taps "Move" and is done.

---

## 9. Non-functional requirements (Phase 0)

| Area | Requirement |
|---|---|
| Availability | 99.9% monthly for the website, booking engine, channel sync and staff app |
| Sync | P95 availability push < 60 s; reservation ingestion < 60 s from aggregator delivery; nightly full reconciliation with each OTA |
| Performance | Staff app usable on a mid-range Android phone over 4G; calendar interactions < 300 ms P95 |
| Concurrency | No double-sell across website, staff and OTAs (load test 50 concurrent attempts for the last room) |
| Security | TLS, encryption at rest, field-level encryption for ID data, MFA for owners with payments |
| PCI | SAQ-A scope via gateway-hosted payment fields; no card numbers in our systems |
| Privacy | Sri Lanka PDPA: see §6.19 (tenant isolation, retention, notices, data processing terms, export, deletion, breach procedure) |
| Self-serve | ≥ 80% of signups complete setup without support contact |
| Backups | Point-in-time recovery 7 days; daily snapshots 30 days |
| Observability | Alerts on sync lag > 5 min, ingestion failures, payment failures, day-close failures |
| Localisation | English, Sinhala, Tamil for the staff app; English + selected tourist languages for website and emails |

---

## 10. Integrations

| Integration | Purpose | Approach |
|---|---|---|
| Connectivity aggregator | Booking.com, Agoda, Airbnb, Expedia ARI + reservations (+ content import, + Google free booking links if offered) | One certified partner; direct OTA certification later for cost and control |
| iCal | Other channels | Standard feeds, polled every 10–15 min |
| Payment gateway | Embedded owner onboarding, online payments, saved cards, payouts, refunds | One partner with a platform/marketplace model (allows us to earn on payments) |
| Email | Transactional + template emails | Email service provider with bounce webhooks |
| Authorities | Guest registration | Country-specific export or API submission |
| Analytics | Website tracking | GA4 / GTM / Meta pixel IDs set by owner |

---

## 11. Packaging (proposal)

Plan limits in software (P0-SUB-02) let the same product grow with the customer. Prices are TBD.

| Plan | For | Includes |
|---|---|---|
| **Starter** (Phase 0) | 5–20 rooms | Everything in this spec |
| **Hotel** (Phase 1) | 20–60 rooms | + front desk, folios, terminals, night audit, maintenance, roles, upsells, smart locks, accounting integrations |
| **Full service** (Phase 2) | 60–200 rooms | + F&B POS, events & groups, company billing, GDS, guest app, API |
| **Group** (Phase 3+) | Multi-property | + central reservations, shared profiles, loyalty, portfolio reporting, RMS |

Revenue levers: subscription per room or per property tier, plus a margin on payments processed
through the platform (subject to the gateway partner's model).

---

## 12. Milestones (indicative)

| Milestone | Contents | Duration |
|---|---|---|
| M0 Foundations | Tenancy, auth, audit log, data model, CI/CD, observability, PWA shell | 4 weeks |
| M1 Inventory & bookings | Property, rooms, rates, calendar, reservations, guests | 5 weeks |
| M2 Channel manager | Aggregator integration, mapping, ARI push, ingestion, exceptions, iCal, reconciliation | 6 weeks |
| M3 Direct & payments | Booking engine, template website, payment gateway, deposit/balance rules, receipts | 6 weeks |
| M4 Automation | Messaging, online check-in, guest registration, housekeeping, day close, dashboard & reports | 5 weeks |
| M5 Self-serve & beta | Onboarding wizard, imports, subscriptions, help centre, load & security testing | 4 weeks |
| Beta | 10–20 properties, then open signup | 6–8 weeks |

(Assumes ~5–6 engineers, 1 designer, 1 PM. M2 and M3 run partly in parallel; total ~6–7 months to open signup.)

---

## 13. Open questions

1. ~~Launch country~~ — **Resolved: Sri Lanka.** Still to confirm locally: SLTDA licensing and police guest-reporting rules (for P0-OCI-03), current hotel tax stack (VAT, SSCL, TDL, 10% service charge — replaces the generic tax model in §4.3), invoice requirements, and whether properties price in USD, LKR or both (multi-currency may be needed in Phase 0).
2. **Connectivity aggregator** — which partner; does it support Airbnb, content import and Google free booking links?
3. **Payment gateway** — Stripe is not available to Sri Lankan businesses; candidates are PayHere, WebXPay, Dialog Genie and bank gateways. Must support international cards, saved cards (tokenization) for automatic balance charges and, ideally, onboarding each property as a sub-merchant with a platform fee.
4. **Pricing** — per room vs per property tier; is there a free plan?
5. **Support model** — chat only, or phone for onboarding?
6. Do we need **SMS/WhatsApp** in Phase 0 for the launch market, or is email enough?
7. **Unified OTA messaging** (reply to Airbnb/Booking.com guest messages from our app) — high value for this segment; aggregator support decides whether it's Phase 0 or Phase 1.
