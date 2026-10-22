# Hotel Go Big — Product Requirements Document

| | |
|---|---|
| Status | Draft v0.1 |
| Last updated | 2026-10-22 |
| Companion doc | [Phase 1 MVP Spec](./phase-1-spec.md) |

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

| Segment | Size | Why they buy | Phase |
|---|---|---|---|
| Independent hotels | 20–150 rooms | Replace outdated PMS, cut integration costs | 1 |
| Small groups / boutique chains | 2–20 properties | Central reservations, shared guest profiles, consolidated reporting | 3 |
| Management companies & franchisors | 20+ properties, mixed ownership | Brand standards, owner reporting, fee calculation | 4+ |
| Resorts & full-service hotels | 150+ rooms, many outlets | Events, F&B, spa all on one folio | 2–3 |

## 4. Personas

| Persona | Primary jobs |
|---|---|
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

Each module is listed with the phase it first ships in. Later phases deepen earlier modules.

| # | Module | Summary | Phase |
|---|---|---|---|
| 1 | **PMS core** | Reservations, front desk, room inventory, folios, night audit | 1 |
| 2 | **Booking engine** | Direct website booking with upsells | 1 |
| 3 | **Channel manager** | Two-way sync with OTAs | 1 (one OTA) → 2 (major OTAs) → 3 (GDS) |
| 4 | **Payments** | Card capture, tokenization, pre-auth, refunds | 1 |
| 5 | **Housekeeping** | Room status, task boards, mobile app | 1 |
| 6 | **Maintenance** | Work orders, out-of-order rooms | 1 (basic) → 2 (preventive, assets) |
| 7 | **Rates & revenue** | Rate plans, restrictions → forecasting & dynamic pricing | 1 (manual) → 4 (RMS) |
| 8 | **Guest CRM** | Profiles, preferences, history | 1 (basic) → 3 (network-wide, loyalty) |
| 9 | **Guest app / digital journey** | Online check-in, digital key, messaging | 2 |
| 10 | **F&B POS & kitchen** | POS, KDS, room service, post-to-room | 2 |
| 11 | **F&B inventory & procurement** | Recipes, stock, suppliers, POs | 3 |
| 12 | **Events / sales & catering** | Function space, pipeline, BEOs, group blocks | 2 |
| 13 | **B2B sales CRM** | Accounts, corporate rates, RFPs, commissions | 3 |
| 14 | **Marketing** | Segments, campaigns, vouchers, reviews | 3 |
| 15 | **Spa & leisure** | Treatments, resources, memberships | 3 |
| 16 | **Workforce / HR** | Rostering, time & attendance, onboarding | 3 (integrate payroll) |
| 17 | **Finance** | AR/city ledger, AP, GL export → full GL (USALI) | 1 (export) → 3 (AR) → 4 (GL) |
| 18 | **Multi-property layer** | Hierarchy, CRS, shared profiles, portfolio dashboards | 3 |
| 19 | **Ownership & brand** | Owner reporting, fees, brand standards & audits | 4 |
| 20 | **Analytics & AI** | Dashboards, BI, forecasting, assistants | 1 (operational reports) → 4 |
| 21 | **Platform** | Public API, webhooks, app marketplace, SSO | 2 (API) → 4 (marketplace) |

### Explicitly out of scope (for now)
- Hostels with per-bed inventory, vacation-rental management, timeshare.
- Building our own payment processor (we integrate with one).
- Building our own payroll engine (we integrate).
- Hardware (locks, kiosks, POS terminals) — we integrate with vendors.

## 7. Phase roadmap

| Phase | Theme | Customer it serves | Exit criterion |
|---|---|---|---|
| **1** | *Run the rooms* | One independent hotel, rooms-only | 3 pilot hotels live 30 days without fallback to old PMS |
| **2** | *Run the whole hotel* | Full-service single property | Pilot hotel runs F&B and events on platform |
| **3** | *Run the network* | Small group, 2–20 properties | Group runs CRS + shared profiles + consolidated reports |
| **4** | *Optimise* | Larger groups / management cos | RMS and GL adopted by ≥ 10 properties |

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
  carry `property_id`. Phase 1 ships with one property per org, but the schema doesn't change in Phase 3.
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
| Usability | A new front-desk agent can complete check-in unaided after 1 hour of training |

## 10. Key risks

| Risk | Mitigation |
|---|---|
| Scope explosion — trying to build everything at once | Strict phase gates; each phase has a real customer and exit criterion |
| OTA / GDS certification is slow | Start certification in Phase 1; use a connectivity aggregator initially |
| Hotels won't switch core PMS mid-season | Migration tooling (import reservations/profiles) and pilot in low season |
| Country-specific fiscal & guest-registration law | Pluggable compliance layer; launch in 1–2 countries first |
| Reliability expectations of a 24/7 business | Invest early in observability, on-call and offline mode |

## 11. Open questions

1. Launch country/countries? (Drives tax, fiscal and guest-registration work in Phase 1.)
2. Payment gateway partner (e.g. Adyen, Stripe)? Drives pre-auth and terminal support.
3. First OTA connection: direct to Booking.com or via a connectivity aggregator?
4. Pricing model: per room per month, per property tier, or modular add-ons?
5. Build vs partner for door-lock integration in Phase 1?
