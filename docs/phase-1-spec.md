# Phase 1 Spec — "Run the Rooms"

| | |
|---|---|
| Status | Draft v0.1 |
| Last updated | 2026-10-22 |
| Parent doc | [PRD](./PRD.md) |
| Builds on | [Phase 0 — Run the Small Property](./phase-0-spec.md) |

---

## 1. Summary

Phase 1 takes the product up-market from Phase 0's 5–20 room properties to **independent hotels
with 20–60 rooms and a staffed front desk**, which need to replace a full PMS. It adds a real
front desk, folios, card terminals, a visible night audit, housekeeping teams, maintenance,
roles & permissions, upsells, smart-lock integration, review aggregation and accounting
integrations.

Phase 0 already delivers the calendar, simple rates, reservations, the booking engine and website,
the multi-OTA channel manager, online payments, automated messaging, online check-in, guest
registration and a cleaning list. Phase 1 **extends** those rather than rebuilding them; sections
marked *(extends Phase 0)* list only the additions or the full requirement for completeness.

Existing Phase 0 customers can upgrade to the Phase 1 plan (*Hotel*) with no data migration,
because both phases use the same multi-tenant, multi-property data model from the PRD.

### Phase 1 exit criteria
1. **3 pilot hotels** run live on the platform for **30 consecutive days** without falling back to their previous PMS.
2. Every night audit in that period closes successfully, with folio balances reconciling to payments.
3. ≥ 95% of OTA reservations arrive automatically without manual re-keying.
4. Median check-in time < 2 minutes (measured from "start check-in" to "checked in").
5. No P1 incidents (front desk or booking unavailable > 15 min) in the final 14 days.

---

## 2. Scope

### In scope
| Area | Included |
|---|---|
| Setup | Organization, one property, users & roles, taxes, room types, rooms |
| Rates | Rate plans, seasonal prices, restrictions, manual availability control |
| Reservations | Individual reservations, multi-room reservations, modify/cancel/no-show |
| Distribution | Booking engine and multi-OTA channel manager from Phase 0, extended with derived rate plans, full restrictions and upsells |
| Front desk | Tape chart, arrivals/departures/in-house lists, room assignment, check-in, check-out |
| Billing | Folios, charges, payments, refunds, split folios, invoices/receipts |
| Payments | Card tokenization, pre-authorization, capture, refund via one gateway; cash |
| Night audit | Business-date roll, room & tax posting, no-show processing, audit reports |
| Housekeeping | Room status, auto-generated daily task board, mobile web app |
| Maintenance | Work orders, out-of-order / out-of-service rooms |
| Guests | Guest profiles, preferences/notes, stay history, duplicate merge |
| Comms | Phase 0 automated messaging and online check-in, plus modification/cancellation emails and folio emails |
| Upsells | Upgrades, extras, early check-in / late check-out sold at booking and pre-arrival |
| Smart locks | Keypad / smart-lock integration with 1–2 vendors: codes issued at check-in |
| Reputation | Review aggregation and responses |
| Accounting | Integrations with the launch market's most common accounting tools |
| Reports | Operational and daily manager reports (see §6.15) |
| Migration | CSV import of future reservations and guest profiles from legacy PMSs; plan upgrade from Phase 0 |
| Platform | Audit log, role-based access, MFA |

### Out of scope (later phases)
Groups/blocks and allotments · events & function space · F&B POS · guest mobile app and mobile keys ·
GDS / metasearch · loyalty · marketing campaigns · dynamic pricing · AR / city ledger & direct
billing to companies · GL (beyond accounting integrations) · HR / rostering · multi-property / CRS ·
public API · offline write mode · kiosks.

---

## 3. Users, roles & permissions

Roles are fixed in Phase 1 (custom roles in Phase 2). A user can hold multiple roles.

