# Decision Log

What we decided, when, and **why**. The PRD and specs say *what* the product does; this log keeps
the reasoning, research findings and pending questions behind those choices.

Status: ✅ decided · 🟡 leaning / recommended, not final · ❓ open · ℹ️ finding

---

## Strategy

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-22 | Build an all-in-one hotel platform on **one shared data model** across all modules and properties | ✅ | The value of all-in-one is removing integrations between systems; that only works if every module uses the same guest, reservation, folio and property records |
| 2026-10-22 | **Land at the smallest properties (5–20 rooms) and sell up-market** phase by phase | ✅ | Small properties can buy self-serve and fast; forces simple UX; same product grows with customers via plans |
| 2026-10-22 | Phases: **0** micro (5–20 rooms) → **1** small hotels (20–60) → **2** full-service (60–200) → **3** groups → **4** enterprise | ✅ | See [PRD §7](./PRD.md) |
| 2026-10-22 | Phase 0 uses an **automatic day close** instead of a night audit screen | ✅ | Small properties have nobody awake at midnight; same data is recorded underneath, so Phase 1 can show a full night audit |
| 2026-10-22 | Phase 0 is **self-serve**: signup, trial, setup wizard, import from existing OTA listing, phone-first web app | ✅ | Low price only works if winning and serving each customer costs very little |

## Team & budget

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-23 | **Team: the founder and Claude** for now; bring in a person only if we get really stuck and Lahiru has no answer | ✅ | Founder's decision. Implication: the founder owns the cloud, payment and partner accounts; Claude works when a session is started and has been given access, but isn't monitoring 24/7 |
| 2026-10-23 | **No budget set**; figure it out as we go | ✅ | Founder's decision. Known costs ahead: company registration, lawyer/accountant, cloud hosting, Channex (~$130/month + ~$7/property once live), VAPT (~LKR 0.8–1.7M before beta) |

## Customers

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-23 | **First beta candidate:** a cabana property known to the founder — several cabanas in one place, each sold as a separate room, in a nature area; also sells **day-out packages** and **meals** (not a separate restaurant, as far as known); **internet connectivity can be poor** | ✅ | A good real-world test. Open implications below |
| — | **Day-out packages** (day visits without an overnight stay) aren't in Phase 0 scope | ❓ | Decide after talking to the owner: add a simple "day package" product to Phase 0, or handle outside the system for the beta |
| — | **Meals**: Phase 0 already sells meals as extras (e.g. breakfast); it doesn't yet have **meal-plan rates** (bed & breakfast, half board, full board), which are common in Sri Lanka | ❓ | Confirm with the owner how meals are sold (included in the room price, per meal, per person per day), then decide whether meal-plan rates move into Phase 0 |
| 2026-10-23 | **Restaurant / F&B system stays a later phase** (Phase 2, as in the PRD), not Phase 0 | ✅ | Founder confirmed: meals, not a restaurant, for now. Founder will collect information on the cabana property's food operations for future use, and report back if they run a restaurant model |
| — | **Poor connectivity**: Phase 0 has no offline mode | ❓ | May need the phone app to keep today's bookings and cleaning list available offline, and sync when back online |
| — | Model each cabana as its own room type (unique units), and check whether Channex holiday-rental (per-unit) pricing applies | ❓ | See Channex question 2 |

## Market

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-22 | **Launch country: Sri Lanka** | ✅ | Founder's decision |
| 2026-10-22 | Add **Agoda** alongside Booking.com, Airbnb, Expedia in Phase 0 | ✅ | Strong with Asian source markets |
| 2026-10-22 | Staff UI in **English, Sinhala, Tamil**; guest pages English + main tourist languages | ✅ | Local staff vs international guests |
| — | Price in **USD, LKR or both**? | ❓ | Many properties quote foreigners in USD; may require multi-currency in Phase 0 |
| — | Local rules: hotel tax stack (VAT, SSCL, TDL, 10% service charge), SLTDA licensing, police guest reporting | ❓ | Research found only old/partial information; confirm with a local accountant/lawyer |

## Partners

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-22 | Connectivity aggregator: **Channex** | 🟡 | White-label channel manager API built for PMS companies; connects to major OTAs. List price ~$130/month + ~$7 per connected hotel (or ~$0.50 per holiday-rental unit), excl. VAT, no setup fee. Limits: can't push listing content (photos etc.) to OTAs; needs our certification + each owner's authorization per OTA |
| 2026-10-22 | Payment gateway: **PayHere** as first choice | 🟡 | **Stripe is not available to Sri Lankan businesses.** PayHere is the most widely used local gateway (cards, wallets, LankaQR, LKR settlement) and has a pre-approval / saved-card API. Alternatives: WebXPay, Dialog Genie, bank gateways. Confirm the questions in [partner-questions.md](./partner-questions.md) before committing |
| 2026-10-23 | Channex account setup | ℹ️ | Test ("staging") account is self-serve at staging.channex.io, no sales call or card needed. Channex works only with companies that have a PMS or booking engine (we qualify). No company-registration requirement found. Standard terms; $1,500 fee only if we want custom terms or a security questionnaire |
| 2026-10-22 | Design the payment layer so **a second gateway can be added** | ✅ | Reduces dependence on one local provider |
| 2026-10-22 | We **never hold guests' money** — payments go guest → gateway → property | ✅ | Avoids becoming a regulated payments business |

## Pricing

| Date | Decision | Status | Why |
|---|---|---|---|
| 2026-10-22 | **Tiered monthly plans by property size** + **payments margin** (small extra on card processing fee, only on payments through our system) | 🟡 | Simple to understand; upgrades feel natural; payments margin grows with customers' business. Avoid per-booking commission ("no commission, unlike the OTAs") |
| 2026-10-22 | Starter plan roughly **US$15–30/month**, 14-day free trial, ~2 months free on annual | 🟡 | Guess based on competitors (Little Hotelier ~$29–109/mo; per-room products ~$4–18/room) adjusted for Sri Lanka; **test with 10–20 owners** before deciding. Must sit well above Channex cost (~$7–10 per property) |

## Reference numbers (as of 2026-10-22)

- USD → LKR ≈ **332**. Channex $130 ≈ LKR 43,200/month; $7 ≈ LKR 2,300 per property.
- Example: 50 properties on Channex ≈ $480/month ≈ LKR 159,000 (~$9.60 per property).
- Sri Lanka: 18% VAT on non-resident digital services since 1 July 2026, above LKR 60M/year sales; excludes supplies to VAT-registered businesses.

## Open questions (summary)

See the end of each spec and [partner-questions.md](./partner-questions.md). Most urgent:
1. USD vs LKR pricing.
2. Local tax, licensing and guest-reporting rules.
3. PayHere answers (sub-merchants, saved cards, foreign cards / USD, 3-D Secure).
4. Channex answers (Agoda, unit pricing, VAT, migration, activation times).
5. Final pricing after testing with owners.
