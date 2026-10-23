# Advisor Questions

Questions for people advising the founder. Questions are added **only after the founder confirms
them**. Fill in **Answer** after each conversation and record any resulting decision in the
[decision log](./decision-log.md).

---

## Lahiru

Built and sold a company built with Claude Code: an HR/payroll app,
[LunchBox HR](https://www.lunchboxhr.com/). Introduced the founder to security practices like VAPT
and to what Claude Code can do.

### 1. Independent VAPT
**Ask:**
- Did you do a VAPT with an independent party, for LunchBox, Rooster or any other company?
- What were the circumstances? (Who asked for it: a customer, a partner, an investor, the buyer when you sold?)
- How much did it cost?
- How important do you think it is, in the Sri Lankan context and in the hotel industry context?

**Why we're asking:**
- Our plan includes an independent VAPT before the Phase 0 beta, budgeted at roughly LKR 0.8–1.7 million
  for a local fixed-scope test. That figure comes from published price lists, not real experience.
  If Lahiru did one and shares what he paid, we can compare it with that budget.
- *Who asked for it* tells us when we really need one. If customers or partners demanded it, we know
  it's unavoidable. If the buyer asked for it when he sold, it matters for the company's eventual value too.
- An HR/payroll app handles sensitive personal and salary data, similar in sensitivity to our guest
  passport details and payments. His view on how much security testing Sri Lankan customers actually
  expect is directly relevant.
- If he names a firm he used, that gives us a trusted starting point for quotes.

**Answer:**

### 2. How he worked with Claude Code
**Ask:**
- How big was your team, and who did what?
- What did you let Claude Code do on its own, and what did you always have a person check?
- What went wrong or surprised you, and what would you do differently?

**Why we're asking:**
- We plan to build this product largely with Claude Code. Lahiru has done that all the way to a sale,
  so his experience is the closest thing we have to a proven playbook.
- The split between "Claude does it" and "a person checks it" decides how many engineers we need,
  what they spend their time on, and where mistakes are most likely to slip through.
- His mistakes are cheaper to learn from than our own.

**Answer:**

### 3. What the buyer checked when he sold
**Ask:**
- When you sold the company, what did the buyer examine (security, code quality, tests,
  documentation, data protection, contracts)?
- Did anything cause problems or lower the price? Is there anything you wish you'd prepared earlier?

**Why we're asking:**
- Before buying a software company (or investing in one), buyers and investors check it closely,
  a process called **due diligence**.
- Knowing what they look at lets us build those things in from the start, which is far cheaper than
  fixing them later. It also makes the company easier to raise money on or sell.

**Answer:**

### 4. Sri Lanka's data protection law (PDPA)
**Ask:**
- How did you handle Sri Lanka's **Personal Data Protection Act (No. 9 of 2022)** with employee and
  salary data?
- Did you use a lawyer? What did it require in practice (consent, data storage, a data protection
  officer, breach reporting)?

**Why we're asking:**
- We'll hold guests' passport and ID details, contact details and payment records, which is highly
  sensitive personal data, much like his salary data.
- We haven't verified the law's current requirements or when they apply. His practical experience
  tells us what really matters and whether we need a lawyer before the beta.

**Answer:**

### 5. Payments in Sri Lanka
**Ask:**
- Which payment gateway did you use for customer subscriptions: PayHere or another?
- What went well or badly? Any problems with foreign cards, automatic monthly charges, payouts or support?

**Why we're asking:**
- We plan PayHere both for hotels' guest payments and for our own subscription billing, but we
  haven't confirmed it can do everything we need (see [partner-questions.md](./partner-questions.md)).
- Real experience from someone who charged customers monthly in Sri Lanka will tell us what to watch
  out for before we commit.

**Answer:**

### 6. Hosting and where data is stored
**Ask:**
- Which cloud provider did you host on, and why?
- Did any Sri Lankan customer, or the law, require data to be stored **inside Sri Lanka**?
- What did hosting cost as you grew?

**Why we're asking:**
- Where data is stored (called **data residency**) can be a legal or customer requirement. If larger
  hotels or the government expect Sri Lankan data to stay in the country, that limits which cloud
  providers we can use.
- Hosting is a monthly cost on every customer, so his real costs help us check our pricing.

**Answer:**