| Permission | Admin | Manager | Front desk | Reservations | Housekeeping | Maintenance | Night auditor |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Property setup, users, taxes | ✅ | | | | | | |
| Rate plans & prices | ✅ | ✅ | | | | | |
| Create / modify reservations | ✅ | ✅ | ✅ | ✅ | | | ✅ |
| Override rate / restrictions | ✅ | ✅ | | | | | |
| Check-in / check-out | ✅ | ✅ | ✅ | | | | ✅ |
| Post charges & payments | ✅ | ✅ | ✅ | | | | ✅ |
| Void / reverse charges, refunds | ✅ | ✅ | | | | | |
| Run night audit | ✅ | ✅ | | | | | ✅ |
| Housekeeping board & status | ✅ | ✅ | ✅ (view) | | ✅ | | |
| Create work orders | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Set out-of-order | ✅ | ✅ | | | | ✅ | |
| Reports | ✅ | ✅ | Operational only | Operational only | | | ✅ |
| Guest data export / erasure | ✅ | | | | | | |

**FR-AUTH-01** All users authenticate with email + password and mandatory TOTP MFA for Admin and Manager.
**FR-AUTH-02** Sessions on shared front-desk terminals time out after 15 min inactivity; quick re-login by PIN for the same user.
**FR-AUTH-03** Every create/update/delete is recorded in the audit log with user, timestamp, before/after values.

---

## 4. Core concepts

### 4.1 Business date
Each property has a **business date** that is independent of the calendar date. It only advances
when night audit completes. All postings (charges, payments) are stamped with the business date
at the time of posting. Reports are by business date.

### 4.2 Room night & availability
- Availability is computed per **room type per night** = physical rooms of that type
  − out-of-order rooms − rooms sold (reservations in `confirmed`, `checked_in`) for that night.
- Out-of-service rooms remain sellable (minor issue) but are flagged; out-of-order rooms are removed from inventory.
- **FR-INV-01** Overbooking is blocked by default. Managers can allow overbooking per room type per night up to a configured limit.

### 4.3 Money rules
- Amounts are stored as integer minor units with an ISO currency code; one currency per property in Phase 1.
- Folio entries are **immutable**. Corrections are made by a reversing entry linked to the original.
- Tax is computed per charge line from the property's tax rules (inclusive or exclusive, percentage or fixed per night/person).

---

## 5. State machines

### 5.1 Reservation
```
           ┌─────────────► cancelled
           │
 (create)──► confirmed ──► checked_in ──► checked_out
           │
           └─────────────► no_show
```
| Transition | Who/what | Rules |
|---|---|---|
| → confirmed | Staff, booking engine, OTA | Availability and restrictions pass (or manager override) |
| confirmed → cancelled | Staff, guest (via link), OTA | Cancellation policy evaluated; fee posted if applicable |
| confirmed → checked_in | Front desk | Arrival date = business date (or manager allows early); room assigned, room status `clean`/`inspected` (warning otherwise); ID captured if required by country |
| confirmed → no_show | Night audit | Arrival date < new business date and not checked in; no-show fee per policy |
| checked_in → checked_out | Front desk | Folio balance = 0 (or manager override to leave balance) |
| checked_out → checked_in | Manager only | "Reinstate" within same business date |

### 5.2 Room status
Two independent dimensions:

- **Occupancy**: `vacant` | `occupied` (derived from reservations)
- **Housekeeping**: `dirty` → `clean` → `inspected` (inspection step optional per property)
- **Availability flag**: `in_service` | `out_of_service` | `out_of_order` (with date range + reason)

Check-out sets the room to `vacant` + `dirty`. Night audit sets all `occupied` rooms to `dirty` (stay-over cleaning).

### 5.3 Work order
`open` → `in_progress` → `done` (or `cancelled`). Priority: `low` | `normal` | `urgent`.

---

## 6. Functional requirements

Each requirement has an ID for traceability to tickets and tests.

