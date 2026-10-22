# Hotel Go Big — Product Requirements Document

| | |
|---|---|
| Status | Draft v0.1 |
| Last updated | 2026-10-22 |
| Phase specs | [Phase 0 — Run the Small Property](./phase-0-spec.md) · [Phase 1 — Run the Rooms](./phase-1-spec.md) |

> Working name. This PRD is intentionally short: it fixes the vision, scope boundaries and
> shared data model. Detailed behaviour lives in the per-phase specs.

---

## 1. Problem

Hotels run on a patchwork of systems: a PMS, a channel manager, a booking engine, a restaurant
POS, a sales & catering tool, a CRM, HR/rostering, payroll and accounting. Each has its own
data, its own logins and its own integration fees. The consequences:

- **Fragmented guest data.** The restaurant doesn't know the guest's allergy recorded at booking;
  the sister property doesn't know they're a repeat VIP.
- **Integration tax.** Hotels pay per-interface fees, and every interface is a failure point.
- **No network view.** Groups consolidate performance in spreadsheets, days late.
- **Legacy UX.** Many incumbent systems are on-premise or desktop-era, hard to train on, with
  high staff turnover making that worse.

## 2. Vision

One cloud platform that runs **every department of a hotel** — and **every hotel in a network** —
on a single shared data model, so a guest, a reservation, a charge or an employee exists exactly
once and every module sees it.

## 3. Target customers

We **land at the smallest properties and sell our way up-market**. Each phase opens the next
segment while keeping every earlier customer on the same product (see §7.1).

| Segment | Size | Why they buy | Phase |
|---|---|---|---|
| Micro properties: B&Bs, guesthouses, inns | 5–20 rooms, owner-operated | No double bookings across OTAs, direct bookings, online payments, automation, low price, self-serve | 0 |
| Small independent hotels | 20–60 rooms, staffed front desk | Replace outdated PMS: front desk, folios, night audit, housekeeping teams | 1 |
| Mid-size & full-service hotels, resorts | 60–200 rooms, several outlets | F&B, events, groups and spa on one guest bill | 2 |
| Small groups / boutique chains | 2–20 properties | Central reservations, shared guest profiles, consolidated reporting, revenue management | 3 |
| Large chains, management companies & franchisors | 20+ properties, mixed ownership | Brand standards, owner reporting, fee calculation, full GL | 4 |

## 4. Personas

| Persona | Primary jobs |
|---|---|
| **Owner-operator** | Does everything at a small property — prices, bookings, payments, guest messages, cleaning schedule — mostly from a phone |
| **Front-desk agent** | Check guests in/out, take bookings, handle payments, answer questions — fast, under pressure |
| **Reservations agent** | Quote, book, modify; manage groups and rate questions |
| **Housekeeper / supervisor** | Know which rooms to clean, in what order; mark status from a phone |
| **Maintenance tech** | Receive and close work orders |
| **General manager** | See today's operation and performance at a glance |
| **Revenue manager** | Set rates and restrictions across channels |
| **F&B / outlet manager** | Run POS, menus, stock and cost |
| **Sales & events manager** | Manage accounts, group blocks, event pipeline, BEOs |
| **Finance / night auditor** | Close the day, reconcile, invoice, export to GL |
| **HR / department head** | Roster staff, track hours, onboard |
| **Group executive** | Compare properties, consolidated P&L |
| **Guest** | Book directly, check in online, request things, pay, leave a review |

## 5. Goals & success metrics

| Goal | Metric | Target (24 months) |
|---|---|---|
| Hotels can run their core operation on us | Live properties | 100 |
| Faster front desk | Median check-in time | < 2 min |
| Grow direct bookings for our customers | Direct share of room revenue | +5 pts vs baseline |
| Fewer systems per hotel | Third-party systems replaced per property | ≥ 3 |
| Reliability | Uptime of booking & front-desk paths | 99.95% |
| Retention | Annual logo churn | < 8% |

## 6. Scope — product modules

Each module is listed with the phase it first ships in and how later phases deepen it.

