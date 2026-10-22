# Partner Questions

Questions to ask partners before committing. Each question comes with **why we're asking**: what
we need it for, and what happens if the answer is "no". Fill in **Answer** after each conversation
and record any resulting decision in the [decision log](./decision-log.md).

---

## PayHere (payment gateway)

### 1. Platform / marketplace model with sub-merchants and automatic fee split
**Ask:** Do you support a platform model where each property is a **sub-merchant** under our
account, onboarded from inside our app, with **our fee split off automatically** from each payment?

**Why we're asking:**
- **Easy onboarding.** Without sub-merchants, every guesthouse owner must apply to PayHere separately,
  wait for approval, then copy technical keys into our app. Many small owners will get stuck there,
  and our "set up in an afternoon" promise breaks.
- **We never hold guests' money.** Money goes guest → PayHere → the property's bank account. If we
  collected the money ourselves and paid properties later, we'd effectively be running a payments
  business, which in Sri Lanka likely needs Central Bank licensing.
- **Our payments margin depends on it.** The automatic fee split is how we earn a small margin on
  each card payment (see decision log, *Pricing*). Without it, that revenue stream doesn't exist.

**If the answer is no:** fallback is "each property opens its own PayHere account and connects it".
Works, but onboarding is harder and there's no payments margin. Consider other gateways.

**Answer:**

### 2. Charging saved cards later through the API
**Ask:** After a guest saves their card (pre-approval), can we charge it **later, automatically,
through the API, without the guest present**? Any limits on amount, timing or number of charges?

**Why we're asking:** this powers the core Phase 0 promise "get paid on time, automatically":
- charging the **balance automatically** e.g. 7 days before arrival,
- charging **no-show and late-cancellation fees**,
- charging **extras or damage** after the guest has left,
- charging **our own subscription** to owners each month.

Without it, owners must either take full payment upfront (fewer bookings), chase guests with payment
links, or accept cash on arrival with no protection against no-shows. PayHere's docs describe a
Preapproval → token → Charging API flow; we need to confirm limits and that it works in our cases.

**If the answer is no:** our automatic payments feature shrinks to "send a payment link"; this is a
major weakening of the Phase 0 product.

**Answer:**

### 3. Foreign cards and USD
**Ask:** Do you accept **foreign-issued cards** (Visa/Mastercard/Amex from Europe, India, China,
etc.)? Can we **charge in USD**, or only LKR? What currency is paid out to the property, and at
what exchange rate and fee?

**Why we're asking:**
- Most guests at Sri Lankan tourist properties are **foreigners paying with foreign cards**. If those
  cards are declined or heavily charged, the booking engine fails at the most important step.
- Many properties **quote prices in USD**. If we can only charge LKR, the guest sees a different amount
  on their card statement than on the website (because of conversion), which causes confusion and disputes.
- The **payout currency and conversion fee** decide how much the owner actually receives, which affects
  whether direct bookings really beat the OTAs for them.

**If the answer is no / limited:** we may need a second gateway for foreign cards, or show prices in
LKR with an approximate USD figure.

**Answer:**

### 4. Bank security checks (3-D Secure) on saved cards
**Ask:** Do you support **3-D Secure** (the bank's extra check, e.g. a one-time code by SMS or a
banking-app approval)? When we charge a **saved card later**, what happens if the guest's bank
demands that check again?

**Why we're asking:**
- Many banks, especially in Europe, require this extra check for online payments. During booking
  the guest is present and can approve it. But an **automatic charge 7 days before arrival happens
  when the guest isn't there** to type a code.
- If the bank insists on the check, the automatic charge **fails**. We need to know how often that
  happens and what PayHere returns, so our system can automatically email the guest a link to approve
  the payment (P0-PAY-08) instead of the owner discovering the problem on arrival day.
- It also affects **fraud protection**: payments approved by 3-D Secure usually shift chargeback
  liability away from the property.

**If the answer is unclear:** we design every automatic charge with a payment-link fallback and warn
owners that some foreign cards may need the guest's approval.

**Answer:**

### 5. Refunds through the API
**Ask:** Can we issue **full and partial refunds** through the API? How long until the guest sees the money?

**Why we're asking:** cancellations within the free-cancellation period, overcharges and goodwill
refunds must be one tap in our app (P0-PAY-06). If refunds have to be done manually in a PayHere
dashboard, owners have to log into two systems, and our records can get out of sync.