### 6.1 Property setup
- **FR-SET-01** Admin configures property: name, address, time zone, currency, check-in/out times, logo, legal entity details, invoice numbering sequence.
- **FR-SET-02** Taxes: name, type (percent / fixed per room-night / fixed per person-night), inclusive/exclusive, applicable charge categories, effective date range.
- **FR-SET-03** Charge catalogue: items (e.g. "Breakfast", "Parking", "Minibar – water") with category, default price, tax mapping.
- **FR-SET-04** Policies: cancellation policies (e.g. free until N days, then first night / % / full stay), deposit policies, no-show policy, child age bands.
- **FR-SET-05** Setup wizard gets a new property to "ready to take bookings" in < 1 hour.

### 6.2 Room types & rooms
- **FR-ROOM-01** Room type: code, name, description, photos, max occupancy (adults/children), base occupancy, bed configuration, amenities.
- **FR-ROOM-02** Room: number, room type, floor, features (view, accessible, connecting-to), housekeeping credits (cleaning effort weight).
- **FR-ROOM-03** Changing a room's type must not break existing assignments; future reservations assigned to that room are flagged for review.

### 6.3 Rates & availability
- **FR-RATE-01** Rate plan: code, name, linked room types, meal plan (room only / breakfast), cancellation policy, deposit policy, channels it's sold on, visible from/to dates.
- **FR-RATE-02** Prices per room type per date, set by bulk editor (date range + days of week) and by calendar grid. Extra-adult and child prices.
- **FR-RATE-03** Derived rates: a rate plan can be defined as base rate ± amount or %, e.g. "Non-refundable = BAR − 10%".
- **FR-RATE-04** Restrictions per rate plan / room type / date: min stay, max stay, closed, closed to arrival (CTA), closed to departure (CTD).
- **FR-RATE-05** Promo codes: code, discount (% or amount), applicable rate plans, booking window, stay window, usage limit.
- **FR-RATE-06** Availability & rate grid shows per date: rooms available per type, price per rate plan, restrictions, occupancy %.
- **FR-RATE-07** Any change pushes to the booking engine immediately and to the OTA within 60 seconds.

### 6.4 Reservations
- **FR-RES-01** Create reservation from availability search (dates, adults, children → available room types & rates with total price incl. taxes).
- **FR-RES-02** A reservation holds one booker (guest profile) and 1..n room stays; each stay has its own room type, rate plan, guests, dates and optional assigned room. (Multi-room booking for families/small parties; true groups are Phase 2.)
- **FR-RES-03** Capture: guest details, arrival time, special requests, source (walk-in, phone, email, booking engine, OTA), market segment, card guarantee (tokenized) or deposit.
- **FR-RES-04** Modify: dates, room type, rate, guests. Price re-quotes; staff choose to keep original price or apply new price (permission-controlled).
- **FR-RES-05** Cancel: applies cancellation policy, shows fee before confirming, posts fee to folio, charges token if configured.
- **FR-RES-06** Confirmation number is unique per property, human-friendly (e.g. `HGB-7K2QX`). OTA reservations also store the OTA reference.
- **FR-RES-07** Search reservations by name, confirmation no., OTA ref, email, phone, dates, room.
- **FR-RES-08** Reservation timeline shows full history of changes (from audit log).
- **FR-RES-09** Daily price breakdown is stored per night at booking time; later rate changes don't alter existing reservations.

**Acceptance criteria (sample)**
- Given 1 room left of type DBL on 12 Mar, when two agents try to book it simultaneously, exactly one succeeds and the other receives "no longer available".
- Given a reservation with a "free cancel until 48h" policy, when cancelled 24h before arrival, the first-night fee is posted and shown before confirmation.