| # | Module | Summary | Phases |
|---|---|---|---|
| 1 | **PMS core** | Calendar, reservations, front desk, folios, night audit | 0 (calendar, bookings, automatic day close) → 1 (front desk, folios, visible night audit) |
| 2 | **Booking engine & website** | Direct booking, template website | 0 → 1 (upsells) |
| 3 | **Channel manager** | Two-way sync with OTAs | 0 (Booking.com, Airbnb, Expedia via aggregator + iCal) → 2 (GDS, metasearch) |
| 4 | **Payments** | Online payments, saved cards, deposits, refunds, terminals | 0 (online, payment links) → 1 (terminals, pre-auth, cash drawer) |
| 5 | **Housekeeping** | Room status, cleaning lists, task boards | 0 (clean/dirty list) → 1 (supervisor board, inspection, lost & found) |
| 6 | **Maintenance** | Work orders, out-of-order rooms, assets | 1 (work orders) → 2 (preventive, assets) |
| 7 | **Rates & revenue** | Rate plans, restrictions → forecasting & dynamic pricing | 0 (simple) → 1 (derived plans, full restrictions) → 3 (RMS) |
| 8 | **Guest CRM** | Profiles, preferences, history, loyalty | 0 (guest list) → 1 (full profiles) → 3 (network-wide, loyalty) |
| 9 | **Guest digital journey** | Messaging, online check-in, keys, guest app | 0 (automated emails, online check-in) → 1 (smart locks, upsells) → 2 (guest app, mobile key) |
| 10 | **F&B POS & kitchen** | POS, KDS, room service, post-to-room | 2 |
| 11 | **F&B inventory & procurement** | Recipes, stock, suppliers, POs | 3 (incl. central purchasing) |
| 12 | **Events / sales & catering** | Function space, pipeline, BEOs, group blocks | 2 |
| 13 | **B2B sales CRM** | Company accounts, corporate rates, RFPs, commissions | 2 → 3 (RFPs, network accounts) |
| 14 | **Marketing & reputation** | Reviews, segments, campaigns, vouchers | 0 (review requests) → 1 (review aggregation) → 3 (campaigns, vouchers) |
| 15 | **Spa & leisure** | Treatments, resources, memberships | 2 |
| 16 | **Workforce / HR** | Rostering, time & attendance, onboarding | 2 (integrate payroll) |
| 17 | **Finance** | Invoices, accounting export, AR/city ledger → full GL (USALI) | 0 (receipts, CSV) → 1 (accounting integrations) → 2 (AR) → 4 (GL) |
| 18 | **Multi-property layer** | Hierarchy, CRS, shared profiles, portfolio dashboards | 3 |
| 19 | **Ownership & brand** | Owner reporting, fees, brand standards & audits | 4 |
| 20 | **Analytics & AI** | Dashboards, BI, forecasting, assistants | 0 (dashboard) → 1 (operational reports) → 3 (portfolio) → 4 (BI, AI) |
| 21 | **Platform** | Public API, webhooks, marketplace, SSO | 2 (API, marketplace) → 3 (SSO) → 4 (enterprise) |
| 22 | **Self-serve & subscriptions** | Signup, onboarding wizard, plans & plan limits | 0 |
| 23 | **Compliance** | Guest registration, fiscal invoicing, GDPR | 0 (launch country) → per new country |

### Explicitly out of scope (for now)
- Hostels with per-bed inventory, vacation-rental management, timeshare.
- Building our own payment processor (we integrate with one).
- Building our own payroll engine (we integrate).
- Hardware (locks, kiosks, POS terminals) — we integrate with vendors.

## 7. Phase roadmap

| Phase | Theme | Customer it serves | Exit criterion |
|---|---|---|---|
| **0** | *Run the small property* | 5–20 rooms, owner-operated | 50 paying, mostly self-onboarded properties; zero sync-caused double bookings for 60 days |
| **1** | *Run the rooms* | 20–60 rooms, staffed front desk | 3 pilot hotels live 30 days without fallback to old PMS |
| **2** | *Run the whole hotel* | 60–200 rooms, full-service | Pilot hotel runs F&B, events and groups on platform |
| **3** | *Run the network* | Small groups, 2–20 properties | Group runs CRS + shared profiles + consolidated reports |
| **4** | *Run the enterprise* | Large chains, management cos, franchisors | Owner reporting and GL adopted by ≥ 10 properties |

### 7.1 Go-to-market: land small, grow up

1. **Same product, more modules.** Each phase adds modules and depth; nothing is rewritten. A
   Phase 0 calendar becomes the Phase 1 tape chart; a Phase 0 booking balance becomes a Phase 1 folio.
