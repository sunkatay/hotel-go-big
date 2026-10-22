# Partner Questions

Questions to ask partners before committing. Update the **Answer** column after each conversation.

## PayHere (payment gateway)

| # | Question | Why it matters | Answer |
|---|---|---|---|
| 1 | Do you support a **platform / marketplace model**, where each property is a **sub-merchant** under our account, onboarded from inside our app, with an **automatic fee split** to us? | Easy onboarding for owners; we never hold guests' money; enables our payments margin | |
| 2 | Can **saved cards (pre-approval tokens)** be charged later through the API, without the guest present? For any amount up to what limit? | Automatic balance charge before arrival, no-show and cancellation fees, extras (P0-PAY-02/03) | |
| 3 | Do you accept **foreign cards**, and can you charge in **USD** (or only LKR)? Settlement currency? | Most guests are foreign tourists; many properties price in USD | |
| 4 | Do you support **3-D Secure / strong authentication** on saved cards, and what happens when a later charge needs the guest to re-authenticate? | Failed automatic charges must fall back to a payment link (P0-PAY-08) | |
| 5 | **Refunds** (full / partial) through the API? | P0-PAY-06 | |
| 6 | **Fees**: card %, foreign-card %, monthly fees, payout timing, chargeback fees. Volume pricing for a platform? | Pricing model and margins | |
| 7 | Can we use PayHere for **our own subscription billing** (charging owners monthly)? | P0-SUB-01 | |
| 8 | Webhooks for payment status, refunds, chargebacks, payouts? Sandbox for testing? | Integration effort | |
| 9 | Onboarding requirements per property (business registration? sole owners with NIC only?) and approval time | Many guesthouses are informal or sole-owner businesses | |

## Channex (connectivity aggregator)

| # | Question | Why it matters | Answer |
|---|---|---|---|
| 1 | Is **Agoda** supported, and Airbnb for hotels/guesthouses (not only rentals)? | Core Phase 0 channels for Sri Lanka | |
| 2 | Which pricing applies to a **guesthouse or villa** — $7 per hotel or $0.50 per unit? | Cost per property | |
| 3 | Volume discounts at 50 / 200 / 1,000 properties? | Margins as we grow | |
| 4 | Do you charge **VAT** to a Sri Lankan company, and from which country do you invoice? | Real monthly cost | |
| 5 | How do we **migrate a property already connected through another Channex customer**? | Avoid a gap or double bookings during switch-over | |
| 6 | Typical **activation time** per OTA after the owner authorizes (Booking.com, Agoda, Airbnb, Expedia)? | Onboarding promise ("live in an afternoon") | |
| 7 | **Content import** (photos, descriptions) from existing listings? | P0-ONB-03 | |
| 8 | Certification steps and typical time for a new PMS to go live? | Project timeline | |