### 6.5 Booking engine (extends Phase 0)
Phase 0 delivers the booking engine and template website (P0-WEB-01…10). Phase 1 adds rate-plan
payment modes, guarantee-only bookings and upsells (§6.17). The full requirement set is:
- **FR-BE-01** Hosted, responsive booking pages at `book.<platform>/<property>` and embeddable widget for the hotel's website.
- **FR-BE-02** Flow: dates & guests → room types with photos & rate options → extras (charge-catalogue items flagged as sellable online) → guest details → payment/guarantee → confirmation.
- **FR-BE-03** Shows total price including taxes and fees before payment (required by consumer-protection law in many markets).
- **FR-BE-04** Supports promo codes, multiple rooms per booking, languages (as Phase 0: English, plus Sinhala/Tamil staff UI), property currency.
- **FR-BE-05** Payment modes per rate plan: guarantee only (card tokenized), deposit %, or full prepayment. 3-D Secure / SCA supported.
- **FR-BE-06** Guest receives a manage-booking link to view, modify (if rate allows) and cancel.
- **FR-BE-07** Page load < 2 s on 4G; Lighthouse accessibility score ≥ 90.
- **FR-BE-08** Analytics hooks: Google Analytics 4 / Tag Manager events for search, select, book.

### 6.6 Channel manager (extends Phase 0)
Phase 0 delivers connections to Booking.com, Agoda, Airbnb and Expedia plus iCal (P0-CH-01…08). Phase 1
adds mapping of multiple and derived rate plans, CTA/CTD/max-stay restrictions, and the exceptions
queue on the front-desk screens.
- **FR-CH-01** Map room types and rate plans to each OTA's equivalents.
- **FR-CH-02** Push availability, rates and restrictions (ARI) on change, plus full resync on demand.
- **FR-CH-03** Receive new, modified and cancelled reservations; create/update reservations automatically, including virtual card details where provided (tokenized).
- **FR-CH-04** Failed or unmappable messages go to an **exceptions queue** visible to front desk with a clear reason and a retry/resolve action.
- **FR-CH-05** OTA commission % stored per channel for reporting.

### 6.7 Front desk
- **FR-FD-01** **Tape chart**: rooms × dates grid showing reservations, statuses, out-of-order blocks; drag to assign/move/extend (with re-price prompt); unassigned reservations panel.
- **FR-FD-02** **Arrivals** list for business date: guest, room type, assigned room, room status, balance, special requests, VIP/notes flags. Bulk auto-assign rooms (rules: matching type, clean first, respect preferences/features).
- **FR-FD-03** **Check-in**: confirm guest details, capture ID/document data where required, assign room, take payment or pre-authorization, print/email registration card, (signature on screen or tablet), mark checked in. Target ≤ 6 clicks for a pre-assigned, pre-paid reservation.
- **FR-FD-04** **In-house** list with room, guest, departure date, balance.
- **FR-FD-05** **Room move** mid-stay: change room, record reason, set old room dirty.
- **FR-FD-06** **Extend / shorten stay** from in-house view with re-price.
- **FR-FD-07** **Departures** list; **check-out**: review folio, settle balance, email/print invoice, mark checked out. Express check-out for zero-balance folios.
- **FR-FD-08** **Walk-in**: create reservation and check in in one flow.
- **FR-FD-09** **Shift log / handover notes** per business date visible to all front-desk users.
- **FR-FD-10** **Guest registration export** in the format required by the launch country's authorities (see open question 1).

### 6.8 Folios & billing
- **FR-FOL-01** Every room stay has at least one folio; staff may create additional folios (e.g. "Room → company", "Extras → guest").
- **FR-FOL-02** Routing rules per reservation: send charge categories to a chosen folio automatically.
- **FR-FOL-03** Post charges from the charge catalogue or ad-hoc (permission-controlled), with quantity, price, tax.
- **FR-FOL-04** Transfer entries between folios (same or other reservation), logged.
- **FR-FOL-05** Reverse an entry (Manager permission, reason required); original stays visible.
- **FR-FOL-06** Payments: card (token / terminal), cash, bank transfer (manual record). Payment links by email for remote payment.
- **FR-FOL-07** Refunds against a prior card payment, up to amount paid.
- **FR-FOL-08** Invoice generation on folio close: sequential numbering per legal entity, legal fields, tax breakdown, PDF, credit notes for corrections. Invoices are immutable once issued.
- **FR-FOL-09** Folio balance always equals Σ charges − Σ payments; enforced by tests and nightly reconciliation.