2. **Plans unlock phases.** Plan limits are enforced in software (Starter → Hotel → Full service → Group),
   so a growing customer upgrades instead of switching vendor.
3. **Self-serve at the bottom, sales-assisted at the top.** Phase 0 must sell and onboard without
   us; from Phase 2 we add sales-led onboarding and migration services.
4. **Small customers keep the UX honest.** If an owner can run their property from a phone, high-turnover
   staff at large hotels can learn it fast.
5. **Integrate before we build.** Payroll, accounting, locks and specialist tools are integrations
   until a segment pays for us to own them.

## 8. Shared data model (core entities)

The single most important architectural commitment. Every module reads and writes these; no
module keeps a private copy.

```
Organization ─┬─ Property ─┬─ RoomType ── Room
              │            ├─ Outlet (restaurant, bar, spa, shop)
              │            ├─ FunctionSpace
              │            └─ RatePlan
              ├─ GuestProfile ── (preferences, documents, consents, loyalty)
              ├─ Account (company / travel agent)
              └─ User / Employee ── Role

Reservation ── Stay (room nights) ── assigned Room
     └── Folio ── Charge / Payment / Adjustment
Group / Block ── Reservations
Event ── BEO ── Charges
Task (housekeeping | maintenance) ── Room
AuditLog (every write, every module)
```

Principles:
- **Multi-tenant from day one**: every record carries `organization_id`; property-scoped records
  carry `property_id`. Phases 0–2 ship with one property per org, but the schema doesn't change in Phase 3.
- **Simple UI, full model**: Phase 0 hides folios and night audit behind a booking balance and an
  automatic day close, but writes the same entities, so upgrading a customer needs no data migration.
- **Money is immutable**: charges and payments are never edited, only reversed by new entries.
- **Business date ≠ calendar date**: each property has its own business date advanced by night audit.
- **Time zones**: stored in UTC, displayed in property local time.

## 9. Non-functional requirements

| Area | Requirement |
|---|---|
| Availability | 99.95% for booking, front-desk and payments paths; hotels operate 24/7 |
| Offline resilience | Front desk can view in-house guests and arrivals, and post charges, during a network outage; syncs on reconnect (Phase 2 target; Phase 1: read-only offline arrivals/in-house report) |
| Performance | P95 < 300 ms for front-desk actions; availability search < 1 s |
| Security | SSO (Phase 2), MFA, role-based access, full audit log |
| Compliance | PCI-DSS (via tokenizing gateway — we never store PAN), GDPR/CCPA, local guest-registration rules, local fiscal rules per country |
| Localisation | Multi-language UI, multi-currency, per-property tax rules |
| Data | Daily backups, point-in-time restore, customer data export on request |
| Usability | A new front-desk agent can complete check-in unaided after 1 hour of training; every Phase 0 function works on a phone |
| Self-serve | A 5–20 room property can sign up and go live without contacting us (Phase 0 target: ≥ 80% of signups) |

## 10. Key risks

| Risk | Mitigation |
|---|---|
| Scope explosion — trying to build everything at once | Strict phase gates; each phase has a real customer and exit criterion |
| Small properties pay little and churn | Self-serve onboarding and support to keep cost to serve low; revenue on payments; annual plans |
| A double booking destroys trust with a small owner | Channel sync is Phase 0's most-tested path: reconciliation, alerts, go-live checklist |
| OTA / GDS certification is slow | Start with a connectivity aggregator in Phase 0; direct OTA and GDS certification from Phase 2 |
| Hotels won't switch core PMS mid-season | Migration tooling (import reservations/profiles) and pilot in low season |
| Country-specific fiscal & guest-registration law | Pluggable compliance layer; launch in 1–2 countries first |
| Reliability expectations of a 24/7 business | Invest early in observability, on-call and offline mode |

## 11. Open questions

1. Launch country/countries? (Drives tax, fiscal and guest-registration work from Phase 0.)
2. Payment gateway partner (e.g. Adyen, Stripe)? Must support embedded owner onboarding and platform fees (Phase 0) and terminals (Phase 1).
3. Which connectivity aggregator for Phase 0 (Airbnb support, content import, Google free booking links)?
4. Pricing model: per room per month, per property tier, or modular add-ons? Free plan?
5. Which smart-lock / keypad vendors to integrate in Phase 1?
