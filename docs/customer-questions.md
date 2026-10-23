# Customer Interview Questions

Questions for property owners we interview before and during building. The same guide works for
every owner; property-specific notes go in the **Answers** section at the end of each interview.
Record any resulting decision in the [decision log](./decision-log.md).

## How to run the interview
- **Listen first, show later.** Ask about how they work today before describing our product.
  Once they've heard our idea, people tend to say what they think we want to hear.
- **Ask about what they actually did, not what they would do.** "What happened the last time you
  had a double booking?" gives a truer answer than "Would you like to avoid double bookings?"
- **Ask for numbers and examples.** "How many bookings last month?" is more useful than "a lot".
- **Let them complain.** Frustrations are where our product earns its price.
- Take 45–60 minutes; skip questions that don't apply.

---

## A. The property

### 1. Basic setup
**Ask:**
- How many rooms or cabanas do you have? Are any identical, or is each one different?
- Who runs the place day to day? How many staff, doing what?
- Which months are busy and which are quiet?

**Why we're asking:**
- Decides how we set the property up in our system: identical rooms share one **room type**, unique
  cabanas each get their own. That also decides which Channex price applies to them
  (~$7 per property, or ~$0.50 per unit for holiday-rental listings).
- Tells us who will actually use the app (owner, manager, cleaners), and what roles we need.
- Quiet months are the best time to switch a property onto a new system.

**Answer:**

## B. Bookings and booking sites

### 2. Where bookings come from
**Ask:**
- Which websites do you list on (Booking.com, Agoda, Airbnb, Expedia, others)?
- Roughly what share of bookings comes from each, and how many come directly (phone, WhatsApp,
  Facebook, walk-ins, your own website)?
- How much commission do you pay the booking sites?

**Why we're asking:**
- Confirms which channel connections matter most in Phase 0, and whether Agoda is as important as we think.
- A high commission bill plus few direct bookings is exactly the problem our website and booking
  engine solve. It's the strongest reason for them to pay us.

**Answer:**

### 3. How they keep track today
**Ask:**
- How do you keep track of bookings today: a notebook, a spreadsheet, a calendar, the booking sites'
  apps, other software?
- When a booking comes in on one site, how do you stop the same cabana being sold on another?
- Have you ever had a **double booking**? What happened, and what did it cost you?

**Why we're asking:**
- Our main promise is "no double bookings". Real stories tell us how painful the problem is and how
  much they'd value the fix.
- What they use today tells us what we must import when they switch, and what they'll compare us against.

**Answer:**

## C. Prices and money

### 4. Prices and currency
**Ask:**
- Do you set prices in **USD, LKR or both**? Different prices for foreigners and locals?
- Do prices change by season, weekends, or holidays?
- Do you give discounts (long stays, returning guests, early booking)?

**Why we're asking:**
- USD vs LKR is one of our biggest open questions. If owners price in both, we need two currencies
  from Phase 0.
- Separate foreigner and local prices are common in Sri Lanka and not yet in our spec.
- Season and discount rules decide how much price control Phase 0 needs.

**Answer:**

### 5. How guests pay
**Ask:**
- How do guests pay you: cash, card machine, bank transfer, online, through the booking site?
- Do you take a **deposit**? What happens if a guest cancels late or doesn't show up?
- Have you lost money to no-shows or unpaid bills?

**Why we're asking:**
- Shows whether online payments and automatic charging of saved cards (our PayHere plan) solve a real
  problem for them.
- No-show losses are a concrete number we can point to when explaining the value of saved cards.
- Tells us which payment methods Phase 0 must record (cash and bank transfer are already included).

**Answer:**

### 6. Taxes and invoices
**Ask:**
- Which taxes and charges do you add to the bill (VAT, service charge, others)?
- Are you registered with the **SLTDA**? Do guests ask for a formal invoice?
- Does an accountant handle your books? What do they need from you each month?

**Why we're asking:**
- We still need to confirm Sri Lanka's hotel tax stack. Owners show us what's done in practice
  (to be checked with an accountant).
- Small properties may not be VAT-registered or SLTDA-registered. Our system must handle both cases.
- What the accountant needs decides our reports and exports.

**Answer:**

## D. Guests and their stay

### 7. Guest details and police reporting
**Ask:**
- What guest details do you collect at arrival (passport copy, forms)?
- Do you have to report guests to the police or another authority? How, and how often?

**Why we're asking:**
- Online check-in and guest registration are in Phase 0, but we haven't confirmed the current legal
  rules. How owners do it today shows the real requirement.