### 6.9 Payments
- **FR-PAY-01** Integrate one PCI-compliant gateway; the platform never stores or logs full card numbers (PAN) or CVC.
- **FR-PAY-02** Operations: tokenize, pre-authorize, incremental auth, capture, void, refund.
- **FR-PAY-03** Card-present: support the gateway's terminals at front desk (cloud terminal API).
- **FR-PAY-04** Cash drawer per user shift: opening float, cash in/out, closing count, variance report.
- **FR-PAY-05** Daily payment reconciliation report matches platform payments to gateway settlements.

### 6.10 Night audit
- **FR-NA-01** Pre-checks with clear blockers/warnings: arrivals not checked in, departures not checked out, unresolved OTA exceptions, open cash drawers.
- **FR-NA-02** Process no-shows per policy (status change + fee posting/charge).
- **FR-NA-03** Post room and tax charges for every in-house stay for the business date, using the stored nightly price.
- **FR-NA-04** Set occupied rooms to dirty; generate next day's housekeeping board.
- **FR-NA-05** Roll business date +1.
- **FR-NA-06** Produce and archive audit reports (PDF + data): daily revenue by category, payments by method, folio balances, occupancy/ADR/RevPAR, no-shows, cancellations.
- **FR-NA-07** Audit is idempotent and resumable: if interrupted, re-running completes remaining steps without double-posting.
- **FR-NA-08** Optional auto-run at a configured time if no blockers.
- **FR-NA-09** Accounting export: daily journal CSV (revenue, tax, payments, by mapped account codes) for import into the hotel's accounting software.

### 6.11 Housekeeping
- **FR-HK-01** Mobile web app (works on cheap Android phones): my rooms today, in priority order (arrivals first, then departures, then stay-overs).
- **FR-HK-02** Housekeeper marks room `cleaning started`, `clean`; supervisor marks `inspected`. Status visible to front desk in real time (< 5 s).
- **FR-HK-03** Supervisor board: all rooms with occupancy, HK status, assignee, arrival/departure flags, notes; drag to assign; auto-distribute by credits across on-duty housekeepers.
- **FR-HK-04** Do-not-disturb / service refused recorded.
- **FR-HK-05** Housekeeper can raise a work order with photo from the room.
- **FR-HK-06** Lost & found log: item, room, found by, date, status (stored, returned, disposed).
- **FR-HK-07** Interface languages: at least 3 (EN + launch-country + one common staff language), switchable per user.

### 6.12 Maintenance
- **FR-MT-01** Work order: room or location, category, description, photo, priority, assignee, status.
- **FR-MT-02** From a work order, set the room out-of-order (date range, reason) — removes from availability; warns if reservations are assigned.
- **FR-MT-03** Maintenance staff see their open work orders on mobile and close with a note/photo.
- **FR-MT-04** Urgent work orders notify assignee and manager by email/push.

### 6.13 Guest profiles
- **FR-GST-01** Profile: name, contact, address, language, nationality, date of birth, ID document data (where legally required; encrypted at rest), company, preferences, notes, VIP level, marketing consent.
- **FR-GST-02** Stay history and lifetime revenue on profile.
- **FR-GST-03** Duplicate detection on create (email/phone/name match) and manual merge.
- **FR-GST-04** GDPR: export a guest's data; anonymize on request while keeping financial records legally required.
- **FR-GST-05** Allergy/accessibility flags surfaced on arrivals list and reservation header.

### 6.14 Notifications (extends Phase 0)
Phase 0's automated messaging and online check-in (P0-MSG, P0-OCI) carry over unchanged.
- **FR-NOT-01** Templated, per-language emails: booking confirmation, modification, cancellation, pre-arrival (N days before), post-stay thank you with folio/invoice.
- **FR-NOT-02** Admin can edit template text and logo; preview and test-send.
- **FR-NOT-03** Email delivery status (sent, bounced) visible on reservation.