**Answer:**

### 6. Fees
**Ask:** What are the fees for local cards, **foreign cards**, wallets and LankaQR? Monthly fees?
Payout timing (how many days until money reaches the owner)? **Chargeback** fees? Special pricing
for a platform bringing many merchants?

**Why we're asking:**
- Our **payments margin** sits on top of PayHere's fee, so we need their exact cost to set our price.
- **Foreign-card fees** are usually higher, and most of our guests use foreign cards.
- **Payout timing** matters a lot to small owners' cash flow; it's a common complaint and selling point.

**Answer:**

### 7. Using PayHere for our own subscription billing
**Ask:** Can we also use PayHere to **charge owners for our subscription** every month or year?

**Why we're asking:** we need a way to collect our own revenue (P0-SUB-01). Using the same gateway
for both keeps things simple; otherwise we need a second billing provider.

**Answer:**

### 8. Webhooks and sandbox
**Ask:** Do you send **automatic notifications (webhooks)** to our system for successful payments,
failures, refunds, chargebacks and payouts? Is there a **sandbox** (test environment) with test cards?

**Why we're asking:** our app must update a booking to "paid" or "payment failed" instantly and
reliably, without anyone checking a dashboard. The sandbox lets our engineers build and test
everything without real money. Poor webhooks or no sandbox = slower development and more bugs.

**Answer:**

### 9. Onboarding requirements per property
**Ask:** What does each property need to be approved: business registration, or can a **sole owner
with only an NIC** be onboarded? Which documents? How long does approval take?

**Why we're asking:** many Sri Lankan guesthouses and homestays are small family or informal
businesses. If PayHere requires formal business registration, a large part of our target customers
can't take online payments through us, and our self-serve onboarding target (≥ 80% without help)
is at risk. Approval time also decides how quickly a new customer can start taking payments.

**Answer:**

---

## Channex (connectivity aggregator)

### 1. Agoda and Airbnb support
**Ask:** Is **Agoda** fully supported (availability, rates, bookings)? Is **Airbnb** supported for
guesthouses and small hotels, not only holiday rentals?

**Why we're asking:** Booking.com, Agoda, Airbnb and Expedia are the Phase 0 channels for Sri Lanka.
Agoda is strong with Asian travellers. If one is missing, we lose a key selling point.

**Answer:**

### 2. Hotel pricing ($7/property) vs holiday-rental pricing ($0.50/unit)
**Ask:** Exactly which properties qualify for **per-unit** pricing? Is it decided by how the property
is listed on the OTA, or by how we set it up in Channex?

**Why we're asking:** many Sri Lankan customers will be villas or a few separate cabanas. The answer
changes our cost per customer and therefore our Starter price.

**Answer:**

### 3. Volume discounts
**Ask:** What are the discounts at 50 / 200 / 1,000 properties?

**Why we're asking:** Channex is a cost on every customer; volume pricing decides our margins as we grow.

**Answer:**

### 4. VAT and invoicing country
**Ask:** From which country do you invoice, and do you charge VAT to a Sri Lankan company?

**Why we're asking:** to know our real monthly cost. Sri Lanka has applied 18% VAT on foreign digital
services since July 2026 (above a sales threshold; supplies to VAT-registered businesses excluded).

**Answer:**

### 5. Moving a property from another Channex customer
**Ask:** If a property is already connected through another company that uses Channex, how do we move it to us?

**Why we're asking:** OTAs allow only one connection per listing. A badly handled switch can leave
availability uncontrolled for a while, which is when double bookings happen.

**Answer:**

### 6. Activation time per OTA
**Ask:** After the owner authorizes, how long does each OTA (Booking.com, Agoda, Airbnb, Expedia)
typically take to activate the connection?

**Why we're asking:** our onboarding promise. Our app can be set up in an afternoon, but if an OTA
takes days, the wizard must say so honestly and show each OTA's status.

**Answer:**

### 7. Content import
**Ask:** Can we import photos, descriptions and room types from an owner's existing OTA listing?

**Why we're asking:** P0-ONB-03 — saves owners from retyping everything, a big part of fast self-serve setup.

**Answer:**

### 8. Certification
**Ask:** What are the certification steps for a new PMS, and how long do they usually take?

**Why we're asking:** certification is on our critical path; the channel manager milestone can't
finish until it's done.

**Answer:**