- If reporting is a paper or manual process, doing it automatically is a strong time-saver to sell.
- Passport data is the most sensitive data we'll hold, so we need to know exactly what's collected.

**Answer:**

### 8. Arrival and check-in
**Ask:**
- How does arrival work: does someone meet guests, hand over keys, explain the place?
- What happens when guests arrive late at night?
- What do guests usually ask before they arrive (directions, transport, meals)?

**Why we're asking:**
- Decides how useful our automatic pre-arrival messages and online check-in are.
- Late-night arrivals are where self check-in and access codes help most (smart locks are Phase 1).
- Common guest questions become ready-made message templates.

**Answer:**

### 9. Guest messages
**Ask:**
- How do you talk to guests before and during the stay: WhatsApp, email, the booking sites' chat, phone?
- How much time a day does this take?

**Why we're asking:**
- Phase 0 sends automatic emails. If guests in Sri Lanka mainly use **WhatsApp**, we may need WhatsApp
  messages earlier than planned (currently a "stretch" item).
- Time spent on messages is time our automation saves, another number to justify the price.

**Answer:**

## E. Operations

### 10. Cleaning and maintenance
**Ask:**
- How do cleaners know which cabanas to clean each day?
- How do you find out about something broken, and how is it fixed?

**Why we're asking:**
- Tests whether our simple Phase 0 cleaning list (shared with cleaners by link, no login) fits how
  they actually work.
- Maintenance tracking is Phase 1. If it's a big pain point, we may move a simple version earlier.

**Answer:**

### 11. Meals
**Ask:**
- How are meals sold: **included in the room price** (bed & breakfast, half board, full board),
  **per meal**, or **per person per day**?
- Do guests order in advance or on the day? How are meals added to the bill?
- Do you serve people who aren't staying, or run anything like a restaurant?

**Why we're asking:**
- Phase 0 can sell meals as extras, but doesn't yet have **meal-plan prices** (B&B, half board, full
  board), which are common in Sri Lanka. The answer decides whether they move into Phase 0.
- How meals reach the bill tells us whether simple extras are enough for now.
- Whether they run a restaurant model is information we're collecting for the later F&B phase.

**Answer:**

### 12. Day-out packages
**Ask:**
- What's included in a day-out package (pool, nature walk, meals, a cabana for the day)?
- How are day-outs booked and paid for? How many per week, and how far in advance?
- Do day-out guests use a cabana that could otherwise be sold to overnight guests?

**Why we're asking:**
- Phase 0 only handles overnight stays. If day-outs are a meaningful share of revenue, we need a
  simple "day package" product before this property can run fully on our system.
- If day-out guests use cabanas, we must block those cabanas for overnight sales the same day,
  or we'll create double bookings ourselves.

**Answer:**

## F. Technology

### 13. Internet and devices
**Ask:**
- How reliable is the internet at the property? Mobile data, Wi-Fi, both? How often does it drop,
  and for how long?
- Which phones, tablets or computers do the owner and staff use?

**Why we're asking:**
- Phase 0 has no offline mode. If the connection drops often, the app must keep today's bookings and
  cleaning list available without internet, and the owner must still receive new booking alerts.
- Phone models decide how light and simple the app must be (we're designing for cheap Android phones).

**Answer:**

### 14. Biggest frustrations
**Ask:**
- What's the most annoying or time-consuming part of running the property?
- If you could fix one thing tomorrow, what would it be?

**Why we're asking:** the honest answer to this often matters more than everything else. If their
top frustration isn't something Phase 0 solves, we need to know before building.

**Answer:**

## G. Our product (ask last)

### 15. Reaction to the idea and prototype
**Ask:** (after briefly describing the product or showing the prototype)
- What's useful? What's confusing? What's missing?
- Is there anything here you'd never use?

**Why we're asking:** tests the Phase 0 spec against a real owner before we spend months building
it. Watching where they get confused is as useful as what they say.

**Answer:**

### 16. Price
**Ask:**
- What do you pay today for software, booking-site commission and card fees?
- Would you pay a monthly fee for this? What would feel fair? At what price would it be too expensive?

**Why we're asking:**
- Our Starter price (roughly US$15–30/month) is a guess. Real answers from owners are how we test it.
- Comparing with what they already pay frames our price: small next to OTA commission.

**Answer:**

### 17. Beta participation
**Ask:**
- Would you try an early version for free or at a discount, and give us feedback?
- Who else do you know who runs a similar property?

**Why we're asking:**
- We need 10–20 beta properties. Owners who help shape the product often become the most loyal customers.
- Owner-to-owner introductions are likely the best way to find more properties in Sri Lanka.

**Answer:**