### 6.15 Reports (Phase 1 set)
| Report | Key content |
|---|---|
| Manager's daily report | Occupancy, ADR, RevPAR, room revenue, other revenue, MTD/YTD vs last year (once data exists) |
| Arrivals / departures / in-house | Operational lists, printable |
| Forecast | Next 30/90/365 days: rooms sold, occupancy, revenue on the books |
| Pickup | Rooms/revenue booked in last N days for future dates |
| Revenue by category | Per business date range |
| Payments by method | Per date range, per user |
| Folio balances | Open balances of in-house and checked-out guests |
| Reservations by source/channel | Count, room nights, revenue, commission |
| Cancellations & no-shows | Per date range, with fees |
| Housekeeping productivity | Rooms cleaned per housekeeper, average time |
| Audit log | Filter by user, entity, date |

All reports export to CSV and PDF.

### 6.16 Data migration
- **FR-MIG-01** CSV import for guest profiles and future reservations (with assigned rooms and prices), with validation preview, error report and dry-run mode.
- **FR-MIG-02** Import templates documented for the 2–3 most common legacy PMSs in the launch market.
- **FR-MIG-03** Phase 0 customers upgrade in-app: hidden folios become visible, the automatic day close becomes the night audit (auto-run stays on by default), cleaning lists become the housekeeping board. No data is re-imported.

### 6.17 Upsells
- **FR-UP-01** Upsell offers: room upgrade (price difference per night), extras from the charge catalogue, early check-in, late check-out; each with price, availability limits and images.
- **FR-UP-02** Offered in the booking engine flow and in the pre-arrival email / online check-in.
- **FR-UP-03** Upgrade offers only shown when the target room type has availability; accepted upgrades re-assign room type and re-price.
- **FR-UP-04** Accepted upsells post to the folio and appear on the arrivals list.
- **FR-UP-05** Report: upsell revenue by offer and conversion rate.

### 6.18 Smart locks & keypads
- **FR-LOCK-01** Integrate 1–2 keypad / smart-lock vendors common in the launch market.
- **FR-LOCK-02** Generate a unique access code per stay, valid from check-in time to check-out time; revoke on check-out, cancellation or room move.
- **FR-LOCK-03** Code delivered via the Phase 0 arrival message, gated on online check-in and payment if configured (P0-OCI-05).
- **FR-LOCK-04** Lock offline or code-generation failure alerts the front desk before the guest arrives.

### 6.19 Reputation & accounting integrations
- **FR-REV-01** Aggregate reviews from Google, TripAdvisor and connected OTAs where available; show rating trends.
- **FR-REV-02** Respond to Google reviews from the app; link out for sources without a response API.
- **FR-ACC-01** Push the daily journal (FR-NA-09) to the launch market's 1–2 most common accounting tools via API, with account mapping; CSV remains available.

---

## 7. Data model (Phase 1 entities)

Every table carries `id` (UUID), `organization_id`, `created_at`, `updated_at`, `created_by`. Property-scoped tables also carry `property_id`.

| Entity | Key fields |
|---|---|
| Organization | name, billing details |
| Property | name, address, time_zone, currency, business_date, check_in_time, check_out_time, settings |
| User | email, name, language, mfa_secret, status |
| UserRole | user_id, property_id, role |
| RoomType | code, name, description, max_adults, max_children, base_occupancy, amenities[] |
| Room | number, room_type_id, floor, features[], hk_credits, hk_status, availability_flag |
| RoomBlock | room_id, type (out_of_order / out_of_service), from, to, reason, work_order_id |
| TaxRule | name, kind, rate/amount, inclusive, categories[], valid_from, valid_to |
| ChargeItem | code, name, category, default_price, tax_rule_ids[], sellable_online |
| Policy | kind (cancellation / deposit / no_show), rules (JSON) |
| RatePlan | code, name, parent_rate_plan_id, derivation, meal_plan, cancellation_policy_id, deposit_policy_id, channels[] |
| RatePrice | rate_plan_id, room_type_id, date, price, extra_adult, child_prices |
| Restriction | rate_plan_id?, room_type_id?, date, min_stay, max_stay, closed, cta, ctd |
| PromoCode | code, discount, rate_plan_ids[], booking_window, stay_window, usage_limit, used_count |
| GuestProfile | names, contacts, address, language, nationality, dob, documents (encrypted), preferences, notes, vip, consents |
| Reservation | confirmation_no, booker_profile_id, source, channel_id, channel_ref, market_segment, status, special_requests, guarantee_token, created_via |
| RoomStay | reservation_id, room_type_id, rate_plan_id, room_id?, arrival, departure, adults, children, status |
| StayNight | room_stay_id, date, price, tax_breakdown |
| StayGuest | room_stay_id, guest_profile_id, is_primary |
| Folio | room_stay_id or reservation_id, name, status (open/closed), payer_profile_id |
| FolioEntry | folio_id, type (charge/payment/reversal/transfer), charge_item_id?, amount, tax_amount, business_date, reverses_entry_id?, posted_by |
| Payment | folio_entry_id, method, gateway_ref, token_id, status |
| Invoice | folio_id, number, issued_at, pdf_url, credit_note_for_id? |
| CashDrawer | user_id, business_date, opening_float, closing_count, status |
| Channel | name, type (booking_engine/ota), commission_pct, mappings (JSON) |
| ChannelMessage | channel_id, direction, payload, status, error, reservation_id? |
| HousekeepingTask | room_id, business_date, type (departure/stayover/arrival-check), assignee_id, status, started_at, finished_at |
| WorkOrder | room_id?, location, category, description, photos[], priority, assignee_id, status |
| LostFoundItem | room_id?, description, found_by, found_at, status |
| NightAuditRun | business_date, status, step, started_at, finished_at, report_ids[] |
| ShiftNote | business_date, author_id, text |
| EmailMessage | reservation_id?, template, to, status, provider_ref |
| AuditLog | actor_id, entity, entity_id, action, before, after, at |

---

## 8. Key user flows (end-to-end)

### 8.1 Direct booking → stay → checkout
1. Guest searches booking engine for 2 nights, 2 adults → sees DBL at €120/night BAR and €108 NR.
2. Picks NR, adds breakfast, pays in full with 3-D Secure → reservation `confirmed`, StayNights stored, payment posted to folio, confirmation email sent, OTA availability reduced.
3. 3 days before arrival: pre-arrival email.
4. Night before: room auto-assigned during arrivals preparation.
5. Arrival day: housekeeper cleans room → `clean`; front desk checks guest in (balance 0, pre-auth for incidentals) in < 2 min.
6. Night audit ×2: room nights already prepaid; breakfast charges posted; room set dirty for stay-over.
7. Departure: minibar charge posted, guest pays by terminal, invoice emailed, room `vacant dirty`.

### 8.2 OTA booking with exception
1. OTA sends a reservation for a rate plan that's not mapped → message lands in exceptions queue.
2. Front desk sees a red badge, opens the exception, maps it to the correct rate plan, clicks "Create reservation".
3. Reservation created with OTA ref; future messages for that rate auto-map.

### 8.3 Out-of-order room
1. Housekeeper reports a leak with a photo → urgent work order → maintenance & manager notified.
2. Maintenance sets room 204 out-of-order for 2 days → availability for that type drops by 1 for those dates and pushes to channels; system warns that tomorrow's arrival is assigned to 204 and suggests an alternative room.

---

## 9. Non-functional requirements (Phase 1)

| Area | Requirement |
|---|---|
| Availability | 99.9% monthly for staff app & booking engine (99.95% target from Phase 2) |
| Performance | P95 < 300 ms for front-desk API calls; availability search < 1 s; tape chart for 150 rooms × 30 days renders < 1.5 s |
| Concurrency | Inventory decrements are transactional; no double-sell under concurrent bookings (load test: 50 concurrent bookings for last room) |
| Offline | Downloadable/printable arrivals, in-house and departures report refreshed every 15 min to the front-desk device for outages |
| Security | TLS everywhere; encryption at rest; ID documents field-level encrypted; MFA; OWASP ASVS L2 |
| PCI | SAQ-A / SAQ-A-EP scope via gateway-hosted fields; no PAN in logs, DB or backups |
| Privacy | GDPR: data processing agreement, export/anonymize, configurable retention |
| Backups | Point-in-time recovery 7 days; daily snapshots 30 days; restore tested monthly |
| Observability | Structured logs, metrics, tracing; alerting on booking failures, OTA sync lag > 5 min, night audit failure |
| Browser support | Last 2 versions of Chrome, Edge, Safari, Firefox; housekeeping app on Android 10+ / iOS 15+ |
| Localisation | UI strings externalized; English, Sinhala, Tamil; date/number/currency formats per locale |
| Accessibility | WCAG 2.1 AA for booking engine; staff app keyboard-navigable |

---

## 10. Integrations

| Integration | Purpose | Approach |
|---|---|---|
| Payment gateway | Tokenize, auth, capture, refund, terminals | One partner (TBD — open question 2) |
| OTA connectivity | ARI push, reservation pull | Phase 0 aggregator integration, extended for more rate plans and restrictions |
| Email | Transactional email | Email service provider with bounce webhooks |
| Accounting | Daily journal | API push to 1–2 accounting tools; CSV export with account mapping |
| Smart locks | Access codes per stay | 1–2 vendor APIs (open question 3) |
| Reviews | Aggregation and responses | Google Business Profile API; other sources where available |
| Authorities | Guest registration | Country-specific export/submission (open question 1) |

---

## 11. Milestones (indicative)

| Milestone | Contents | Duration |
|---|---|---|
Foundations, distribution, online payments and messaging come from Phase 0, so Phase 1 is shorter.

| Milestone | Contents | Duration |
|---|---|---|
| M1 Roles & rates | Full roles & permissions, derived rate plans, full restrictions, guest profiles | 4 weeks |
| M2 Front desk & money | Tape chart, arrivals/departures, check-in/out, folios & routing, terminals, pre-auth, invoices, cash drawer | 6 weeks |
| M3 Night audit & operations | Visible night audit, housekeeping board, maintenance & out-of-order, reports | 5 weeks |
| M4 Revenue add-ons | Upsells, smart locks, review aggregation, accounting integrations | 5 weeks |
| M5 Pilot readiness | Legacy PMS import, Phase 0 upgrade path, load & security testing, training material | 3 weeks |
| Pilot | 3 hotels live; fix & iterate | 6–8 weeks |

(Assumes a team of ~6–8 engineers, 1 designer, 1 PM. M3 and M4 can partly overlap.)

---

## 12. Open questions

1. ~~Launch country~~ — **Resolved: Sri Lanka** (see Phase 0 open question 1 for what still needs confirming).
2. **Payment gateway** partner and terminal hardware.
3. **Smart-lock / keypad vendors** to integrate first.
4. **Pilot hotels** — identified? Their current PMS determines import templates. Could any Phase 0 customers at the top of the size range be pilots?
5. Should the **inspection** step be mandatory for pilot hotels?
6. Is **pre-paid deposit handling** subject to escrow/trust rules in the launch country?
7. Tech stack and hosting region (data residency).

---

## 13. Glossary

| Term | Meaning |
|---|---|
| ADR | Average Daily Rate = room revenue ÷ rooms sold |
| ARI | Availability, Rates and Inventory (data pushed to channels) |
| BAR | Best Available Rate — the standard public rate |
| CTA / CTD | Closed to Arrival / Closed to Departure |
| Folio | The guest's running bill |
| Night audit | End-of-day process that posts charges and advances the business date |
| OOO / OOS | Out of Order (unsellable) / Out of Service (sellable, flagged) |
| OTA | Online Travel Agency (Booking.com, Expedia…) |
| RevPAR | Revenue per Available Room = room revenue ÷ rooms available |
| Tape chart | Rooms × dates visual grid of reservations |
