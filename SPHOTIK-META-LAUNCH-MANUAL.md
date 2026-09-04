# Sphotik Meta Launch Operating Manual

**Version 1.0 · Prepared 4 September 2026 · For internal use by the founder**

---

## How to read this document

Every claim in this manual is tagged so you know how much weight to put on it.

| Tag | Meaning |
|---|---|
| **[FACT]** | Verifiable against Meta official documentation or your own codebase. Source cited. |
| **[PLATFORM]** | Current Meta platform behaviour as of Sep 2026. Meta changes UI often. Verify in your own account before acting. |
| **[SECONDARY]** | From practitioner or third-party sources, not Meta. Treat as directional. |
| **[ASSUMPTION]** | Something I am assuming about your business that you should confirm or correct. |
| **[ESTIMATE]** | A number I calculated from assumptions. It will be wrong. It is there to give you a range, not a target. |
| **[RECOMMENDATION]** | My opinion. Argued, not asserted. You can overrule it. |

**Currency assumption used throughout: USD 1 = BDT 122.** [ASSUMPTION] Adjust if the rate has moved.

**Codebase state:** This manual was written after reading your actual repository at `Sphotik-main`. Where I say something is missing, I checked. `node_modules` was not installed at the time of writing, so the Next.js 16 code snippets below follow standard App Router patterns and should be verified against `node_modules/next/dist/docs/` after you run `npm install`.

---

## The single most important thing in this document

Before anything else, read this.

**You currently have no way to know who paid you.**

Your checkout at `src/app/checkout/page.tsx` collects a name, phone, email, area, payment method, and a transaction ID. It then generates an order number in the browser with `Date.now()`, writes a membership object to `localStorage`, and fires a Meta `Purchase` event. There is no API call. There is no database. There is no email to you. [FACT: verified in `src/app/checkout/page.tsx` lines 244-252 and 720-731; the only API route in the project is `src/app/api/return-requests/route.ts`.]

If you launch ads tomorrow and thirty people complete checkout, you will have thirty `Purchase` events in Meta Events Manager and zero records of who those people are. You cannot activate their memberships. You cannot ship them books. You cannot refund them. The money will arrive in your bKash account with no way to match it to a person.

**Second problem:** the `Purchase` event fires the moment the user types any string into the transaction ID box and clicks confirm. It is not verified. Anyone can type "abc" and generate a `Purchase`. You will be optimising your ad spend toward people who are good at typing, not people who are good at paying.

Fixing both of these is Task Zero. It is covered in Part 2 Step 0 and Part 4. Do not spend a single taka on ads until it is done.

---
---

# PART 1: Pre-launch business audit

I am going to disagree with you in several places. That is what you asked for.

## 1.1 Is ৳900 / ৳1,600 a good founding offer?

**Partly.** The ৳900 six-month price is well judged. The ৳1,600 twelve-month price is not.

The six-month price works because of the comparison a Bangladeshi reader will actually make in their head. A new Bangla paperback runs roughly ৳300 to ৳500; a Bangla translation of a foreign novel runs ৳400 to ৳700; an English import runs ৳700 upward. [ASSUMPTION: based on typical Bangladeshi retail cover pricing. Confirm against Rokomari and Boi Bazar current listings.] So ৳900 for six months reads as "less than two books, for six months of books." That is a clean, defensible mental arithmetic and it is the strongest thing about your pricing.

The twelve-month price is the problem. Two six-month memberships cost ৳1,800. Your annual is ৳1,600. That is an 11% discount for doubling the commitment period, to a brand that did not exist last month, in a country where subscription services routinely disappear.

**11% is not enough to buy trust.** Nobody is going to reason "I will lock in twelve months with an unproven library to save one hundred and eighty taka." What will actually happen is that almost everyone picks the six-month, and your twelve-month tier exists only as a decoy that makes the six-month look sensible. That is not a disaster (decoys are useful), but you are leaving cash flow on the table, and cash flow is what buys your next fifty books.

There is a second, less obvious problem. **Your real offer is not ৳900.** Your real offer is:

| Line | Amount |
|---|---|
| Membership (6 months, founding) | ৳900 |
| Security deposit (entry tier) | ৳500 |
| **Cash out of pocket on day one** | **৳1,400** |
| Delivery, first shipment (outbound) | ~৳70 to ৳120 [ESTIMATE] |
| Delivery, first return | ~৳70 to ৳120 [ESTIMATE] |
| **Realistic first-month cash** | **~৳1,550 to ৳1,640** |

And over a full six-month membership with monthly borrowing:

| Line | Amount |
|---|---|
| Membership | ৳900 |
| Delivery, 6 outbound + 6 return trips at ~৳90 | ~৳1,080 [ESTIMATE] |
| **Total six-month cost, excluding refundable deposit** | **~৳1,980** |

Delivery costs more than membership. That is the most important number in this audit and it is not on your website.

If a member borrows two books per swap, twelve books over six months costs them ৳1,980, or ৳165 per book read. That is still good value against ৳400 to buy. But it is not the "৳900 for six months" story your ad will tell, and the gap between the advertised number and the lived number is where refund requests and one-star reviews come from.

## 1.2 Is ৳1,200 / ৳2,000 a good regular price?

**The ৳1,200 is fine. The ৳2,000 has the same structural flaw, worse.** Two six-month regulars cost ৳2,400; the annual is ৳2,000; that is a 17% discount, which is better than the founding tier's 11%, but it means your founding discount on the annual (20% off) is smaller than your founding discount on the six-month (25% off). You are discounting the commitment you want less and the one you want more, backwards.

More importantly: **you have not earned the right to publish a regular price yet.** Showing ৳1,200 struck through next to ৳900 only works as a persuasion device if the ৳1,200 is credible. For a brand with zero history, a struck-through price that has never been charged to anyone reads as a marketing trick, and Bangladeshi consumers are extremely well trained to spot fake anchoring after a decade of e-commerce "৳5,000 ৳999" ads. [RECOMMENDATION] Keep the regular price on the site, but change the framing from "was ৳1,200" to "the price after launch will be ৳1,200." That is a forward-looking promise rather than a backward-looking claim, and it is true. Your `src/data/content.ts` already has `regularPrice` as a separate field, so this is a copy change, not a data change.

## 1.3 Is the deposit structure psychologically attractive?

**The concept is excellent. The execution is over-engineered.**

What is good: a refundable deposit is genuinely clever positioning. It converts what would otherwise feel like a large payment into something the customer mentally files as "my money, parked." It also does real work: it is collateral, it self-selects for people who intend to return books, and it scales borrowing capacity honestly.

What is bad: **you have coupled three different variables to one dial.** Your deposit tiers control (a) how many books, (b) total replacement value cap, and (c) loan duration.

| Deposit | Books | Value cap | Days |
|---|---|---|---|
| ৳500 | 2 | ৳700 | 10 |
| ৳1,000 | 4 | ৳1,400 | 14 |
| ৳1,500 | 6 | ৳2,000 | 21 |

A cold visitor from a Facebook ad has about eight seconds of patience for a pricing table. You are asking them to hold three dimensions in their head simultaneously, on top of a business model they have never encountered, on top of a separate membership fee, on top of separately-charged delivery. That is four independent concepts before they reach a checkout button.

The days coupling is the worst of the three, for two reasons. First, it does no economic work. A ৳500 member holding two books for 21 days does not cost you materially more than one holding them for 10 days; your real constraint is copies-in-circulation, which the book-count cap already handles. Second, it reads as punitive. "Pay less, get rushed" is a bad feeling to design into your entry tier, and the entry tier is where 70% of your first members will land. [ESTIMATE]

The value cap is the one that will actually bite operationally. ৳700 across two books means an average cover price of ৳350. Look at your own catalogue: a lot of translated fiction and most English titles are above ৳350. So a ৳500 member will repeatedly try to borrow the books your ads made them want, and be told no. That is a first-week churn machine.

To be fair to your design: the cap is economically correct. If you buy at roughly 30% off cover, ৳700 of cover price costs you about ৳490, and you are holding ৳500. You are exactly collateralised. Do not raise the cap to ৳1,400 and leave the deposit at ৳500; that would leave you uncovered. The fix is presentational and operational, not economic.

## 1.4 Could the deposit create conversion friction?

**Yes, and it is your single largest conversion risk after comprehension.**

The mechanism is not that ৳500 is a lot of money. The mechanism is that **the number the ad promises and the number the checkout demands are different**, and that gap is experienced as a small betrayal. Your ad will say ৳900. Your checkout will say ৳1,400. In behavioural terms this is the same shape as a hidden shipping fee, which is the single most studied cause of cart abandonment.

There is a second, harder mechanism specific to Bangladesh: **a refundable deposit to a stranger on the internet is a trust ask, not a price ask.** The customer is not asking "can I afford ৳500?" They are asking "will I ever see this ৳500 again?" And your current cancellation policy makes that question harder to answer well: if a member cancels early, you keep the membership fee entirely, and you hold their deposit until the original period ends. From the outside, that reads as "they keep your money and hold your money."

You cannot remove the deposit; it is load-bearing. But you can and must make it feel safe. See MUST CHANGE below.

## 1.5 Is membership-library positioning stronger than "book rental"?

**Strategically yes, tactically it needs a translation layer.**

"Membership library" is the right internal model. It supports no-per-book-fee, it supports recurring revenue, it supports a community narrative, and it correctly signals that you are not a shop.

But two words in it fight you on cold traffic in Bangladesh:

- **"Library"** primarily evokes a physical building, usually free, often a study room, frequently associated with exam preparation rather than pleasure reading. A cold viewer hearing "library" may assume they need to go somewhere, or that it is a study space rental (a large and growing category in Dhaka), or that it is free.
- **"Membership"** signals recurring commitment and, in a market saturated with "membership" MLM and course-selling schemes, carries some negative charge.

[RECOMMENDATION] Keep "membership library" as the positioning line and the About page identity. But in the ad and in the first six words of the landing page, lead with the **job**, not the **category**: something on the order of "borrow books, read them, send them back, borrow again." Once the mechanism is understood, the category label becomes an asset rather than a hurdle. Your homepage subheadline already does this well ("Join, borrow books from our collection, read them at your own pace, then send them back and borrow again"). The problem is that it is the *sub*headline. Promote it.

## 1.6 Is "Read more. Own less." strong enough?

**As a brand signature, it is very good. As an ad headline, it may actively repel your best customer.**

The line is elegant, it is memorable, it has rhythm, and it earns its place in your footer and on your packaging. Keep it there.

The problem is the second half. "Own less" is a minimalism value imported from high-consumption Western markets, where owning things is the default and shedding them is the aspiration. In Bangladesh, and specifically among the readers you want, **owning books is the aspiration.** A personal bookshelf is a status object and an identity marker. The person most likely to pay you ৳1,400 is precisely the person who has spent years building a shelf and photographing it. Telling them the goal is to own less is telling them their existing behaviour was a mistake.

There is also a business risk in it: "own less" positions you against ownership, which means positioning against the bookshops. You do not want to be anti-bookshop. You want to be the thing people use for the eighty books they want to read once, so they can afford to buy the twenty they want to keep.

[RECOMMENDATION] Never use "Read more. Own less." as a paid ad headline or hook. Use it as the sign-off frame in Video C only, where the brand context has already been built. For direct response, the honest and much stronger version of the same idea is about **volume**: "read four books a month for the price of one."

## 1.7 Does delivery create too much friction?

**Yes. This is your biggest operational and honesty problem, and it is fixable cheaply.**

Right now your model is: delivery is quoted per shipment, outbound and return are separate trips, and the member arranges and pays for the return. Three things go wrong with that.

**First, it is unpriceable to the customer.** Your `/delivery` page explains the policy but does not give a number. A cold visitor being asked for ৳1,400 cannot compute their true cost. Unpriced ongoing costs are the number one killer of subscription conversion, because the customer's brain fills the blank with the worst case.

**Second, "the member arranges the return" is a very heavy ask.** In practice it means the member must open a courier app or call a courier, package a book, and wait at home. Every single one of those steps is a place where a member decides to keep the book "just one more week," and every week of delay is a copy out of circulation and a support conversation for you.

**Third, it doubles the number of financial interactions.** Every borrow cycle involves two separate payment moments. Over six months that is twelve small payments. Twelve chances to feel nickel-and-dimed.

[RECOMMENDATION] Replace per-trip variable pricing with **one published flat swap fee that covers both legs and both the pickup arrangement**. Something like ৳120 per swap, all-in, and you arrange the return pickup. Whether ৳120 is the right number depends on your negotiated courier rate; the point is that it is **one number, published, covering everything**. Predictability beats accuracy at this stage. If you lose ৳20 per swap on some routes, that is the cheapest market research you will ever buy, and you can adjust in V2 with real data.

This single change turns your offer from "৳900 plus a deposit plus unknown ongoing costs" into "৳900 plus a refundable ৳500, and ৳120 every time you swap books." That second sentence is something a person can decide about in eight seconds.

## 1.8 Summary of recommendations

### MUST CHANGE BEFORE LAUNCH

These are blockers. Do not spend money until they are done.

| # | Change | Why | Effort |
|---|---|---|---|
| **M1** | **Persist every checkout to a server-side record you control** (Google Sheet via Apps Script is fine). Capture name, phone, email, area, plan, deposit tier, amount, payment method, transaction ID, timestamp, `fbp`, `fbc`, and all UTMs. | You currently have zero record of who paid. You physically cannot fulfil an order. See Part 2 Step 0. | Half a day |
| **M2** | **Replace the placeholder payment numbers.** `src/data/checkout.ts` contains `01XXXXXXXXX` for bKash, Nagad, and Rocket, with the comment "Replace with the live merchant numbers before the first paid campaign." | Self-explanatory. | 10 minutes |
| **M3** | **Stop firing browser `Purchase` on unverified payment.** Fire a custom `PaymentClaimed` from the browser; fire the standard `Purchase` from your server only after you manually confirm the money arrived. | Your ad optimisation currently targets "people who type things," not "people who pay." See Part 4. | Half a day |
| **M4** | **Persist checkout state across app switching.** The user must leave your site, open bKash, send money, and come back. Your checkout state lives in React `useState` and will be destroyed if the in-app browser reloads. | This is a silent, invisible conversion killer specific to your payment method. See Part 22. | 1 to 2 hours |
| **M5** | **Publish one flat, all-in swap delivery fee** and take responsibility for arranging the return pickup. | See 1.7. | Copy change plus one courier conversation |
| **M6** | **Add a Founding Member Guarantee: full refund of the membership fee if you cancel within 14 days and have no books out (or have returned them undamaged). Delivery already paid is not refunded.** | Your current terms are the harshest possible for a brand nobody knows: fee forfeited, deposit held until period end. You will not be able to tell whether a failed test means "bad offer" or "no trust." This guarantee removes the trust variable from the experiment for almost no real risk. | Copy change plus a line in `/membership-rules` |
| **M7** | **Install the Meta Pixel and fire `PageView` on client-side route changes.** Your `src/lib/analytics.ts` calls `window.fbq` only if it already exists, and nothing ever loads it. `PageView` is defined in the event union but never called anywhere. | You have a tracking layer with nothing connected to it. | 1 hour |
| **M8** | **Put the deposit and the "pay today" number above the fold on the landing page.** | Removes the ad-to-checkout price gap. See Part 8. | Copy change |
| **M9** | **Change the checkout defaults from 12-month + ৳1,000 deposit to 6-month + ৳500 deposit.** `defaultDepositIdx = 1` in `src/data/content.ts` and `membershipDurations[1]` in `src/app/checkout/page.tsx`. | Your checkout currently opens at a pay-today of **৳2,600** while your ad promises ৳900. This is a two-line fix and probably the highest-value change in this table. See Part 21.3. | 5 minutes |

### SHOULD TEST

Run these as experiments, not as assumptions.

- **Twelve-month price.** ৳1,600 vs ৳1,500 (see Part 21). My recommendation is ৳1,500 but the evidence is weak either way; this is a genuine test.
- **Flat 14-day loan period for all tiers** vs current 10/14/21 coupling. My instinct says flat wins on comprehension; I would not bet money on it.
- **Default deposit tier**, *after* you have fixed M9. ৳500 default (lowest first payment, my recommendation) vs ৳1,000 default (higher cash per member). This is a legitimate test once your default is no longer silently tripling your advertised price.
- **Landing headline:** job-led ("borrow, read, send back, borrow again") vs category-led ("a membership library") vs economic ("four books a month for the price of one").
- **Making email optional** and phone the only required contact field. Many Bangladeshi buyers do not use email actively; requiring a valid email address is a real form-abandonment cost. Your `DetailsStep` currently hard-requires a valid email.

### CAN WAIT

Do not spend time on these before launch.

- Full Conversions API with real-time server events. The minimal delayed-Purchase-from-verification version in Part 4 is enough and is strictly better for your case.
- Meta business verification. Useful for spending limits later; not blocking at USD 250. [PLATFORM: Meta scopes business verification as "required to access certain advertising, developer, billing and spending features," not as a prerequisite to advertise. Source: Meta Business Help Centre, "About business verification in Meta Business Suite."]
- Lookalike audiences. You will not have enough seed data. See Part 11.
- Catalogue / Advantage+ catalogue ads for individual books.
- Bangladesh Post or Speed Post testing.
- Instagram Shopping, WhatsApp Business API, Messenger automation.
- A second city.
- Any paid analytics tool.

---
---

# PART 2: Zero-to-launch checklist

Sequential. Do not skip ahead. Estimated total: 3 to 4 working days if you are focused.

---

## STEP 0: Fix the order record and the Purchase event (do this first)

**This step is not on your original list. It is the most important one.**

### 0a. Create the order recorder

You are on Cloudflare Pages with a `public/_redirects` file containing `/* /index.html 200`, which suggests a static output. [FACT: verified in repo.] That means Next.js API routes may not run. Rather than solve that now, use the zero-infrastructure option.

**Where to go:** [sheets.google.com](https://sheets.google.com)

1. Create a spreadsheet named `Sphotik Orders`.
2. Row 1 headers, exactly these, in this order:
   `timestamp | order_no | name | phone | email | area | plan_id | membership_fee | deposit_tier | deposit_amount | pay_today | payment_method | txn_id | fbp | fbc | utm_source | utm_medium | utm_campaign | utm_content | utm_term | fbclid | status | verified_at | capi_sent`
3. `Extensions` > `Apps Script`.
4. Paste a `doPost(e)` handler that appends `JSON.parse(e.postData.contents)` as a row.
5. `Deploy` > `New deployment` > type `Web app` > Execute as `Me` > Who has access `Anyone`.
6. Copy the deployment URL. Put it in `.env.local` as `NEXT_PUBLIC_ORDER_ENDPOINT`.

**What success looks like:** you `POST` a test JSON payload with `curl` and a row appears in the sheet within two seconds.

**Common errors:**
- Apps Script web apps do not return CORS headers on `POST`. Fix: send the request from the browser with `fetch(url, { method: "POST", mode: "no-cors", body: JSON.stringify(payload) })`. You will not be able to read the response, which is fine, because you will also `await` a local success state. Belt and braces: also send yourself the same payload by email from inside the Apps Script using `MailApp.sendEmail`.
- Forgetting to redeploy after editing the script. Apps Script serves the last *deployed* version, not the last saved one. Every edit needs `Deploy` > `Manage deployments` > edit > `New version`.

### 0b. Capture the Meta click identifiers at checkout

You need `_fbp` (Meta's browser cookie, set by the Pixel) and `_fbc` (derived from the `fbclid` URL parameter) stored *with the order*, so that when you later send the server-side Purchase, Meta can match it back to the ad click.

Add to `src/lib/analytics.ts`:

```ts
function readCookie(name: string): string | undefined {
  if (typeof document === "undefined") return undefined
  const match = document.cookie.match(new RegExp("(^| )" + name + "=([^;]+)"))
  return match ? decodeURIComponent(match[2]) : undefined
}

/** Meta click identifiers, stored with the order so the server can match it later. */
export function getMetaIdentifiers() {
  const fbp = readCookie("_fbp")
  let fbc = readCookie("_fbc")
  if (!fbc && typeof window !== "undefined") {
    const fbclid = new URLSearchParams(window.location.search).get("fbclid")
    // Meta's documented _fbc format: fb.{subdomainIndex}.{creationTime}.{fbclid}
    if (fbclid) fbc = `fb.1.${Date.now()}.${fbclid}`
  }
  return { fbp, fbc }
}
```

[PLATFORM: the `fb.{subdomainIndex}.{creationTime}.{fbclid}` format for `_fbc` is documented by Meta under "Conversions API > Parameters > fbc". Verify the current format in Meta's developer docs before shipping.]

Because your checkout is a client-side flow and the `fbclid` is only present on the *first* landing URL, capture these on first page load and stash them in `sessionStorage`, then read from `sessionStorage` at checkout.

### 0c. Split the Purchase event

In `src/app/checkout/page.tsx`, the `Confirmed` component currently does this:

```tsx
    track("Purchase", {
      currency: CURRENCY,
      value: payToday,
      membership_value: plan.price,
      deposit_value: tier.deposit,
      duration_id: plan.id,
    })
```

Replace with a custom event that does not claim a verified sale, and POST the order:

```tsx
    // Browser-side: a *claim* of payment, not a confirmed one.
    track("PaymentClaimed", {
      currency: CURRENCY,
      value: plan.price,          // membership fee only, see Part 4
      deposit_value: tier.deposit,
      cash_collected: payToday,
      plan: plan.id,
      order_no: orderNumber ?? "",
    })
    void submitOrder({ /* ...full payload... */ })
```

Add `"PaymentClaimed"` to the `AnalyticsEvent` union. Do **not** add it to `STANDARD_META_EVENTS`, so it is sent via `trackCustom`.

**What success looks like:** you complete a test checkout, a row appears in your Google Sheet with all UTMs and `fbp`/`fbc` populated, and Meta Test Events shows a custom `PaymentClaimed` event and no `Purchase`.

---

## STEP 1: Prepare business assets

You need these before you touch Meta. Gather them into one folder.

| Asset | Spec | Notes |
|---|---|---|
| Logo, square | 1080×1080 PNG, transparent background | Also export a 512×512 for the Page profile |
| Logo, wordmark | 1200×400 PNG | For cover images |
| Cover image | 1640×856 (Facebook Page cover; safe area 1090×630 centred) | [PLATFORM: Facebook renders Page covers at different crops on desktop and mobile. Keep all text inside the central 1090×630.] |
| OG image | 1200×630 JPG | You already reference `/og-image.jpg` in `src/app/layout.tsx`. Confirm the file exists in `public/`. |
| Business email | `hello@sphotik.com.bd` | Your `/contact` page already lists this. Confirm it actually receives mail. |
| Phone number | A real, answered number | Your `/contact` page currently shows the placeholder `+880 1700 000000`. Fix this. |
| Business address | A real address in Dhaka | Can be a home address. It must exist and be consistent everywhere. |
| Live domain on HTTPS | `sphotik.com` or `sphotik.com.bd` | **Decide one and only one.** Your `.env.example` says `sphotik.com`, your contact email says `sphotik.com.bd`. Pick one, redirect the other. Mixed domains break domain verification and confuse trust. |
| TIN, and BIN if you have one | 12-digit e-TIN; 13-digit BIN | See the tax note in Step 6. Get the e-TIN at minimum; it is free and takes an afternoon at [etaxnbr.gov.bd](https://etaxnbr.gov.bd). |
| Personal Facebook profile in good standing | Not new, not previously restricted | Meta will judge your ad account partly by the profile that created it. |

**Common error:** creating the Page from a brand-new Facebook profile made specifically for the business. New profiles get flagged. Use your real, aged personal profile. It will not be publicly shown as the Page owner unless you enable Page Transparency detail.

---

## STEP 2: Create the Facebook Page

**Where to go:** [facebook.com/pages/create](https://facebook.com/pages/create) while logged into your personal profile.

**What to enter:**

| Field | Value |
|---|---|
| Page name | `Sphotik` |
| Category | `Library` (primary). Add `Book Store` as secondary if offered. |
| Bio | See Part 6 for exact copy. |

Then immediately:
1. Set the profile picture (square logo) and cover image.
2. `Settings` > `Page setup` > set the username to `@sphotik` (or `@sphotikbd` if taken). This gives you `facebook.com/sphotik`.
3. Add website, email, phone, and Dhaka as the location.
4. Set the action button to `Learn more` pointing at your homepage. Do **not** use `Shop now` (implies buying individual books) or `Sign up` (implies free).

**What success looks like:** `facebook.com/sphotik` loads, shows a logo, a cover, a bio, a website link, and one pinned post.

**Common errors:**
- Choosing a category of `Product/Service` or `Brand`. `Library` is more credible and more specific, and category affects how Meta interprets your ads.
- Leaving the Page with zero posts at launch. A Page with an ad running and no organic posts is a strong scam signal to a cautious Bangladeshi buyer, who **will** click your Page name before paying you ৳1,400. Post at least four things before your first ad goes live.

**How to verify:** open `facebook.com/sphotik` in a private browsing window while logged out. That is what your cold traffic sees.

---

## STEP 3: Create the Meta Business Portfolio

[PLATFORM] "Business Manager" is now called a **Business Portfolio**, managed inside Meta Business Suite. [FACT: Meta Business Help Centre, "Create a business portfolio in Meta Business Suite," help ID 1710077379203657.]

**Where to go:** [business.facebook.com](https://business.facebook.com), log in with your personal Facebook profile.

**What to click:** the dropdown below `Home` in the top left > `Create a business portfolio`.

**What to enter:**

| Field | Value |
|---|---|
| Business portfolio name | `Sphotik` |
| Your name | Your legal name |
| Business email | `hello@sphotik.com.bd` |

[FACT: Meta's documentation states the portfolio name "should match the public name of your business or organisation because it will be visible across Meta" and "cannot contain special characters."]

Then:
1. Confirm the business email from the link Meta sends. **Do not skip this.** Unconfirmed portfolios hit walls later.
2. `Settings` > `Business info` > fill in legal name, address, phone, website, and time zone. Set time zone to **Asia/Dhaka**. This cannot be changed later on the ad account and it determines when your "day" rolls over in reporting.
3. `Settings` > `Accounts` > `Pages` > `Add` > `Add an existing Page` > select `Sphotik`.

**What success looks like:** the portfolio exists, the email shows as confirmed, business info is complete, and the Page appears under Accounts > Pages.

**Common errors:**
- Creating multiple portfolios by accident while experimenting. You will end up with assets scattered across them and no way to merge. Create exactly one.
- Adding the Page via "Request access" instead of "Add an existing Page." Request access is for Pages you do not own.

---

## STEP 4: Connect Instagram

**Prerequisite:** create the Instagram account first, on the Instagram app, and switch it to a Business account (`Settings` > `Account type and tools` > `Switch to professional account` > `Business`).

**Where to go:** `business.facebook.com` > `Settings` > `Accounts` > `Instagram accounts` > `Add`.

Log in with the Instagram credentials. Then, still in Settings, click the connected Instagram account and **assign the Sphotik Page and the ad account to it**. Assignment is the step people forget; without it the Instagram account will not be selectable as an ad identity.

**What success looks like:** when you later build an ad, the "Instagram account" dropdown at the ad level shows `@sphotik.bd` rather than "Use Facebook Page."

**Common error:** connecting Instagram through the Instagram app's "Link Facebook Page" flow instead of through Business Settings. That creates a Page-level link, not a portfolio-level asset, and you get inconsistent behaviour in Ads Manager.

---

## STEP 5: Create the ad account

**Where to go:** `business.facebook.com` > `Settings` > `Accounts` > `Ad accounts` > `Add` > `Create a new ad account`.

**What to enter:**

| Field | Value | Warning |
|---|---|---|
| Ad account name | `Sphotik - BD - Ads 01` | |
| Time zone | `Asia/Dhaka (GMT+6)` | **Permanent. Cannot be changed.** |
| Currency | `BDT` | **Permanent. Cannot be changed.** See below. |

**Currency decision, and it matters:**

[SECONDARY, but well-corroborated] Meta has been rolling out bKash as a payment method for Bangladeshi advertisers during 2026. Multiple practitioner reports in August 2026 confirm the option appearing in Ads Manager, and the consistent condition reported is that **the ad account currency must be set to BDT**. Meta has made no official announcement, and availability is per-account, not universal.

[RECOMMENDATION] **Set the currency to BDT.** Reasons: it is the only path to bKash billing if that rollout reaches you; it removes exchange-rate noise from your CPM and CAC reporting; and your entire business, pricing, and event values are in BDT, so having the ad account in USD would force you to convert every number by hand. The cost is that your "USD 250" budget becomes a BDT number that drifts with the exchange rate. That is a smaller problem than paying in a currency you do not think in.

Convert once, at the top: **USD 250 ≈ ৳30,500.** Work in taka from there.

Then: `Settings` > `Accounts` > `Ad accounts` > select the account > `Add people` > add yourself with **Full control**. Also `Connected assets` > add the `Sphotik` Page.

**What success looks like:** Ads Manager loads with the account selected, showing BDT and Dhaka time.

**Common errors:**
- Creating the ad account from your personal profile instead of inside the portfolio. Personal ad accounts cannot be transferred into a portfolio cleanly and lack partner access controls.
- [FACT] Meta limits all advertisers to **one ad account ID until they make a confirmed payment.** Source: Meta Business Help Centre, "How to create an ad account in Meta Ads Manager." So do not plan a multi-account structure. You get one.

---

## STEP 6: Add a payment method

**Where to go:** Ads Manager > hamburger menu > `Billing and payments` > `Payment settings` > `Add payment method`.

**Order of preference:**

1. **bKash**, if it appears. Verify the account country is Bangladesh and currency is BDT first. If it does not appear, your account is simply not in the rollout. Do not attempt workarounds and never share a bKash PIN or OTP with anyone claiming they can enable it.
2. **International dual-currency debit or credit card** (EBL, City Bank, BRAC, Standard Chartered all issue these against your passport endorsement).
3. A card belonging to someone you trust, added as a payment method. Legal and common in Bangladesh, but it ties your ad account's trust score to their card history.

### The tax situation, which will eat your budget

[FACT] Meta adds VAT at the local rate to your ad purchase if your "Sold To" address is in Bangladesh and you have **not** added your Business Identification Number (BIN) to the ad account. Source: Meta Business Help Centre, "Taxes on Meta Ads Placement," help ID 133076073434794. The Bangladesh standard VAT rate is 15%.

[SECONDARY] Practitioner reports from August 2026 describe the bKash payment path as adding roughly 17.25% on top (15% VAT plus a ~2.25% bKash fee) **with** a verified BIN, and roughly 32.25% **without** one, the extra 15% being an additional tax applied to unverified advertisers. I could not confirm the 32.25% figure against an official Meta or NBR source. Treat it as a warning, not a certainty, and check your own first invoice.

**What this means for your plan:**

| Scenario | You pay | Roughly reaches the auction |
|---|---|---|
| Card, no BIN | ৳30,500 | ~৳26,500 |
| bKash, verified BIN | ৳30,500 | ~৳26,000 |
| bKash, no BIN | ৳30,500 | ~৳23,000 |

[ESTIMATE] **Plan your campaign on ~৳26,000 (about USD 213) of delivered ad spend, not ৳30,500.** Every budget number in Part 15 is delivered spend.

**Action:** go to `Business Settings` > `Billing` (or `Payment settings` > `Business info`) and add your TIN, and your BIN if you have one. This is a fifteen-minute task that may be worth ৳4,500 to you.

### New account spending limit

[SECONDARY] New ad accounts receive an algorithmic daily spending cap, commonly reported in the USD 25 to 50 per day range, which rises automatically with clean payment history. It is not published by Meta and cannot be requested manually. This does not affect you: your planned daily budgets are USD 8 to 12. But it is why you should not plan to "scale to USD 100/day" if things go well in week one. You will not be allowed to.

---

## STEP 7: Complete business details

**Where:** `business.facebook.com` > `Settings` > `Business info`.

Fill every field. Legal business name, street address, city (Dhaka), postcode, country, phone, website, and industry. Then upload the square logo as the portfolio avatar.

**Why it matters:** [SECONDARY] account trust scoring is opaque, but the consistent practitioner observation is that complete, consistent business information correlates with faster spending-limit increases and fewer automated restrictions. It costs you ten minutes.

**Optional but recommended if offered:** `Settings` > `Security Centre` > `Start verification`. [FACT] Meta states plainly that "not all businesses need to or have the option to complete verification"; you will see either `Start verification` or `Ineligible for verification`. If it is offered and you have a trade licence, do it. Budget up to 14 business days for a decision. Do not block your launch on it.

---

## STEP 8: Verify your domain

[PLATFORM] **Important correction to older guides:** domain verification is **no longer a prerequisite** for conversion optimisation or Aggregated Event Measurement. [FACT] Meta's own AEM help page now states "You no longer need to prioritise eight conversion events," and multiple 2025 and 2026 sources confirm the manual AEM configuration tab and the eight-event cap have been removed for web events, with Meta aggregating eligible web events automatically.

**So why do it?** Two real reasons that still apply:
1. It gives you exclusive rights to edit link previews and ad copy for your domain, and prevents anyone else from running ads that claim your domain.
2. It is one of the cheapest trust signals you can add to a new portfolio.

**Where to go:** `business.facebook.com` > `Settings` > `Brand safety and suitability` > `Domains` > `Add`.

**What to enter:** your root domain without protocol or `www`, for example `sphotik.com`.

**Method:** choose **DNS TXT record**. It is the most reliable for a Cloudflare-hosted site.

1. Meta gives you a string like `facebook-domain-verification=abc123...`.
2. Go to your Cloudflare dashboard > your domain > `DNS` > `Add record`.
3. Type `TXT`, Name `@`, Content the full `facebook-domain-verification=...` string, TTL Auto.
4. Wait 5 to 60 minutes, then click `Verify` in Meta.

**What success looks like:** the domain shows a green `Verified` badge.

**Common errors:**
- Adding the record to `www.sphotik.com` instead of the root `@`.
- Cloudflare proxying interfering. TXT records are never proxied, so this is not actually an issue, but people panic about it.
- Verifying `sphotik.com` while your site actually serves from `sphotik.com.bd`. Pick one domain (Step 1) before doing this.

**How to verify independently:** run `nslookup -type=TXT sphotik.com` in PowerShell and confirm the string appears.

---

## STEP 9: Create the dataset and install the Pixel

[PLATFORM] **Terminology change you need to know.** Meta has merged website, app, offline, and messaging events into a single object called a **dataset**. The Pixel is now a data source *inside* a dataset. [FACT] Meta Business Help Centre, "About datasets in Meta Events Manager," help ID 750785952855662: "When you create your dataset, you'll have a dataset ID that you can use to set up your integrations... You can use your dataset ID like you would use your pixel ID." Your dataset ID **is** your Pixel ID. Older guides saying "create a pixel" are describing the same object.

### 9a. Create the dataset

**Where to go:** Ads Manager > hamburger menu > `Events Manager`.

1. Click `Connect data` (green button).
2. Select `Web`. Continue.
3. Name it `Sphotik - Web`.
4. When asked, choose **`Meta Pixel only`** for now. You will add the server side in Step 10. [PLATFORM: choosing pixel-first does not lock you out of adding Conversions API later.]
5. Enter your website URL when prompted.
6. Copy the **dataset ID** (a 15 or 16 digit number). This is your Pixel ID.

Then `Settings` on the dataset > `Assign assets` > add the `Sphotik - BD - Ads 01` ad account.

### 9b. Install it in your Next.js app

Add to `.env.local` and to your Cloudflare Pages environment variables:

```
NEXT_PUBLIC_META_PIXEL_ID=000000000000000
```

Create `src/components/analytics/MetaPixel.tsx`:

```tsx
"use client"
import Script from "next/script"
import { usePathname, useSearchParams } from "next/navigation"
import { useEffect, useRef } from "react"

const PIXEL_ID = process.env.NEXT_PUBLIC_META_PIXEL_ID

/**
 * The base snippet fires the first PageView. App Router navigations are
 * client-side, so every subsequent route change needs an explicit one.
 */
export function MetaPixel() {
  const pathname = usePathname()
  const searchParams = useSearchParams()
  const isFirstRender = useRef(true)

  useEffect(() => {
    if (!PIXEL_ID) return
    if (isFirstRender.current) {
      isFirstRender.current = false
      return
    }
    window.fbq?.("track", "PageView")
  }, [pathname, searchParams])

  if (!PIXEL_ID) return null

  return (
    <Script id="meta-pixel" strategy="afterInteractive">
      {`!function(f,b,e,v,n,t,s)
{if(f.fbq)return;n=f.fbq=function(){n.callMethod?
n.callMethod.apply(n,arguments):n.queue.push(arguments)};
if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
n.queue=[];t=b.createElement(e);t.async=!0;
t.src=v;s=b.getElementsByTagName(e)[0];
s.parentNode.insertBefore(t,s)}(window,document,'script',
'https://connect.facebook.net/en_US/fbevents.js');
fbq('init','${PIXEL_ID}');
fbq('track','PageView');`}
    </Script>
  )
}
```

Mount it in `src/app/layout.tsx`, inside a `Suspense` boundary because it reads `useSearchParams`:

```tsx
import { Suspense } from "react"
import { MetaPixel } from "@/components/analytics/MetaPixel"
// ...
      <body suppressHydrationWarning>
        <Suspense fallback={null}>
          <MetaPixel />
        </Suspense>
        <ClientProviders>
```

Also widen the `fbq` type in `src/lib/analytics.ts` so you can pass an event ID (needed for deduplication in Step 10):

```ts
    fbq?: (
      method: string,
      event: string,
      payload?: Record<string, unknown>,
      options?: { eventID?: string },
    ) => void
```

and thread it through `track`:

```ts
export function track(event: AnalyticsEvent, payload: Payload = {}, eventId?: string) {
  if (typeof window === "undefined") return
  const clean = Object.fromEntries(
    Object.entries(payload).filter(([, v]) => v !== undefined),
  )
  window.dataLayer?.push({ event, ...clean })
  if (typeof window.fbq === "function") {
    const method = STANDARD_META_EVENTS.has(event) ? "track" : "trackCustom"
    window.fbq(method, event, clean, eventId ? { eventID: eventId } : undefined)
  }
}
```

**What success looks like:** install the [Meta Pixel Helper](https://chromewebstore.google.com/detail/meta-pixel-helper/fdgfkebogiimcoedlicjlajpkdmockpc) Chrome extension, load your homepage, and see one `PageView`. Then navigate to `/member` **without reloading** and see a second `PageView`.

**Common errors:**
- Forgetting to add `NEXT_PUBLIC_META_PIXEL_ID` to the Cloudflare Pages environment variables (not just `.env.local`). The Pixel will work locally and silently do nothing in production. This is the single most common launch failure.
- Placing `MetaPixel` outside `Suspense`. Next.js will throw a build error about `useSearchParams` requiring a suspense boundary.
- Double-firing the first `PageView` by not guarding `isFirstRender`.

---

## STEP 10: Set up the server-side Purchase (a minimal, deliberate Conversions API)

**Should you do CAPI at all on a USD 250 budget?**

Normally I would say no, defer it. In your case the answer is **yes, but only for one event**, because of a fact specific to your business: **your payments are manually verified.** A browser-only setup physically cannot tell Meta the truth about who paid you. That makes a server event not an optimisation nicety but a correctness requirement.

### The architecture

```
Browser (Pixel)                      Server (Apps Script)
─────────────────                    ────────────────────
PageView          ──────────────>
ViewMembership    ──────────────>
SelectMembership  ──────────────>
SelectDeposit     ──────────────>
InitiateCheckout  ──────────────>
AddPaymentInfo    ──────────────>
PaymentClaimed    ──────────────>    (row written to Google Sheet)
                                              │
                                     you check bKash, mark verified
                                              │
                                              v
                                     Purchase  ──────────> Meta CAPI
                                     (value = membership fee)
```

The browser never sends `Purchase`. The server sends `Purchase` only for verified money. There is nothing to deduplicate, because no event is sent twice. That is why this design is simpler than a normal Pixel-plus-CAPI setup, not more complex.

### Implementation

1. **Events Manager** > `Sphotik - Web` > `Settings` > scroll to `Conversions API` > `Generate access token`. Copy it. Store it in Apps Script under `Project Settings` > `Script properties` as `META_ACCESS_TOKEN`. **Never put this in your Next.js code.** It is a server credential; `NEXT_PUBLIC_` anything is visible to the world.

2. In your Apps Script, add a function triggered manually (or on a time-based trigger) that scans the sheet for rows where `status = verified` and `capi_sent` is empty, and for each one `POST`s to:

   ```
   https://graph.facebook.com/v21.0/{DATASET_ID}/events?access_token={TOKEN}
   ```

   [PLATFORM: check the current Graph API version in Meta's developer docs before shipping; `v21.0` is illustrative.]

   Payload shape:

   ```json
   {
     "data": [{
       "event_name": "Purchase",
       "event_time": 1757000000,
       "event_id": "SP-XXXXXX",
       "action_source": "website",
       "event_source_url": "https://sphotik.com/checkout",
       "user_data": {
         "em": ["<sha256 of lowercased trimmed email>"],
         "ph": ["<sha256 of phone in E.164 without +, e.g. 8801712345678>"],
         "fbp": "fb.1.1757000000.1234567890",
         "fbc": "fb.1.1757000000.IwAR..."
       },
       "custom_data": {
         "currency": "BDT",
         "value": 900,
         "content_name": "6-months",
         "content_category": "membership"
       }
     }]
   }
   ```

3. Mark `capi_sent = yes` on success.

**Critical rules:**
- `em` and `ph` must be **SHA-256 hashed by you** before sending. `fbp` and `fbc` must **not** be hashed. Apps Script: `Utilities.computeDigest(Utilities.DigestAlgorithm.SHA_256, value)` then hex-encode.
- Normalise before hashing: email lowercased and trimmed; phone digits only with country code and no `+`, so `01712345678` becomes `8801712345678`.
- [PLATFORM] CAPI accepts an `event_time` up to 7 days in the past. Your verification delay of a few hours is comfortably inside that. Use the **original checkout timestamp**, not the verification timestamp, so attribution lands on the right day.

**What success looks like:** in Events Manager > `Test Events`, add the `test_event_code` to your payload and see `Purchase` appear with a "Server" source and a high number of matched parameters.

**If this is too much work right now:** the acceptable fallback is to send zero `Purchase` events to Meta and track members entirely in your sheet. That is **strictly better** than sending fake unverified purchases. What is not acceptable is leaving the current code as-is.

---

## STEP 11: Configure events

See Part 4 for the full architecture and Part 3 for naming. Summary of what to build in Events Manager:

**Custom conversions** (Events Manager > `Custom conversions` > `Create`):

| Name | Rule | Purpose |
|---|---|---|
| `SPH - Membership Page View` | Event `PageView`, URL contains `/member` | Mid-funnel optimisation fallback and audience source |
| `SPH - Checkout Started` | Event `InitiateCheckout` | Cleaner reporting label |
| `SPH - Payment Claimed` | Custom event `PaymentClaimed` | Diagnostic only. **Never optimise for this.** |
| `SPH - Verified Member` | Event `Purchase` | Your true north metric |

**What NOT to do:** do not create a custom conversion for every page. Custom conversions are limited (historically 100 per ad account) and each one is a thing you have to reason about. Four is right.

---

## STEP 12: Test events

Covered in full in Part 5. Do not proceed past this step until every line of that checklist passes.

---

## STEP 13: Create UTMs

Covered in Part 3.

---

## STEP 14: Connect analytics

[RECOMMENDATION] **Skip Google Analytics for now.** Reasons: your `src/lib/analytics.ts` already pushes to `window.dataLayer`, so you can add GTM in twenty minutes whenever you want; GA4's learning curve will eat a day you do not have; and at ~1,700 sessions total across the whole test, your Google Sheet plus Meta's own reporting will tell you everything GA4 would.

**What to use instead:** Cloudflare Web Analytics. It is free, it is one line, it requires no cookie banner, and it will give you page views and top pages, which is all you need. Cloudflare dashboard > your site > `Analytics` > `Web Analytics` > enable.

**Add GA4 only if** you get past the validation phase and start running more than one channel.

---

## STEP 15: Create audiences

**Where:** Ads Manager > hamburger > `Audiences` > `Create audience` > `Custom audience`.

Create all seven **before launch day**, even though most will be empty. Custom audiences only start collecting from the moment they are created. Every day you delay is a day of retargeting pool you never get back.

| Name | Source | Rule | Retention |
|---|---|---|---|
| `WEB-ALL-30D` | Website | All visitors | 30 days |
| `WEB-MEMBER-30D` | Website | URL contains `/member` | 30 days |
| `WEB-CHECKOUT-30D` | Website | URL contains `/checkout` | 30 days |
| `WEB-CLAIMED-180D` | Website | Event `PaymentClaimed` | 180 days |
| `WEB-PURCHASE-180D` | Website | Event `Purchase` | 180 days |
| `VID-25PCT-30D` | Video | People who viewed at least 25% of any of your three videos | 30 days |
| `SOC-ENGAGE-90D` | Facebook Page + Instagram account | Anyone who engaged | 90 days |

**Common error:** creating a "Website: all visitors, 180 days" and thinking you can retarget it. You can, but Meta needs roughly 1,000 people in an audience before delivery is reliable. See Part 19 for when to turn retargeting on.

---

## STEP 16: Build campaigns

Covered in Part 14.

---

## STEP 17: Launch

Covered in Parts 15 and 29.

---
---

# PART 3: Naming convention

Simple, greppable, no cleverness. Every name is lowercase-safe and uses hyphens.

| Asset | Convention | Your value |
|---|---|---|
| Business Portfolio | Brand name | `Sphotik` |
| Facebook Page | Brand name | `Sphotik` |
| Page username | `@brand` | `@sphotik` |
| Instagram | `@brand.bd` | `@sphotik.bd` |
| Ad account | `Brand - Country - Ads NN` | `Sphotik - BD - Ads 01` |
| Dataset / Pixel | `Brand - Source` | `Sphotik - Web` |
| Campaign | `OBJECTIVE-STAGE-GEO-YYMM` | `SALES-PROSPECT-DHK-2609` |
| Ad set | `AUDIENCE-AGERANGE-PLACEMENT` | `BROAD-2045-AUTO` |
| Ad | `CREATIVE-ANGLE-VERSION` | `V1-MATH-A` |
| Audience | `SOURCE-FILTER-WINDOW` | `WEB-MEMBER-30D` |
| Custom conversion | `SPH - Human readable` | `SPH - Verified Member` |

**Creative codes** (fix these now, use them everywhere including your video filenames):

| Code | Video | Angle |
|---|---|---|
| `V1-MATH` | The Shelf | Loss aversion / money arithmetic |
| `V2-HOW` | How It Actually Works | Mechanism clarity |
| `V3-WHY` | Why I Built This | Founder trust |

## UTM convention

```
utm_source=facebook
utm_medium=paid_social
utm_campaign={{campaign.name}}
utm_content={{ad.name}}
utm_term={{placement}}
```

[PLATFORM] Meta supports dynamic URL parameters including `{{campaign.name}}`, `{{adset.name}}`, `{{ad.name}}`, `{{placement}}`, and `{{site_source_name}}`. Paste the block above into the **URL parameters** field at the ad level (Ads Manager > ad > `Tracking` > `URL parameters`), not into the destination URL itself. Meta appends it automatically and you never have to hand-write a URL.

**Result on a real click:**
```
https://sphotik.com/?utm_source=facebook&utm_medium=paid_social&utm_campaign=SALES-PROSPECT-DHK-2609&utm_content=V1-MATH-A&utm_term=Instagram_Reels&fbclid=IwAR...
```

Your order recorder (Step 0) writes all five UTMs to the sheet. That is how you will know which of your three videos actually produced paying members, independent of anything Meta tells you. **This is more trustworthy than Meta's own attribution at your volume**, because Meta's numbers will be heavily modelled at 10 to 20 conversions.

Because UTMs only exist on the landing URL and your checkout is several client-side navigations later, capture them into `sessionStorage` on first load, alongside `fbp` and `fbc`.

**Non-paid UTMs** for organic posts, so you can compare:
```
utm_source=facebook&utm_medium=organic&utm_campaign=launch
utm_source=instagram&utm_medium=bio&utm_campaign=launch
utm_source=whatsapp&utm_medium=dm&utm_campaign=friends
```

---
---

# PART 4: Tracking architecture

## 4.1 The event map

Your `src/lib/analytics.ts` already defines most of these. The table below is the target state.

| Event | Type | Fires when | Where in your code | Parameters | Optimise? |
|---|---|---|---|---|---|
| `PageView` | Standard | Every page load and every client-side route change | `MetaPixel.tsx` (Step 9) | none | No |
| `ViewContent` | Standard | Book detail page opens | `src/app/book/[id]/page.tsx` | `content_ids`, `content_name`, `content_type: "book"` | No |
| `ViewMembership` | **Custom** | `/member` renders | `src/app/member/page.tsx` | `content_category: "membership"` | Fallback only |
| `SelectMembership` | **Custom** | User picks 6m or 12m | `MembershipCard` | `plan`, `value` (fee), `currency` | No |
| `SelectDeposit` | **Custom** | User picks a deposit tier | `MembershipCard`, checkout step 1 | `deposit`, `books` | No |
| `InitiateCheckout` | Standard | `/checkout` mounts | `src/app/checkout/page.tsx` | `value` (membership fee), `currency`, `num_items: 1` | **Yes, fallback** |
| `AddPaymentInfo` | Standard | User selects a payment method on step 3 | `PaymentStep` **(new, add this)** | `value`, `currency` | No |
| `Lead` | Standard | Contact details submitted | `DetailsStep` | `value`, `currency` | No |
| `PaymentClaimed` | **Custom** | Confirmation screen renders **(replaces browser Purchase)** | `Confirmed` | `value`, `currency`, `deposit_value`, `cash_collected`, `plan`, `order_no` | **Never** |
| `Purchase` | Standard | **Server-side only**, after you verify the money arrived | Apps Script | `value` (membership fee), `currency`, `content_name`, `content_category` | **Yes, primary** |
| `JoinWaitlist` | **Custom** | Waitlist join on a book page | `src/app/book/[id]/page.tsx` | `content_ids`, `content_name` | No |
| `BookRequest` | **Custom** | `/request` submitted | `src/app/request/page.tsx` | `content_name` | No |
| `ReturnRequest` | **Custom** | Return pickup requested | profile return card | none | No |

### Which are standard, which are custom

[FACT] Meta recognises 17 standard events: `AddPaymentInfo`, `AddToCart`, `AddToWishlist`, `CompleteRegistration`, `Contact`, `CustomizeProduct`, `Donate`, `FindLocation`, `InitiateCheckout`, `Lead`, `PageView`, `Purchase`, `Schedule`, `Search`, `StartTrial`, `SubmitApplication`, `Subscribe`, `ViewContent`. Source: Meta for Developers, "Meta Pixel Reference."

Your `STANDARD_META_EVENTS` set currently contains `PageView`, `ViewContent`, `InitiateCheckout`, `Purchase`, `Lead`. **Add `AddPaymentInfo`.** Everything else in your union is correctly sent via `trackCustom`.

**Two events you might be tempted to map to standard events, and should not:**

- `ViewMembership` → do **not** map to `ViewContent`. Your `/book/[id]` pages already fire `ViewContent`, and merging the two would give you one blended signal where you need two distinct ones. Keep `ViewMembership` custom and reach it through a custom conversion.
- `JoinWaitlist` → do **not** map to `AddToWishlist`. It looks tempting but `AddToWishlist` is wired into Meta's catalogue-ads machinery and you do not have a catalogue. Keep it custom.

**One you should reconsider later:** `CompleteRegistration` would be the natural standard event for "membership activated." Once you have real accounts (right now `/auth` is a UI mock), fire `CompleteRegistration` server-side when you activate a membership. Not now.

### Which should be custom conversions

Only four, all created in Events Manager, not in code. See Step 11. The rule: create a custom conversion when you need to **optimise for** or **cleanly report on** something that is not already a standard event with the right shape. Do not create one just to have a nicer name in a chart.

### What data must NOT be sent to Meta

This is not optional; it is a policy and legal boundary.

| Never send | Why |
|---|---|
| Raw, unhashed email or phone in any browser Pixel call | Meta's Business Tools Terms prohibit sending unhashed personal data. The browser Pixel has no supported hashing path for you. Send identifiers server-side, hashed, only. |
| National ID, NID number, passport number | Prohibited. |
| Full street address, building number, flat number | Do not send. `area` is fine as a coarse city-district string in your own sheet; do not send it to Meta at all. |
| bKash / Nagad / Rocket transaction ID | This is a financial identifier. Keep it in your sheet. Never in an event parameter. |
| Payment account numbers of any kind | Obviously. |
| Deposit amount as the Meta `value` | See 4.2. Not a privacy issue, an accuracy one. |
| Anything about which specific books a member borrowed, tied to a person | Reading habits are sensitive. Your `/privacy` page makes commitments; honour them. |
| `external_id` derived from a phone number without hashing | If you use `external_id`, hash it. |

Your existing `track()` function is well designed here: it accepts only `string | number | boolean` and strips `undefined`. Keep that discipline. The risk is not the function, it is what a future you passes into it.

## 4.2 The Purchase value question

> A founding customer pays ৳900 membership + ৳500 refundable deposit = ৳1,400 today. Should Meta `Purchase` value be A) ৳1,400, B) ৳900, or C) something else?

**Answer: B, ৳900. Send the membership fee only. Pass the deposit as a separate custom parameter.**

Here is the reasoning from both directions, and the one place where the answer is genuinely arguable.

### From the accounting side

The deposit is a **liability**, not revenue. You are holding someone else's money with a contractual obligation to give it back. Under any reasonable accounting treatment it sits on your balance sheet as a current liability, not on your income statement as revenue. It never becomes revenue unless a member forfeits it, which is a rare, separate, and unpredictable event.

If you set `value = 1400`, then the ROAS column in Ads Manager becomes a number that is 55% larger than your actual revenue. You will look at "ROAS 1.2" and feel roughly break-even when you are in fact losing 36% on every acquisition. Over a three-week test that error is survivable. As a habit that you carry into scaling, it is the kind of error that quietly kills a company: you scale spend against a revenue number that includes money you owe back.

There is a specific failure mode here worth naming. Deposits are refunded on a lag (your policy says at period end, 3 to 7 working days after request). So in month one, deposit inflows are pure cash and no outflows have happened yet. If you have trained yourself to read ৳1,400 as revenue, the first three months will look great and month seven will look like a collapse, when in reality nothing changed except that the refunds arrived. That is textbook float-mistaken-for-profit.

### From the performance marketing side

Two arguments, pointing the same way.

**Argument one: `value` is an optimisation input, not just a label.** If you ever enable value optimisation or a minimum-ROAS bid strategy, Meta will bid harder for users predicted to produce a higher `value`. With `value = membership + deposit`, the highest-value user is someone who picks the ৳1,500 deposit tier, so Meta learns to chase deposit size. Deposit size is not a proxy for member quality; if anything, the ৳1,500 tier selects for heavy borrowers, who cost you the most in delivery, inventory contention, and support. You would be paying Meta to find your most expensive customers.

**Argument two: with `value = membership fee`, the number carries real, useful information for free.** A six-month member sends `900`; a twelve-month member sends `1600`. That is a genuine, correct quality differential, and it is exactly the signal you would want a value-optimising system to learn from. You get a better optimisation signal by being accurate than by being generous.

The counterargument, stated fairly: **at USD 250 you will not use value optimisation at all.** You will use lowest-cost bidding, in which Meta ignores `value` entirely for delivery purposes. So on pure delivery mechanics, the choice is nearly irrelevant during this test. That is precisely why you should set it correctly now: the decision is free today and expensive to unwind later, because changing `value` semantics mid-flight makes all your historical data non-comparable.

### The one place C has a real case

There is a defensible "C": **contribution margin**, roughly ৳480 for a six-month member after conservative delivery leakage and packaging (see Part 18). Feeding margin instead of revenue is what a sophisticated advertiser eventually does, because it makes ROAS directly readable as profitability.

I am **not** recommending it, for three reasons. You do not yet know your true margin (you have never shipped a book). It makes your Meta numbers incomparable to every benchmark and every conversation you will have. And it adds a moving part to a system that currently has too many. Revisit at 200 members.

### The exact implementation

**Browser, on the confirmation screen:**

```ts
track("PaymentClaimed", {
  currency: "BDT",
  value: plan.price,            // 900 or 1600
  deposit_value: tier.deposit,  // 500, 1000, or 1500
  cash_collected: payToday,     // 1400, etc. Reporting only.
  plan: plan.id,
  order_no: orderNumber ?? "",
})
```

**Server, after verification:**

```json
"custom_data": {
  "currency": "BDT",
  "value": 900,
  "content_name": "6-months",
  "content_category": "membership",
  "num_items": 1
}
```

**And in your Google Sheet, track four separate columns, because these are four different numbers and conflating any two of them will mislead you:**

| Column | Six-month founding example | What it is |
|---|---|---|
| `membership_revenue` | ৳900 | Revenue. Goes to Meta as `value`. |
| `deposit_held` | ৳500 | Liability. Never revenue. |
| `cash_collected` | ৳1,400 | What hit your bKash today. Your runway. |
| `delivery_collected` | ৳0 at signup | Pass-through. Not revenue. |

Your CAC is measured against `membership_revenue`. Your survival is measured against `cash_collected`. Do not confuse the two, and never let the second one make you feel good about the first.

---
---

# PART 5: Verify tracking

Run every line. Tick every box. Any failure is a launch blocker.

## 5.1 Pixel installation

- [ ] Meta Pixel Helper extension shows the Pixel as **found and active** on `sphotik.com`.
- [ ] The Pixel ID shown matches your dataset ID in Events Manager exactly.
- [ ] Exactly **one** Pixel is detected. Two means you pasted the snippet twice.
- [ ] Page source contains `connect.facebook.net/en_US/fbevents.js`.
- [ ] The Pixel loads in **production**, not just localhost. Check the live Cloudflare URL in a private window. (This is where the missing environment variable bites.)
- [ ] Pixel loads on every page: `/`, `/member`, `/checkout`, `/catalog`, `/book/[id]`, `/faq`, `/how-it-works`, `/delivery`.

## 5.2 Browser events

Navigate the whole funnel manually with Pixel Helper open, without ever reloading the page.

- [ ] `/` → one `PageView`.
- [ ] Click through to `/member` → a **second** `PageView` fires. (This is the App Router route-change fix. If it does not fire, `MetaPixel.tsx` is wrong and every custom conversion built on URL rules is dead.)
- [ ] `/member` → `ViewMembership` fires once, not on every re-render.
- [ ] Click a membership card → `SelectMembership` with a `plan` parameter.
- [ ] Change deposit tier → `SelectDeposit` with `deposit` and `books`.
- [ ] Arrive at `/checkout` → `InitiateCheckout` fires **exactly once** with `value` and `currency: "BDT"`. (Your `viewed` ref guard handles this; confirm it survives the deposit change, because `payToday` is in the dependency array.)
- [ ] Submit contact details → `Lead`.
- [ ] Select bKash → `AddPaymentInfo` (new event; confirm you added it).
- [ ] Confirm payment → `PaymentClaimed` fires, and **no `Purchase` fires**.
- [ ] Book detail page → `ViewContent` with `content_ids`.
- [ ] Navigate backwards and forwards through checkout steps → no event fires twice.

## 5.3 Conversions API

- [ ] Access token is stored in Apps Script Script Properties, **not** in the repo, **not** in a `NEXT_PUBLIC_` variable.
- [ ] `git grep -i "EAAG\|access_token"` in your repo returns nothing.
- [ ] A test `Purchase` sent with `test_event_code` appears in Events Manager > `Test Events` with source **Server**.
- [ ] `user_data` contains hashed `em` and `ph`, plus unhashed `fbp` and `fbc`.
- [ ] Event Match Quality for `Purchase` shows as **Good** or better once you have a handful of real events. If it shows Poor, your hashing or normalisation is wrong.
- [ ] `event_time` uses the original checkout timestamp, not the verification timestamp.

## 5.4 Deduplication

Your architecture avoids the deduplication problem entirely: `Purchase` is sent from exactly one place. Verify that:

- [ ] Search your codebase: `grep -rn "Purchase" src/` returns **no** call site that fires it from the browser.
- [ ] Events Manager > `Sphotik - Web` > `Overview` > `Purchase` shows connection method **Server only**, not "Browser and Server."
- [ ] If you later add a browser `Purchase` for any reason, you must pass the same `event_id` in both places. [FACT: Meta's Pixel reference recommends passing `eventID` as the fourth parameter to `fbq('track')` and the matching `event_id` in the CAPI payload; `event_name` must match exactly, case-sensitively.] Your `track()` signature after Step 9b supports this.

## 5.5 Purchase fires only after successful payment

- [ ] Complete a checkout with a **fake** transaction ID like `TESTFAKE123`. Confirm: a row appears in the sheet with `status` empty, `PaymentClaimed` fires, and **`Purchase` does not appear in Events Manager**.
- [ ] Manually set `status = verified` in the sheet, run the CAPI function, and confirm `Purchase` now appears.
- [ ] Confirm `capi_sent` is set to `yes` so the row cannot be sent twice.
- [ ] Run the CAPI function twice in a row deliberately. Confirm no duplicate `Purchase`.

## 5.6 No duplicate Purchase

- [ ] Refresh the confirmation screen. Because your checkout state is in React state, a refresh resets `step` to `"membership"`, so nothing re-fires. Confirm this is still true after you add the state persistence from M4. **This is the exact place where M4 could reintroduce a duplicate.** If persisted state restores `step === "confirmed"`, guard the effect on a persisted `hasFiredPaymentClaimed` flag, not just the `recorded` ref.
- [ ] Browser back button from `/catalog` to `/checkout` does not re-fire.

## 5.7 InitiateCheckout

- [ ] Fires once per checkout session.
- [ ] Does **not** fire on `/member`.
- [ ] `value` equals the membership fee, not the pay-today total. (Your current code sends `payToday`. Change it, per Part 4.)

## 5.8 Parameters

- [ ] `currency` is `"BDT"` on every valued event. Your `CURRENCY` constant already handles this; confirm it is used everywhere.
- [ ] `value` is a **number**, not a string. `900`, not `"900"` and not `"৳900"`.
- [ ] No `undefined` values in any payload. Your `track()` already strips these.
- [ ] No personal data in any browser event. Re-read the "must not send" table in Part 4.

## 5.9 Domain verification

- [ ] `Settings` > `Brand safety and suitability` > `Domains` shows `sphotik.com` as Verified.
- [ ] `nslookup -type=TXT sphotik.com` returns the `facebook-domain-verification=` string.

## 5.10 Event configuration

- [ ] All four custom conversions exist and show a non-zero event count after you run test traffic.
- [ ] [PLATFORM] Check whether your Events Manager still shows an **Aggregated Event Measurement** tab. Most accounts no longer do; Meta removed manual event prioritisation and the eight-event cap for web events in mid-2025 and now aggregates eligible web events automatically. If your account still shows the old interface, rank in this order: `Purchase`, `InitiateCheckout`, `AddPaymentInfo`, `Lead`, `ViewMembership`, `ViewContent`, `PageView`.
- [ ] Dataset is assigned to the `Sphotik - BD - Ads 01` ad account under `Assign assets`.

## 5.11 Test Events tool

- [ ] Events Manager > `Sphotik - Web` > `Test Events` > enter your live URL > walk the full funnel in the opened window.
- [ ] Every expected event appears in the real-time stream in the right order.
- [ ] Click into each event and read the parameters. Read them; do not glance at them.

## 5.12 Diagnostics

- [ ] Events Manager > `Diagnostics` shows zero errors and zero warnings.
- [ ] Specifically look for: "Invalid currency," "Missing required parameter," "Unverified domain," "Low event match quality," "Redundant PageView."
- [ ] Re-check Diagnostics 24 hours after launch. Some issues only surface with real traffic volume.

---
---

# PART 6: Facebook Page setup

Every piece of copy below is ready to paste.

## Page name
```
Sphotik
```
Not "Sphotik Library BD" or "Sphotik - Book Rental Bangladesh." Keyword-stuffed Page names look like dropshipping accounts, and Meta has restricted name changes on Pages that do it.

## Username
```
@sphotik
```
Fallback if taken: `@sphotikbd`. Match your Instagram handle as closely as possible.

## Category
Primary: `Library`. Secondary: `Book Store`.

## Profile image
1080×1080 square logo. High contrast. It renders at roughly 40 pixels in a mobile feed, so a detailed wordmark will be an illegible smudge. **Use the mark, not the wordmark.** Test it by shrinking the file to 40×40 and looking at it on your phone.

## Cover image
1640×856, all text inside the central 1090×630.

Content: a clean shot of real books on a real shelf, warm light, with the line
```
Read more. Own less.
```
in your display typeface, small, bottom left.

**Do not** put pricing, phone numbers, or a call to action in the cover. Cover images are wallpaper, not billboards, and nobody reads them.

## Description (long, `About` section)
```
Sphotik is a membership library in Bangladesh.

You join once, borrow books from our collection, read them at your own pace, send them back, and borrow again. There is no rental fee for each book. Your membership covers your access, a refundable security deposit sets how many books you can hold at a time, and delivery is charged separately for each swap.

We started Sphotik because reading a lot of books in Bangladesh is expensive, and most of us finish a book once and then watch it sit on a shelf for years. A library solves that, if someone runs it properly: honest availability, books that arrive in good condition, and clear rules about money.

Currently serving Dhaka. Founding memberships are open now.

Read more. Own less.
```

## Short bio (the 101-character field)
```
A membership library in Bangladesh. Borrow books, read, send them back, borrow again. No per-book fee.
```
That is 101 characters exactly. Verify in your editor before pasting.

## Call to action button
`Learn more` → `https://sphotik.com`

Not `Shop now` (implies buying books). Not `Sign up` (implies free). Not `Book now` (implies appointments). `Learn more` is correct for a model people do not yet understand.

## Website
```
https://sphotik.com
```
One domain. The same one you verified.

## Contact details
- Email: `hello@sphotik.com.bd` (or switch to `@sphotik.com` if you consolidate)
- Phone: a real, answered mobile number. **Delete the `+880 1700 000000` placeholder from `/contact` first.**
- WhatsApp: same number, business account
- Location: `Dhaka, Bangladesh`. Set the address to a real one. You do not have to enable "visitors can come here."
- Hours: set them honestly. `10:00 to 20:00, Saturday to Thursday` is credible for a solo operation. Do not claim 24/7.

## Pinned post

Post this before your first ad goes live. Pin it. It is the first thing a cautious buyer reads after clicking your Page name from an ad.

```
What Sphotik actually is, in one post.

Sphotik is a library you can join. Not a bookshop, not a per-book rental.

How it works:
1. You take a membership. ৳900 for 6 months at our founding price, or ৳1,600 for 12 months.
2. You place a refundable security deposit. ৳500 lets you hold 2 books at a time, ৳1,000 lets you hold 4, ৳1,500 lets you hold 6.
3. You pick books from our catalogue. We courier them to you.
4. You read. When you are done, we arrange the pickup and send your next set.
5. You pay delivery for each swap. That is the only ongoing cost.

There is no fee for each book you read. Read one book a month or eight, the membership costs the same.

Your deposit is yours. It comes back in full once your books are returned and nothing is outstanding.

Three honest things:
- We are new. We launched in September 2026 and we are starting in Dhaka only.
- Our catalogue is small on purpose. We would rather own 200 books that are actually on the shelf than list 5,000 we cannot send you.
- If a book is marked available, a physical copy is sitting here right now. We do not list books we do not have.

Full rules, pricing, and the catalogue: sphotik.com

Questions? Comment below or message us. A real person answers.
```

## Credibility setup

For a brand nobody has heard of asking for ৳1,400, the Page is a trust document. Do these before launch:

1. **Post four to six times before the first ad.** A Page with an ad and an empty timeline reads as a scam. Suggested first posts:
   - The pinned explainer above.
   - A photo of your actual shelves. Real books, real room, no stock photography. This is the single highest-trust asset you own.
   - A short post naming five specific titles that are available right now, with a photo.
   - The founder introduction (see Part 7, it cross-posts).
   - A plain post explaining the deposit refund policy in full.
2. **Turn on Page Transparency detail.** `Settings` > `Page transparency`. Showing the Page creation date and the country of the admin is a trust *gain*, not a loss, for a legitimate local business. Hiding it is what fake Pages do.
3. **Enable reviews / recommendations** but expect zero for two weeks. Do not seed them. See Part 23.
4. **Respond to every comment within a few hours**, publicly. Response rate is visible and it is one of the few objective trust signals a new Page can build fast.
5. **Do not** buy likes. It is detectable, it destroys your engagement rate, and Bangladeshi consumers specifically look for the follower-to-engagement mismatch that bought likes produce.

---
---

# PART 7: Instagram setup

Keep this genuinely MVP. Instagram is your **credibility surface**, not your acquisition channel, for the next month. Your ads will run on Instagram placements, and people will tap the profile before they pay you. That is its whole job right now.

## Username

In order of preference: `@sphotik` → `@sphotik.bd` → `@sphotiklibrary` → `@readsphotik`

Avoid underscores and numbers. Match the Facebook username if you possibly can.

## Bio

150 characters maximum, and Instagram counts emoji generously.

```
A membership library in Dhaka
Borrow books. Read. Send them back. Borrow again.
No fee per book · Refundable deposit
Founding memberships open ↓
```

## Link strategy

**Use a single direct link. Do not use Linktree.** Reasons: it costs you a click, it looks like a creator account rather than a business, and it breaks your UTM chain.

```
https://sphotik.com/?utm_source=instagram&utm_medium=bio&utm_campaign=launch
```

[PLATFORM] Instagram now supports up to five links in the bio. Use two at most: the homepage and the membership page. More than two dilutes.

## Profile setup

- Business account (required for ads and insights).
- Category: `Library`.
- Same profile image as Facebook.
- Contact buttons: email and WhatsApp enabled, call optional.
- Connect to the Sphotik Facebook Page from Business Settings (done in Step 4).

## Highlights

Four covers, plain typographic, same colour family as the site.

| Highlight | Content |
|---|---|
| `How it works` | 5 story frames: join, choose, we courier, you read, we collect |
| `Pricing` | 3 frames: membership, deposit, delivery. Real numbers, no hedging. |
| `The shelf` | Photos of actual books you own. Refresh weekly. |
| `Questions` | 6 frames answering the top objections from Part 23 |

## First posts

Six pieces of content is enough to launch. Do not build a content calendar; you have a business to run.

| # | Format | Content |
|---|---|---|
| 1 | Carousel, 5 slides | The explainer. Same copy as the pinned Facebook post, one idea per slide. |
| 2 | Single photo | Your actual shelf. Caption: what you bought and why. |
| 3 | Reel, 20s | Silent, fast: hands pulling five books off a shelf, packing them, sealing the box. On-screen text only. This is your highest-reach organic asset. |
| 4 | Carousel, 3 slides | "Five books you can borrow right now." Real covers, real availability. |
| 5 | Reel, 45s | `V3-WHY` (the founder video, see Part 9), cut to vertical. |
| 6 | Single image | The deposit explained in one graphic. ৳500 → 2 books. ৳1,000 → 4 books. ৳1,500 → 6 books. |

## Cross-posting

- Post to Instagram first, then use Meta Business Suite's `Create post` to publish to both. Do not use third-party schedulers yet.
- **Do not** auto-crossport Instagram Stories to Facebook Stories. The audiences behave differently and the Facebook version gets almost no reach.
- Your three campaign videos should be uploaded natively to both, in **9:16 vertical** for Reels and Stories, and **4:5** for feed. Meta's Advantage+ placements will adapt automatically in ads, but for organic you need the native crops.

---
---

# PART 8: Landing page

## 8.1 The assessment

Your site is genuinely well built. The copy is calm, the FAQ is unusually honest (28 questions across 7 categories in `src/data/content.ts`), the bilingual implementation is real rather than decorative, and the tone matches the brand. Most early-stage sites I audit are far worse.

The problem is specific: **it is written for someone who already knows what Sphotik is.** It reads like the website of a library you have heard of. Cold Facebook traffic has not heard of you, has never encountered your model, and will make a stay-or-leave decision in about five seconds.

Concretely, here is what your current above-the-fold does and does not do:

| Question a cold visitor asks in the first 5 seconds | Answered above the fold today? |
|---|---|
| What is this? | Partly. The subheadline answers it, but it is below the headline and the headline is abstract. |
| What do I get? | Yes. |
| What does it cost me, today, in total? | **No.** You show "starts at ৳900" but not the ৳1,400 that checkout will actually ask for. |
| Can you deliver to me? | **No.** Dhaka is not mentioned above the fold. |
| Why should I believe you? | **No.** Zero trust signals above the fold. |
| Is this thing actually real, or a pre-launch? | **No.** |

The five-second failure mode is not "this is confusing," it is "this is a nice-looking site for something that might not exist."

## 8.2 What must be above the fold

In this order, on a 390×844 phone screen (the majority of your traffic):

1. **A location and status line.** Four words that make it real.
2. **A job-led headline.** The mechanism, not the philosophy.
3. **A subheadline that states the full price honestly**, including the deposit.
4. **One primary CTA.** One.
5. **A one-line trust strip.**

## 8.3 What must be removed or moved down

- **"Read more. Own less." as the H1.** Move it to the footer, where it already is, and to the closing section. It is a great sign-off and a bad opener. See Part 1.6.
- **The secondary CTA "Browse the library."** On cold traffic, a second CTA of equal weight splits intent, and `/catalog` is a dead end for someone who does not yet know what borrowing costs. Keep it, but demote it to a text link below the primary button, not a button beside it.
- **The book shelves section, in its current position.** Showing covers before explaining the model invites the reader to think "bookshop." Move it below "how it works."

## 8.4 What must be added

- **A "Pay today" module.** A small, explicit box: `Membership ৳900 + Refundable deposit ৳500 = ৳1,400 today. ৳500 of that comes back to you.` This is the single highest-leverage addition on the page, because it eliminates the ad-to-checkout price shock.
- **The delivery number.** Once you set the flat swap fee (M5), put it on the homepage. `Delivery is ৳120 per swap, both directions included.`
- **A service-area line.** `Currently delivering across Dhaka.` With a link to `/delivery` for the detail.
- **A real photograph of your actual books.** Not an illustration, not a stock image. One photo of your real shelf, mid-page, captioned honestly. This will do more for conversion than any headline test.
- **The Founding Member Guarantee (M6)**, stated near the CTA.
- **A founder line with a face.** One sentence and a photo. For a new brand asking for money in Bangladesh, an identifiable human is worth more than any badge.

## 8.5 Exact recommended landing page copy

### Above the fold

**Kicker**
```
Dhaka · Founding memberships open
```

**H1**
```
Borrow books.
Read them.
Send them back.
```

**Subheadline**
```
Sphotik is a membership library. Join once, then borrow as many books as you like from our collection. There is no fee for each book. Membership is ৳900 for six months, plus a refundable ৳500 deposit, so you pay ৳1,400 today and ৳500 of it comes back to you.
```

**Primary CTA button**
```
See membership options
```

**Secondary, as a text link below**
```
Or browse what is on the shelf right now
```

**Trust strip, four items**
```
No fee per book  ·  Deposit fully refundable  ·  Delivering across Dhaka  ·  Cancel in 14 days, fee refunded
```

### Section 2: How it works

**H2**
```
Five steps, and then it just repeats
```

```
01  Join
Choose six or twelve months. Place a refundable deposit. Your deposit decides how many books you can hold at once, not how much you pay.

02  Choose
Pick from what is actually on our shelf. If a book says available, a physical copy is sitting here right now waiting to be packed.

03  We send
We pack your books and courier them to you. However many books travel together, it counts as one shipment.

04  Read
Take your time. No per-day charges, no late-fee traps. If nobody is waiting for a book, you can extend it.

05  Swap
Tell us you are done. We arrange the pickup, and your next set goes out. Delivery is ৳120 per swap, covering both directions.
```

### Section 3: The money, plainly

**H2**
```
Exactly what you pay, and exactly what comes back
```

```
Membership, six months, founding price
৳900
This is the only fee. It does not change based on how many books you read. After our launch period this becomes ৳1,200.

Refundable security deposit
৳500, ৳1,000, or ৳1,500
This is not a fee. It sets how many books you can hold at one time, and it comes back to you in full once your books are returned and nothing is outstanding.

Delivery
৳120 per swap
Charged when you swap books, and it covers both the books coming to you and the books going back. Not included in membership.

What that means on day one
You pay ৳1,400. Of that, ৳500 is your deposit and it is yours.
```

### Section 4: The honest section

**H2**
```
Three things we would rather tell you now
```

```
We are new.
Sphotik opened in September 2026. Our founding members are joining a library that is still small, and we would rather say that than pretend otherwise.

Our catalogue is deliberately small.
We own a few hundred books, not a few thousand. Everything we list, we physically have. We would rather show you a short shelf that is true than a long one that is not.

Delivery is a real cost.
It is not included, and we do not hide it. ৳120 per swap, both directions, quoted before anything moves.
```

### Section 5: Founder

```
Why I built this

[Photo]

I kept buying books I read once. So did everyone I know. A library fixes that, if someone actually runs it well: books that are really in stock, arriving in good condition, with clear rules about money.

That is the whole idea. If it works for you, join. If something goes wrong, message me and I will fix it.

[Name], founder
[phone] · hello@sphotik.com.bd
```

### Section 6: FAQ

Pull six from your existing `homeFaqs`, but reorder them by objection strength, not by logic:

1. Is the deposit really refundable, and how do I get it back?
2. What happens if I damage or lose a book?
3. Do I pay a fee for each book?
4. How much is delivery, really?
5. What if I want to cancel?
6. What does "available now" mean?

Your existing answers for 3, 4, and 6 are already good. You need new ones for 1, 2, and 5, and the answer to 5 must reflect the new 14-day guarantee.

### Final CTA

**H2**
```
Your next read is on the shelf
```

```
Join as a founding member. ৳900 for six months, plus a ৳500 refundable deposit. If it is not for you, cancel within 14 days and we refund the membership fee.
```

**Button**
```
Become a founding member
```

Below it, small:
```
Read more. Own less.
```

## 8.6 Where should ads point?

[RECOMMENDATION] **Send all cold traffic to `/` with UTMs. Send all retargeting traffic to `/member`.**

The argument for `/member` on cold traffic is that it is closer to the money. The argument against, which I find stronger: your bottleneck is comprehension, not intent. A cold Bangladeshi user landing on a pricing page for a model they have never seen will bounce, because the page answers "how much" before answering "what." The homepage answers "what" first. That is the right order for a category that does not exist yet.

Retargeting is different. Those people already know what it is. Send them straight to the decision.

**Do not build a separate landing page.** With three creatives and USD 250, a separate LP adds a variable you cannot afford to test and a page you have to maintain. Your homepage, with the copy above, is enough.

---
---

# PART 9: Three campaign videos

Three angles, chosen because they attack the three different reasons someone will not buy: **it costs too much** (economic), **I do not understand it** (cognitive), and **I do not trust you** (social).

| Code | Title | Trigger | Attacks |
|---|---|---|---|
| `V1-MATH` | The Shelf | Loss aversion, concrete arithmetic | Price objection |
| `V2-HOW` | How It Actually Works | Clarity, curiosity resolution | Comprehension objection |
| `V3-WHY` | Why I Built This | Reciprocity, identifiable person | Trust objection |

All three: **9:16 vertical, 1080×1920**, exported also at 1:1 and 4:5. Burned-in subtitles, always, because most feed viewing is silent. Total production budget if you shoot on a phone: ৳0 to ৳8,000.

---

## VIDEO 1: `V1-MATH`: "The Shelf"

**Strategic objective:** Convert price-sensitive readers by making the cost of *not* joining concrete and visible. Make ৳900 feel obviously smaller than the number it replaces.

**Psychological trigger:** Loss aversion combined with the endowment paradox. People do not respond to "save money." They respond to seeing money they already spent, wasted, in physical form. The books on the shelf **are** the wasted money, and they are visible.

**Ideal duration:** 42 seconds.

**Language:** **Bangla voiceover, English on-screen numbers.** Money reasoning happens in the mother tongue; numerals are read faster in Western digits by this audience. Mixing them is normal Dhaka speech, not a compromise.

**First 3 seconds:** A hand pulls one book off a shelf. Hard cut to the price sticker still on the back. On-screen: `৳480`. No talking yet. The sticker is the hook.

### Hook (spoken, 0:00 to 0:04)
> "এই বইটা পড়েছি একবার। চারশো আশি টাকা।"
>
> *(I read this book once. Four hundred and eighty taka.)*

### Full script

| Time | Visual | Voiceover (Bangla) | On-screen text |
|---|---|---|---|
| 0:00-0:04 | Hand pulls a book from a shelf. Cut to price sticker on the back cover, tight macro. | "এই বইটা পড়েছি একবার। চারশো আশি টাকা।" | `৳480` |
| 0:04-0:09 | Second book pulled, sticker. Third book, sticker. Quick, rhythmic. | "এটাও একবার। এটাও।" | `৳520` then `৳390` |
| 0:09-0:14 | Wide shot: a shelf, maybe forty books. Slow push in. | "গত এক বছরে বই কিনেছি প্রায় বারো হাজার টাকার। পড়েছি একবার করে। এখন শুধু তাকিয়ে থাকে।" | `৳12,000 in one year` |
| 0:14-0:19 | Hard cut. Black frame, single line of text. Silence for half a beat. | *(silence, then)* "একটা লাইব্রেরিতে এই একই বইগুলো..." | `Same books. From a library.` |
| 0:19-0:27 | Clean product shot: a stack of five books on a plain surface, being tied with paper tape. | "সফটিক একটা মেম্বারশিপ লাইব্রেরি। ছয় মাসের মেম্বারশিপ নয়শো টাকা। প্রতি বইয়ের জন্য আলাদা কোনো ফি নেই।" | `৳900 for 6 months` then `No fee per book` |
| 0:27-0:34 | Hands opening a courier packet, books sliding out onto a table. Then the same packet being sealed to go back. | "বই কুরিয়ারে পৌঁছে যায়। পড়া শেষ হলে ফেরত যায়, আর নতুন বই আসে। যতবার খুশি।" | `We courier. You read. We collect. Repeat.` |
| 0:34-0:38 | Simple text card, brand typography, cream background. | "নয়শো টাকা মেম্বারশিপ, আর পাঁচশো টাকা জামানত, যেটা পুরোটাই ফেরতযোগ্য।" | `৳900 membership` / `+ ৳500 refundable deposit` |
| 0:38-0:42 | Logo, URL, CTA. | "ঢাকায় এখন ফাউন্ডিং মেম্বারশিপ খোলা।" | `sphotik.com` / `Founding memberships open in Dhaka` |

**Last 5 seconds:** the two prices on screen simultaneously, so the final frame is a direct comparison: `৳12,000 a year buying` versus `৳900 for six months borrowing`. Hold for a full two seconds. People screenshot final frames.

**Visual direction:** Warm, natural light. Real books with real Bangladeshi price stickers, which is a detail no stock footage will give you and which is instantly recognisable to your audience. Shallow depth of field. Handheld but steady. No stock footage of white people in libraries under any circumstances.

**Music and sound:** No music for the first 14 seconds. Only diegetic sound: pages, the shelf, tape tearing. Music enters at 0:19 when the solution appears, and it should be a warm acoustic bed, low, never rising above the voice. The silence in the first half is what makes the audio-off subtitle version work, and it is what makes it feel like a real person rather than an ad.

**Pacing:** Slow and heavy for 0:00 to 0:14 (the problem should feel a bit uncomfortable). Brisk from 0:19 (the solution should feel easy). Never frantic.

**CTA:** `Learn more` (Meta button) with the on-screen `sphotik.com`.

**Thumbnail text:** `৳12,000. Read once each.`

**What to avoid:**
- Any suggestion that buying books is stupid or wasteful as a *value judgement*. Your audience loves buying books. The message is "you cannot afford to buy all of them," not "buying is bad."
- Showing more than three price stickers. It becomes a list and loses its punch.
- Any mention of the deposit before 0:34. Introducing a second number too early kills the arithmetic.
- Fake or inflated prices. Use real stickers. Someone will check.

---

## VIDEO 2: `V2-HOW`: "How It Actually Works"

**Strategic objective:** Remove the comprehension barrier. This is your workhorse ad and, in my estimation, the most likely of the three to produce the actual paying members. [ESTIMATE]

**Psychological trigger:** Curiosity resolution combined with perceived risk reduction. A novel model creates an open loop; open loops are uncomfortable; the ad closes it. Watching a complete process end to end is also the cheapest form of proof that the process exists.

**Ideal duration:** 72 seconds. Longer than a normal cold ad, deliberately: anyone who watches 72 seconds of a mechanism explanation is pre-qualified in a way a 15-second viewer is not.

**Language:** **Bangla-led, mixed.** English for the process nouns your audience uses in English anyway ("membership," "deposit," "courier," "catalogue"). This is how people in Dhaka actually talk about services, and code-switching here reads as natural, not lazy.

**First 3 seconds:** Direct to camera, no preamble. "This is not a bookshop." Cut. "And it is not a rental." The double negation creates the open loop instantly.

### Hook (spoken, 0:00 to 0:05)
> "এটা বইয়ের দোকান না। ভাড়াও না। আসলে কীভাবে কাজ করে, দেখাচ্ছি।"
>
> *(This is not a bookshop. It is not a rental either. Let me show you how it actually works.)*

### Full script

| Time | Visual | Voiceover | On-screen text |
|---|---|---|---|
| 0:00-0:05 | Founder to camera, medium shot, real room with real shelves behind. | "এটা বইয়ের দোকান না। ভাড়াও না। আসলে কীভাবে কাজ করে, দেখাচ্ছি।" | `Not a bookshop.` / `Not a rental.` |
| 0:05-0:14 | Screen recording of `sphotik.com/member` on a phone, finger tapping the 6-month card. | "এক। মেম্বারশিপ নিন। ছয় মাস নয়শো টাকা, বারো মাস এক হাজার ছয়শো। এই ফি টা লাইব্রেরিতে ঢোকার জন্য, বইয়ের জন্য না।" | `01 · Join` / `৳900 / 6 months` |
| 0:14-0:26 | Screen recording, deposit selector. Then cut to physical: two books placed side by side, then four, then six. | "দুই। একটা সিকিউরিটি ডিপোজিট দিন। পাঁচশো টাকায় একসাথে দুইটা বই, এক হাজারে চারটা, দেড় হাজারে ছয়টা। এটা কোনো ফি না। বই ফেরত দিলে পুরো টাকাটাই ফেরত পাবেন।" | `02 · Deposit` / `৳500 = 2 books` / `৳1,000 = 4` / `৳1,500 = 6` / **`Fully refundable`** |
| 0:26-0:36 | Screen recording of the catalogue, scrolling. Cut to a hand pulling those exact books off a real shelf. | "তিন। ক্যাটালগ থেকে বই বেছে নিন। যেটাতে 'available' লেখা, সেটার একটা কপি এই মুহূর্তে আমাদের তাকে আছে। না থাকলে আমরা লিখি না।" | `03 · Choose` / `Available means it is physically here` |
| 0:36-0:46 | Real packing: books wrapped, taped, address label, handed to a courier rider at a door. | "চার। আমরা প্যাক করে কুরিয়ারে পাঠিয়ে দিই। কয়টা বই একসাথে যাচ্ছে সেটা ব্যাপার না, একটা শিপমেন্টই ধরা হয়।" | `04 · We send` |
| 0:46-0:58 | Someone reading on a sofa, tea, unhurried. Then the same books going back into a packet. | "পাঁচ। পড়ুন। শেষ হলে জানান, আমরা পিকআপের ব্যবস্থা করি, আর পরের সেট পাঠিয়ে দিই। প্রতিটা সোয়াপে ডেলিভারি একশো বিশ টাকা, আসা আর যাওয়া দুইটাই ধরা।" | `05 · Read, then swap` / `৳120 per swap, both ways` |
| 0:58-1:05 | Founder to camera again, same framing as the opening. | "প্রতি বইয়ের জন্য কোনো ফি নেই। মাসে একটা বই পড়ুন বা আটটা, মেম্বারশিপের দাম একই।" | `No fee per book. Ever.` |
| 1:05-1:12 | Text card, then logo and URL. | "ঢাকায় ফাউন্ডিং মেম্বারশিপ এখন খোলা। sphotik.com" | `Founding memberships · Dhaka` / `sphotik.com` |

**Last 5 seconds:** the numbered list appears all at once as five short lines, so a viewer who watched nothing can screenshot the whole model. This is the frame people will send to a friend.

**Visual direction:** Documentary, not commercial. Real hands, real tape, a real courier rider, a real doorway. Screen recordings must be of your **actual live site**, which also functions as proof the site exists. Consistent warm white balance. Founder appears at 0:00 and 0:58 in the same setup so the video is bookended by a person.

**Music and sound:** A single light, rhythmic acoustic bed at low volume throughout. Diegetic sound raised at the packing moments (tape, paper) because texture sells physicality. No voiceover music swells; the tone is competent, not exciting.

**Pacing:** Steady and even. Each of the five steps gets roughly equal weight. **Do not** speed up the boring parts; the boring parts are the proof. Resist the urge to add whip-pan transitions.

**CTA:** `Learn more`.

**Thumbnail text:** `Not a bookshop. Not a rental.`

**What to avoid:**
- Motion graphics or animated explainer style. It signals "agency-made ad," which reduces trust for a local startup. Physical footage beats animation here.
- Skipping the deposit. It is the most confusing part, so it gets the most screen time (twelve seconds, the longest single section).
- Any claim about catalogue size. Do not say "thousands of books." You do not have thousands of books, and your own homepage promises honest availability.
- Over-scripting the founder segments. Two takes, keep the more natural one, keep the small verbal stumbles.

---

## VIDEO 3: `V3-WHY`: "Why I Built This"

**Strategic objective:** Convert people who understand the offer and can afford it but do not trust an unknown brand with ৳1,400. Also serves as your primary retargeting creative and your Instagram pinned Reel.

**Psychological trigger:** The identifiable person effect and reciprocity. Trust does not transfer from logos, testimonials, or badges when a brand is three weeks old. It transfers from one identifiable human taking visible personal responsibility. Admitting a limitation out loud is the mechanism that makes the rest of the claims credible.

**Ideal duration:** 58 seconds.

**Language:** **Bangla, unmixed, except for the closing card.** This is the emotional one. Code-switching creates distance. Speak the way you would to a friend.

**First 3 seconds:** Founder, unpolished, in the actual room with the actual books, holding one. No graphics, no music, no logo. "I have three hundred and twelve books." A specific odd number is a trust device; "hundreds of books" is not.

### Hook (spoken, 0:00 to 0:05)
> "আমার কাছে তিনশো বারোটা বই আছে। এর মধ্যে আশিটা আমি কখনো দ্বিতীয়বার খুলিনি।"
>
> *(I have three hundred and twelve books. Eighty of them I have never opened a second time.)*

### Full script

| Time | Visual | Voiceover | On-screen text |
|---|---|---|---|
| 0:00-0:07 | Founder in the room, holding a book, seated. Natural light. Slightly wide, not a tight talking-head. | "আমার কাছে তিনশো বারোটা বই আছে। এর মধ্যে আশিটা আমি কখনো দ্বিতীয়বার খুলিনি।" | *(none for the first 7 seconds, deliberately)* |
| 0:07-0:17 | Slow pan across the actual shelves. | "প্রতিটা বই কেনার সময় মনে হয়েছিল, এটা রেখে দেব। কিন্তু সত্যি বলতে, বেশিরভাগ বই একবারই পড়া হয়। আর তারপর শুধু তাক দখল করে থাকে।" | *(none)* |
| 0:17-0:26 | Founder again. Direct eye contact. | "তাহলে সমস্যাটা কী? সমস্যা হলো, বেশি পড়তে চাইলে বেশি কিনতে হয়। আর বেশি কেনা বেশিরভাগ মানুষের পক্ষে সম্ভব না। এই কারণেই লাইব্রেরি জিনিসটা আবিষ্কার হয়েছিল।" | `Reading more should not mean buying more.` |
| 0:26-0:36 | Hands sorting and shelving books, labelling spines. Real work, real mess. | "তাই সফটিক শুরু করলাম। একটা মেম্বারশিপ লাইব্রেরি। আপনি জয়েন করেন, বই ধার নেন, পড়েন, ফেরত দেন, আবার নেন। প্রতি বইয়ের জন্য কোনো ফি নেই।" | `Sphotik · a membership library` |
| 0:36-0:47 | Founder, more serious register. Slight change in framing, slightly closer. | "একটা কথা সৎভাবে বলি। আমরা নতুন। আমাদের কালেকশন এখনো ছোট। ঢাকার বাইরে আমরা এখনো ডেলিভারি দিই না। কিন্তু যেটা আমরা লিখি, সেটা সত্যি। 'Available' মানে বইটা এই মুহূর্তে আমার তাকে আছে।" | `We are new.` / `Our shelf is small.` / `Dhaka only, for now.` / **`But what we list, we have.`** |
| 0:47-0:54 | Founder, hand gesture toward the shelves. Warmer. | "ফাউন্ডিং মেম্বারশিপ এখন খোলা। ছয় মাস নয়শো টাকা, সাথে পাঁচশো টাকা ফেরতযোগ্য জামানত। পছন্দ না হলে চোদ্দ দিনের মধ্যে বলুন, মেম্বারশিপের টাকা ফেরত দিয়ে দেব।" | `৳900 / 6 months` / `+ ৳500 refundable` / `14-day founding guarantee` |
| 0:54-0:58 | Text card only. Brand typography, cream ground. | *(silence)* | `Read more. Own less.` / `sphotik.com` |

**Last 5 seconds:** the brand line in silence. This is the only one of the three videos where "Read more. Own less." earns its place, because by 0:54 the viewer has the context that makes it a conclusion rather than an instruction.

**Visual direction:** The least produced of the three, on purpose. Single camera, tripod, natural window light, one room. **No colour grade beyond basic correction.** The polish level should read as "a person filmed this in their flat," because that is the claim. If it looks like an agency made it, the honesty section at 0:36 reads as a manipulation technique instead of a confession.

**Music and sound:** No music at all for the first 26 seconds. A very quiet piano or acoustic bed from 0:26, fading out entirely at 0:47 so the guarantee is spoken in silence. Room tone throughout; do not noise-reduce it into sterility.

**Pacing:** Unhurried. This video should feel like it has time. Let there be a half-second of air after "eighty of them I have never opened a second time." That pause is doing work.

**CTA:** `Learn more`.

**Thumbnail text:** `I own 312 books. 80 I never reopened.`

**What to avoid:**
- Any hint of a sales voice. If it sounds like an ad at 0:00, the trust mechanism fails completely.
- Music under the honesty section. Silence is what makes it read as sincere.
- Overselling the limitation into false modesty. State the three limits flatly and move on. Do not linger, do not apologise.
- Reading from a script on camera. Learn the four beats (I own too many books / most get read once / so I built a library / here is what we cannot do yet) and improvise the words. Shoot five takes.

---

## Production notes for all three

| Item | Spec |
|---|---|
| Aspect | 9:16 master at 1080×1920. Export 1:1 and 4:5 crops. |
| Subtitles | Burned in, always. Bangla VO gets Bangla subtitles plus a small English line for the key numbers. [SECONDARY: silent viewing is the majority case on mobile feeds.] |
| Safe zones | Keep all text out of the top 14% and bottom 20% for Reels and Stories UI overlays. |
| File | H.264 MP4, under 100 MB. |
| Frame rate | 30fps. Do not shoot 60fps and slow it down; it looks like a phone effect. |
| Shoot order | Shoot all three on the same day in the same room. Same light, same wardrobe, consistent brand feel. |
| Total shooting time | One day, phone camera, a cheap lav mic (~৳800) and a window. That is genuinely enough. |
| What to spend money on | The lav mic. Bad audio kills a founder video; a slightly soft image does not. |

---
---

# PART 10: Ad copy

Primary text is truncated at roughly 125 characters on mobile before "See more." The first sentence must survive alone. Every version below is built for that.

---

## `V1-MATH`: The Shelf

### Primary text, short (recommended for cold)
```
I spent about ৳12,000 on books last year. I read most of them exactly once.

Sphotik is a membership library in Dhaka. ৳900 for six months, and you can borrow as many books as you want. There is no fee for each book.

You place a refundable ৳500 deposit, which lets you hold two books at a time and comes back to you in full. Delivery is ৳120 per swap.

Founding memberships are open now.
```

### Primary text, long
```
I spent about ৳12,000 on books last year. I read most of them exactly once. They are on a shelf behind me right now, and they will probably stay there.

That is the problem Sphotik solves.

Sphotik is a membership library in Dhaka. You take a membership, you borrow books from our collection, you read them, you send them back, and you borrow again. There is no fee for each book you read. Read one book a month or eight, the membership costs the same.

Founding price: ৳900 for six months, or ৳1,600 for twelve.
Refundable deposit: ৳500 holds two books at a time, ৳1,000 holds four, ৳1,500 holds six. It comes back to you in full once your books are returned.
Delivery: ৳120 per swap, covering both the books coming to you and the books going back.

Three honest things. We are new, we opened in September 2026. Our catalogue is small on purpose, because everything we list we physically own. And we only deliver in Dhaka right now.

If it is not for you, cancel within 14 days and we refund the membership fee.

sphotik.com
```

### Headline (40 characters max, safe)
```
৳900 for six months of books
```
Alternate: `Stop buying books you read once`

### Description (link description, often hidden)
```
Membership library in Dhaka. No fee per book.
```

### CTA button
`Learn more`

---

## `V2-HOW`: How It Actually Works

### Primary text, short (recommended for cold)
```
This is not a bookshop and it is not a rental. Here is exactly how it works.

You take a membership. You place a refundable deposit, which decides how many books you can hold at once. You choose books from our catalogue, we courier them to you, you read them, and we collect them and send the next set.

There is no fee for each book. ৳900 for six months, ৳500 refundable deposit, ৳120 delivery per swap.

Dhaka only, for now.
```

### Primary text, long
```
This is not a bookshop and it is not a per-book rental. Here is exactly how Sphotik works.

Step one. You take a membership. ৳900 for six months at our founding price, or ৳1,600 for twelve. This fee is for access to the library, not for any specific book.

Step two. You place a refundable security deposit. ৳500 lets you hold two books at a time, ৳1,000 lets you hold four, ৳1,500 lets you hold six. This is not a fee. It comes back to you in full once your books are returned and nothing is outstanding.

Step three. You choose from our catalogue. If a book says available, a physical copy is sitting on our shelf right now. We do not list books we do not have.

Step four. We pack them and courier them to you. However many books travel together, it counts as one shipment.

Step five. You read, at your own pace. When you are done, tell us, we arrange the pickup, and your next set goes out. Delivery is ৳120 per swap and covers both directions.

That is the whole model. No fee per book, no late-fee traps, no per-day charges.

Founding memberships are open in Dhaka now. sphotik.com
```

### Headline
```
Borrow books. Read. Send back. Repeat.
```
Alternate: `A library you can join, in Dhaka`

### Description
```
৳900 for 6 months. No fee per book.
```

### CTA button
`Learn more`

---

## `V3-WHY`: Why I Built This

### Primary text, short (recommended for retargeting)
```
I own 312 books. Eighty of them I have never opened a second time.

That is why I started Sphotik, a membership library in Dhaka. You join, you borrow, you read, you send them back, you borrow again. No fee for each book.

We are new and our shelf is still small. But everything we list, we physically have.

৳900 for six months plus a refundable ৳500 deposit. Cancel within 14 days and we refund the fee.
```

### Primary text, long
```
I own 312 books. Eighty of them I have never opened a second time.

Every one of them felt worth buying at the time. But most books get read once, and then they just take up shelf space. Reading more should not mean buying more, and for most people it cannot.

That is why libraries exist. So I started one, run the way I would want one run.

Sphotik is a membership library in Dhaka. You take a membership, borrow books from our collection, read them at your own pace, send them back, and borrow again. There is no fee for any individual book.

Let me be honest about where we are. We are new, we opened this month. Our collection is a few hundred books, not a few thousand. And we only deliver inside Dhaka right now.

But what we list, we have. When our catalogue says a book is available, a physical copy is on our shelf, and we will pack it for you.

Founding membership is ৳900 for six months, plus a ৳500 refundable security deposit. Delivery is ৳120 per swap. If it is not what you hoped for, tell us within 14 days and we will refund the membership fee.

Read more. Own less.

sphotik.com
```

### Headline
```
I own 312 books. So I built a library.
```
Alternate: `A library run by someone you can call`

### Description
```
Founding memberships open in Dhaka.
```

### CTA button
`Learn more`

---

## Copy rules that apply to all nine variants

1. **The first sentence must work alone.** It is the only guaranteed-visible part.
2. **Numbers before adjectives.** "৳900 for six months" beats "affordable membership."
3. **State Dhaka in every ad.** Every impression outside your service area is wasted money, and a self-selecting geographic statement in the copy does filtering that targeting alone will not.
4. **Never advertise ৳1,400.** Advertise ৳900. Disclose ৳1,400 on the landing page above the fold. The ad's job is to earn a click; the page's job is to be complete.
5. **No emoji.** They are wrong for the brand and they signal a different price tier.
6. **No urgency you cannot back up.** No "last 3 spots," no fake countdowns. "Founding memberships are open now" is true and sufficient.
7. **No em dashes.** Commas, colons, semicolons, parentheses, regular hyphens.
8. **Never claim more books than you have.** Your entire positioning is honest availability. One inflated number destroys it.

---
---

# PART 11: Audience strategy

## 11.1 The recommendation, up front

**One ad set. Dhaka. Ages 22 to 45. All genders. Advantage+ audience ON, seeded with four reading interests. Automatic placements. No language restriction. No exclusions except purchasers.**

That is the whole prospecting audience. Everything below is the argument for why it should not be more complicated than that.

## 11.2 Why broad, and where broad is actually wrong

The standard 2026 advice is "go broad, let the algorithm find them." That advice is correct for accounts with conversion history and wrong for accounts with none, and yours has none. Meta's delivery system optimises toward your optimisation event using signals from your pixel; with zero prior conversions it has nothing account-specific to work from, and pure-broad on a small budget in a large market tends to spend on the cheapest available impressions.

But the opposite move, tight interest stacking, is worse for a different reason. [PLATFORM] Under the unified campaign structure Meta shipped in 2025 and completed in the February 2026 Ads Manager overhaul, detailed targeting functions as a **signal rather than a hard constraint** when Advantage+ audience is on; only location, minimum age, language, and exclusions remain true limits. So a narrow interest stack does not actually narrow delivery the way it used to. What it does is give the algorithm a starting prior, and then get expanded past anyway.

**Therefore: seed, do not cage.** Give Meta four to six high-signal interests as a starting point, leave Advantage+ audience on, and let it expand. You get the cold-start benefit of a prior without the fragmentation cost of trying to enforce it.

## 11.3 Exactly what to enter

| Setting | Value | Why |
|---|---|---|
| Location | `Dhaka, Dhaka Division, Bangladesh` (city) | See Part 12 |
| Age | `22 - 45` | Below 22, discretionary spend of ৳1,400 is rare and parental payment adds friction. Above 45, Facebook usage patterns shift and reading-purchase behaviour is harder to reach efficiently. [ASSUMPTION, worth revisiting.] |
| Gender | All | Do not pre-judge. Let the data tell you. |
| Language | **Leave blank** | Setting `Bengali` restricts to people whose Facebook interface language is Bangla. Your target reader, an educated urban Dhaka book buyer, very often runs Facebook in English. Setting language here would cut a large part of your best audience. **This is the most common targeting mistake made in Bangladesh.** |
| Detailed targeting | 4 to 6 interests, see below | Seed only |
| Advantage+ audience | **ON** | Lets Meta expand past the seed |
| Advantage+ placements | **ON** (automatic) | See Part 14 |
| Exclusions | `WEB-PURCHASE-180D`, `WEB-CLAIMED-180D` | Never pay to re-acquire someone who already paid |

### Interest seeds, in priority order

Check availability in the Ads Manager audience browser; Bangladesh interest inventory varies.

1. **Books** (broadest, highest confidence)
2. **Reading** or **Books and literature**
3. **Rokomari.com** (if available; this is the strongest single behavioural proxy for "buys books online in Bangladesh")
4. **Bengali literature** or **Bangla literature**
5. **Humayun Ahmed** (by far the highest-volume Bangladeshi author interest)
6. **Goodreads** (skews English-reading, educated, exactly your segment)

Add these as a **single OR group** in one detailed-targeting box. Do **not** use "Narrow audience" to AND them together. Narrowing at this budget produces an audience too small to learn from.

## 11.4 What NOT to target, and why

| Do not target | Why not |
|---|---|
| **Language = Bengali** | See above. Cuts your best segment. |
| **"Students" behaviour or education-status targeting** | The category is enormous and captures a population where ৳1,400 of discretionary spend is uncommon. If students turn out to convert, you will discover it from your own data (you collect `area`, and you can ask). Do not pre-load the algorithm with them. |
| **Job title or employer targeting** | Meta's employment data in Bangladesh is self-reported, sparse, and stale. |
| **Income or household-value targeting** | Not meaningfully available for Bangladesh. |
| **Parents / household composition** | Tempting for a "children's books" angle, but you are not selling children's books and it would fragment a tiny budget. |
| **Islamic content interests** | There is a genuinely large Islamic-books market in Bangladesh and it may well be a real opportunity. But it needs its own creative, its own catalogue depth, and its own test. Adding it as a seed to a generic ad set would muddy your signal without serving that audience properly. Note it as a Phase 2 test. |
| **Detailed targeting expansion set to OFF** | You want expansion on at this budget. |
| **More than 6 interests** | Diminishing returns and it makes your seed meaningless. |
| **Any second ad set with a different interest stack** | See below. |

## 11.5 Why not to split into multiple interest ad sets

You will be tempted. Do not.

[FACT] Meta's guidance is that an ad set needs roughly 50 optimisation events in a 7-day window to exit the learning phase. [ESTIMATE] Your entire three-week test is likely to produce somewhere between 5 and 25 verified purchases in total. If you split that across three interest ad sets, each one gets 2 to 8 events over three weeks. Each will sit in Learning Limited permanently, each will have wide confidence intervals, and the "winner" you pick at the end will be noise.

There is also a budget-mechanics reason. Meta's minimum viable daily budget scales with your target cost per result. With a plausible cost per member in the ৳1,500 to ৳2,500 range [ESTIMATE], an ad set spending ৳400 a day produces well under one result a day. Three such ad sets produce well under one result a day between them, but with three times the variance.

**One ad set. Three ads inside it. Meta will allocate impressions between the ads automatically, and that comparison, at the ad level within one ad set, is the only creative comparison you can afford to make.**

## 11.6 Lookalikes

**Do not build any. Not now, not at the end of this test.**

[PLATFORM] A lookalike source audience needs a meaningful minimum number of people (Meta's documented guidance has long been at least 100 from a single country, with better results well above that). Even at the top of my estimate you will finish this test with fewer than 30 verified members. A lookalike built from 20 people is a lookalike of 20 people's Facebook behaviour, not of your customer profile, and it will confidently deliver you 20 people's worth of noise.

**When to revisit:** at 200+ verified members, build a 1% lookalike from the `Purchase` audience, and even then run it as an addition to broad, not a replacement.

An intermediate option that is genuinely useful earlier: at around 100 members, upload your customer list (hashed phone numbers from your Google Sheet) as a Customer List custom audience and build the lookalike from that instead of from pixel data. Customer-list lookalikes tend to be higher quality because the source is verified rather than inferred.

## 11.7 Retargeting audiences

Covered in Part 19. The short version: build them on day zero, turn them on around day six, spend USD 2 per day, and use different creative from prospecting.

---
---

# PART 12: Geography

## 12.1 The recommendation

**Phase 1: Dhaka city only. Nothing else.**

In Ads Manager, set the location to `Dhaka, Dhaka Division, Bangladesh` with the type set to **`People living in this location`**, not the default "People living in or recently in this location." The default includes travellers and people passing through, and you cannot deliver books to a person who is in Dhaka for a wedding.

## 12.2 Why Dhaka only, when your courier covers the whole country

Private couriers in Bangladesh (Steadfast, RedX, Pathao Courier, Paperfly, eCourier, Sundarban) will deliver anywhere. So technically you *can* serve the whole country. Here is why you should not, in order of how much each one will hurt you.

**1. Return logistics are the real constraint, not outbound.** Sending a book to Rangpur is easy. Getting it back, in good condition, on a predictable timeline, from a member who has no particular incentive to hurry, is hard. Every day a book is in transit or sitting in someone's flat is a day it is not earning. Outside Dhaka, a full swap cycle plausibly runs 8 to 12 days instead of 3 to 5. [ESTIMATE] That roughly halves your effective inventory turns.

**2. Delivery economics invert.** Intra-Dhaka courier is meaningfully cheaper than nationwide. [ESTIMATE: intra-city ৳60 to ৳80, nationwide ৳110 to ৳150 per leg, based on published courier rate cards. Verify with your chosen courier.] At a ৳120 flat swap fee, Dhaka is roughly break-even and nationwide loses you money on every swap.

**3. Damage and loss risk rises with distance and handling.** Books are heavy, soft-cornered, and moisture-sensitive. More hubs means more handling.

**4. You cannot recover a bad experience remotely.** In Dhaka, if something goes badly wrong with one of your first fifty members, you can physically go and fix it. That capability is worth more than the incremental members you would get from opening up nationally, and it is exactly what the first-100 phase is for (Part 24).

**5. Reader density.** Dhaka concentrates the universities, the English-medium schools, the corporate offices, the bookshops, and the literary scene. [ASSUMPTION, but a safe one.] Your best customers are geographically clustered.

## 12.3 Should you narrow inside Dhaka?

**No, not with USD 250.**

The temptation is to pin-drop Dhanmondi, Gulshan, Uttara, and Bashundhara, since that is where your buyers most likely are. Two arguments against.

First, radius targeting in Dhaka is imprecise. Population density is extreme and Meta's location inference for a densely packed city is not reliable enough to cleanly separate Dhanmondi from Hazaribagh. You will exclude real buyers and include people you meant to exclude.

Second, and more importantly, **you get the filtering for free elsewhere.** Your ad copy says Dhaka. Your landing page says Dhaka. Your checkout collects `area` before payment, and your `DetailsStep` already tells the user "If we cannot deliver to your area yet, we will tell you before taking any payment." So an out-of-area person costs you one click, not one failed fulfilment. That is a cheap filter, and it produces data: if you get twelve signups from Narayanganj, that is a market signal you would never have seen with tight geo-targeting.

**What to do instead:** track `area` in your Google Sheet religiously. After three weeks you will have a map of where your actual demand is, drawn from your real buyers rather than your assumptions. That map is worth more than any pre-launch geographic hypothesis.

## 12.4 The expansion sequence, if this works

Do not run these during the USD 250 test.

| Phase | Geography | Trigger to open it |
|---|---|---|
| 1 | Dhaka city | Now |
| 2 | Dhaka metro: add Savar, Keraniganj, Narayanganj, Gazipur | 50+ Dhaka members and a swap cycle reliably under 6 days |
| 3 | Chattogram | 150+ members, and only with a local pickup arrangement |
| 4 | Sylhet, Khulna, Rajshahi | Proven Chattogram unit economics |
| 5 | Nationwide, outbound only, longer loan windows to absorb transit | A model where transit time is priced in |

[RECOMMENDATION] When you do open a second city, treat it as a **new experiment with its own budget and its own ad set**, not as an expansion of the Dhaka ad set. Different cities have different courier realities and blending them hides which one works.

## 12.5 What you should not do

- Do not target "Bangladesh" as a country. You will spend a large fraction of your budget on people you cannot serve.
- Do not add Kolkata or any other Bengali-speaking region outside Bangladesh. Payment, courier, and customs all break.
- Do not target Bangladeshi diaspora audiences. They cannot use the service and they will engage with your ads (driving up your CTR and destroying your relevance signal) without ever converting. This is a genuine and underappreciated risk for Bangladeshi brands, because diaspora engagement is high and worthless here.

---
---

# PART 13: Campaign objective

## 13.1 The comparison

| Objective | What Meta optimises for | Fit for your validation goal |
|---|---|---|
| **Awareness** | Reach, ad recall lift | **No.** Measures memory, not money. You would learn nothing about willingness to pay. |
| **Traffic** | Link clicks or landing page views | **No, and it is the trap.** It will produce cheap, plentiful clicks and a beautiful-looking dashboard. Meta will find people who habitually click, who are not people who habitually pay. You would end the test with 3,000 clicks, 2 members, and no idea why. |
| **Engagement** | Post engagement, video views, messages | **No** for the main test. Cheap video views optimise for people who watch, and watching is free. |
| **Leads** | Form fills or messages | **Not as the main campaign.** A lead form would get you a big list of email addresses from people who will never pay ৳1,400. It answers "will people express interest," which you already know the answer to. It does not answer your actual question. Worth considering as a **fallback**, see 13.5. |
| **Sales** | Purchases or another conversion event | **Yes.** This is the answer, but the reasoning matters more than the answer. |

## 13.2 Why Sales, and the honest problem with it

**Use the Sales objective.** But you asked me not to just say that, so here is the problem you are walking into.

[FACT] Meta's guidance is that an ad set needs approximately **50 optimisation events per 7-day window** to exit the learning phase and deliver stably.

Here is what your budget actually buys. [ESTIMATE, and every number is arguable]

| Step | Assumption | Result |
|---|---|---|
| Delivered ad spend | after ~15% VAT on ৳30,500 | ~৳26,000 |
| CPM, cold conversion campaign, Dhaka | ৳150 | 173,000 impressions |
| Link CTR | 1.2% | ~2,080 link clicks |
| Landing page view rate | 80% of clicks | ~1,660 LPVs |
| LPV → membership page | 30% | ~500 |
| Membership page → checkout start | 20% | ~100 |
| Checkout start → payment claimed | 25% | ~25 |
| Payment claimed → verified | 80% | **~20 verified members** |

[SECONDARY: the ৳150 CPM and 1.2% CTR sit inside published Bangladesh benchmark ranges, which cluster around ৳100 to ৳300 CPM and ৳8 to ৳40 CPC for 2026. Wide ranges; treat as a scenario, not a forecast.]

Twenty purchases over three weeks is about **7 per week**. You need 50. **You will not exit the learning phase. Not at any point in this test, with any configuration.**

That is not a fixable problem. It is a budget-size fact. So the question is not "how do I exit learning," it is **"what should I do given that I cannot?"**

## 13.3 The answer: accept Learning Limited, and design for *your* learning

**Recommended configuration:**

- **Objective:** Sales
- **Conversion location:** Website
- **Conversion event:** `Purchase` (which, in your architecture, means the server-side verified event)
- **Bid strategy:** Highest volume (lowest cost). **Not** cost cap, not bid cap, not ROAS goal, all of which need volume you will not have.
- **Attribution setting:** 7-day click, 1-day view (the default). Do not change it.

**Why optimise for `Purchase` despite the volume problem:**

1. **Meta's delivery system is not solely dependent on your account's event count.** It uses cross-account priors, conversion modelling, and Advantage+ audience signals. Learning Limited means "delivery may be less stable and more expensive," not "the optimisation event is ignored." You will still get delivery biased toward likely converters, just noisily.

2. **Optimising for a proxy actively corrupts your experiment, which is the thing you said you must avoid.** If you optimise for `InitiateCheckout`, Meta finds people who start checkouts. Starting a checkout is free. You will get a nice number of checkout starts, near-zero purchases, and a completely unanswerable question: was the offer wrong, or did the algorithm just find tyre-kickers? Optimising for the real event keeps the experiment clean, and a clean negative result is worth far more to you than a dirty positive one.

3. **The single question you are paying to answer is "will strangers pay?"** Every step away from `Purchase` as the optimisation event is a step away from that question.

4. **Switching optimisation events resets learning and invalidates comparison.** With three weeks total, you cannot afford two resets.

## 13.4 The one condition under which you should step down

There is exactly one diagnostic pattern that justifies moving to `InitiateCheckout` optimisation, and it is narrow:

> **You have accumulated 40 or more `InitiateCheckout` events and zero `Purchase` events, AND your landing-to-checkout funnel rates look healthy.**

That combination says people understand you, want it, and start paying, but the algorithm cannot find the subset who finish. That is a genuine optimisation problem and stepping down may help.

**Every other zero-purchase pattern is not an optimisation problem.** If you have 1,500 landing page views and 6 checkout starts, the algorithm is not the issue. Your offer, your price, or your page is the issue. Switching optimisation events would produce more traffic and hide the actual finding. That is corrupting the experiment.

If you do step down, do it by **duplicating the ad set with the new event and pausing the old one**, not by editing the live ad set. Editing resets learning on the existing ad set and loses the comparison; duplicating gives you a clean before-and-after.

## 13.5 The fallback if the answer is "no"

If the test says people will not pay ৳1,400 to a stranger from a cold ad, that is a genuinely useful result and it does not mean the business is wrong. It may mean **paid social cold traffic is the wrong first channel for a novel, trust-heavy, high-consideration purchase.**

In that case the next experiment (a separate budget, a separate decision, after this test concludes) is a **Leads campaign with an instant form** collecting name, phone, and area, followed by you personally calling every lead. That converts a ৳1,400 cold decision into a warm conversation. It does not scale, and it is not supposed to; it is how you get your first hundred members while you learn what actually persuades people. See Part 24.

**Do not run this simultaneously with the Sales test.** Two campaigns on USD 250 gives you two underpowered experiments instead of one adequately powered one.

## 13.6 Advantage+ Sales specifically

[PLATFORM] Advantage+ is not a seventh objective. It is a **campaign state** inside the Sales objective, reached when three automation levers are all enabled: campaign-level budget, Advantage+ audience, and Advantage+ placements. [FACT: Meta's unified Marketing API structure, announced 29 May 2025, determines Advantage+ status through exactly these three settings; the February 2026 Ads Manager overhaul merged the separate "Manual" and "Advantage+" creation flows into one.]

**In practice, the setup I recommend in Part 14 will qualify as Advantage+ anyway** (campaign budget, Advantage+ audience on, automatic placements). That is fine. What matters is that you understand you are not choosing between "Advantage+" and "manual"; you are choosing which toggles to leave on.

**One caution:** [SECONDARY] Advantage+ Sales performs best with a large creative bank (10 to 50 assets) and steady purchase volume. You have 3 assets and no volume. So do not expect the ROAS uplift that Advantage+ case studies advertise. You are using it because the automation defaults are sensible for a beginner, not because it is magic.

---
---

# PART 14: Exact campaign structure

## 14.1 The structure

```
CAMPAIGN: SALES-PROSPECT-DHK-2609
├── Objective: Sales
├── Budget: Campaign budget (Advantage campaign budget), daily
├── Bid strategy: Highest volume
├── Special ad category: None
│
└── AD SET: BROAD-2245-AUTO
    ├── Conversion location: Website
    ├── Dataset: Sphotik - Web
    ├── Conversion event: Purchase
    ├── Location: Dhaka, Bangladesh (People living in this location)
    ├── Age: 22-45 · Gender: All · Language: (blank)
    ├── Detailed targeting: 4-6 reading interests (OR group)
    ├── Advantage+ audience: ON
    ├── Placements: Advantage+ (automatic)
    ├── Exclusions: WEB-PURCHASE-180D, WEB-CLAIMED-180D
    ├── Schedule: Run continuously, no dayparting
    │
    ├── AD: V1-MATH-A   (video 1, short primary text)
    ├── AD: V2-HOW-A    (video 2, short primary text)
    └── AD: V3-WHY-A    (video 3, short primary text)


CAMPAIGN: SALES-RETARGET-DHK-2609        [start Day 6, not Day 1]
├── Objective: Sales
├── Budget: Campaign budget, daily
│
└── AD SET: WARM-ALL-30D
    ├── Conversion event: Purchase
    ├── Location: Dhaka, Bangladesh
    ├── Age: 22-45
    ├── Custom audiences (OR): WEB-ALL-30D, VID-25PCT-30D, SOC-ENGAGE-90D
    ├── Advantage+ audience: OFF (retargeting must stay contained)
    ├── Placements: Advantage+ (automatic)
    ├── Exclusions: WEB-PURCHASE-180D, WEB-CLAIMED-180D
    │
    ├── AD: V3-WHY-RT   (founder video, objection-handling copy)
    └── AD: STATIC-FAQ  (single image, deposit and refund explained)
```

**Two campaigns. Two ad sets. Five ads. That is the entire account.**

## 14.2 Why this shape and not another

**Why campaign budget rather than ad set budget?** With one ad set per campaign it makes almost no difference mechanically, but campaign-level budget is one of the three conditions for Advantage+ state, it makes adding a second ad set later non-disruptive, and it means you change budget in one place.

**Why one ad set with three ads, rather than three ad sets?** Because Meta allocates impressions between ads *within* an ad set automatically and continuously, based on early performance. That is the only creative test you can afford. Three separate ad sets would each need their own learning, their own budget, and their own 50 events. See Part 11.5.

**Why automatic placements?** Two reasons. Manual placement selection at this budget is a way to make your CPM worse for no benefit; Reels and Stories inventory in Bangladesh is generally cheaper than Feed and your vertical video is built for it. And placement selection would disqualify the campaign from Advantage+ state. [FACT: Advantage+ placement state "prohibits any placement targeting or exclusions."] The one thing to check: after a few days, look at the placement breakdown, and if Audience Network is consuming meaningful spend with zero results, that is a signal worth acting on in a duplicated ad set, not an edit.

**Why no dayparting?** Two reasons. Meta's delivery system already models time-of-day. And restricting hours shrinks your daily budget's effective auction pool, which at ৳1,000 per day makes delivery lumpier.

**Why exclude `WEB-CLAIMED-180D` as well as `WEB-PURCHASE-180D`?** Because someone who claimed a payment but has not been verified yet is either a real member awaiting confirmation or a false start. Either way, showing them a cold acquisition ad is wrong.

**Why is the retargeting campaign separate rather than a second ad set in the same campaign?** Because with a shared campaign budget, Meta would shift almost all spend to the retargeting ad set (warm audiences look cheaper per conversion), starving your prospecting. Separate campaigns mean separate, protected budgets. This is the single most common structural mistake made by beginners running prospecting and retargeting together.

## 14.3 Ad-level settings

For all five ads:

| Setting | Value |
|---|---|
| Identity | Facebook Page `Sphotik`, Instagram account `@sphotik.bd` |
| Format | Single video (or single image for `STATIC-FAQ`) |
| Destination | Website |
| URL, prospecting | `https://sphotik.com` |
| URL, retargeting | `https://sphotik.com/member` |
| Display link | `sphotik.com` |
| URL parameters | The UTM block from Part 3 |
| Call to action | `Learn more` |
| Advantage+ creative enhancements | **Turn OFF** text improvements, image brightness, and music. **Leave ON** only automatic aspect-ratio adaptation. |

The Advantage+ creative point matters more than it looks. Those enhancements rewrite your copy, add filters, and overlay music. Your entire brand position is calm, precise, and honest; an auto-generated exclamation mark or a stock music bed undoes it. Also, if Meta rewrites your text, you no longer know what you tested.

## 14.4 Schedule

- Start: **Saturday**, at 00:00 Dhaka time. Saturday is the start of the Bangladeshi working week; launching then gives you five full weekdays before the first weekend and cleaner day-over-day comparison.
- Run continuously.
- No end date on the campaign; you control it with the phase budgets in Part 15.

## 14.5 What NOT to build

- No campaign budget optimisation across prospecting and retargeting. See above.
- No A/B test tool (Ads Manager `Experiments`). Split testing needs statistical power you do not have; it would halve your already-insufficient volume per cell.
- No dynamic creative. It generates combinations you cannot interpret at 20 conversions.
- No catalogue ads. You do not have a product catalogue and individual books are the wrong unit of sale.
- No Messenger or WhatsApp conversion location. Your funnel is a website funnel; sending people to chat means you personally answer every message, which does not scale and, more importantly, changes what you are testing.
- No second geography, no second offer, no second landing page.

---
---

# PART 15: The USD 250 budget plan

## 15.1 Money reality first

| Line | Amount |
|---|---|
| Budget you have | USD 250 |
| In taka at ৳122 | ~৳30,500 |
| Less ~15% Bangladesh VAT | ~ -৳4,000 |
| **Delivered ad spend available** | **~৳26,500 (USD ~217)** |
| **Planned delivered spend across all three phases** | **~৳22,000 (USD ~180)** |
| **Reserve, deliberately unspent** | **~৳4,500 (USD ~37)** |

The reserve is not padding. It is there so that when you find something that works on day 16, you have money to press it, and so that a billing surprise does not end your test.

**You are not obliged to spend all of it.** Stopping at ৳8,000 with a clear negative answer is a better outcome than spending ৳26,500 to get the same answer more slowly.

## 15.2 Phase plan

All budgets below are **delivered spend**. Ads Manager will show these numbers; your bank will show ~15% more.

---

### PHASE 0: Preparation (Days -3 to -1). Budget: ৳0

**What is running:** nothing.

**What you do:**
- Complete Steps 0 through 15 of Part 2.
- Publish 4 to 6 organic posts on the Page and 6 on Instagram.
- Run 3 to 5 test checkouts end to end and verify against every line of Part 5.
- Send the landing page to five people who have never heard of Sphotik. Ask each one, after eight seconds, "what is this and what does it cost?" **If three of five cannot answer both, do not launch.** Fix the page first. This costs nothing and is the highest-return thirty minutes in this entire document.
- Create all seven custom audiences so they start collecting.

**Decision:** Go / no-go on the Part 5 checklist. Any red line is a no-go.

---

### PHASE 1: Signal (Days 1 to 5). Budget: ৳1,200/day × 5 = ৳6,000 (USD ~50)

**What is running:** `SALES-PROSPECT-DHK-2609`, one ad set, three ads. Nothing else.

**Purpose:** find out whether cold Dhaka traffic engages at all and whether the funnel physically works with real strangers.

**What to inspect, once per day, at the same time, for no more than ten minutes:**

| Metric | Where | Healthy range [ESTIMATE] |
|---|---|---|
| Impressions delivering | Ads Manager | Budget spending fully by end of day |
| CPM | Ads Manager | ৳100 to ৳250 |
| Link CTR | Ads Manager | above 1.0% |
| Landing page views | Ads Manager | 75%+ of link clicks |
| `ViewMembership` count | Events Manager | 25%+ of LPVs |
| `InitiateCheckout` count | Events Manager | any at all by day 3 |
| Rows in your order sheet | Google Sheet | any at all by day 5 |

**What NOT to do in Phase 1:**
- Do not pause any ad before the end of day 3, no matter how it looks on day 1.
- Do not change the budget.
- Do not edit the ad set. Any edit restarts learning.
- Do not look at the dashboard more than once a day. [This is a real instruction, not a joke. Founders lose more money to panic edits than to bad targeting.]

**Decision gate 1, end of Day 5:**

| Condition | Action |
|---|---|
| Link CTR ≥ 1.0% **and** ≥ 25 `ViewMembership` **and** ≥ 3 `InitiateCheckout` | **Proceed to Phase 2 as planned.** The funnel is alive. |
| Link CTR ≥ 1.0% but ≤ 1 `InitiateCheckout` | **Proceed to Phase 2, but rewrite the landing page above the fold first.** People are interested and the page is losing them. Traffic works, conversion does not. |
| Link CTR < 0.6% | **Pause all three ads. Do not proceed.** Nobody is responding to the creative. Re-cut the hooks (see Part 20) and relaunch Phase 1 with the same budget. Spend ৳0 more until you do. |
| Zero landing page views despite clicks | **Stop everything.** Your site is broken, slow, or blocked in the Facebook in-app browser. Diagnose before spending another taka. |
| CPM above ৳400 | **Pause.** Something is wrong: a policy flag, an unhealthy Page, or an audience too small. Check the ad account quality page under `Account Quality`. |

---

### PHASE 2: Read (Days 6 to 12). Budget: ৳1,400/day prospecting + ৳250/day retargeting × 7 = ৳11,550 (USD ~95)

**What is running:**
- `SALES-PROSPECT-DHK-2609`, same ad set, but now with underperformers paused if the rules in Part 28 are met.
- `SALES-RETARGET-DHK-2609` switched on, but **only if `WEB-ALL-30D` has reached at least 700 people.** If it has not, keep that ৳250/day in the prospecting campaign and check again on day 9.

**Purpose:** get to a real number of members and find out what your CAC actually is.

**What to inspect, daily:** everything from Phase 1, plus:

| Metric | Source |
|---|---|
| Verified members | Your Google Sheet, `status = verified` |
| CAC | delivered spend ÷ verified members |
| Which `utm_content` produced them | Your Google Sheet |
| Checkout abandonment | `InitiateCheckout` minus `PaymentClaimed`, from Events Manager |
| Which deposit tier people choose | Your Google Sheet |
| Which plan (6m vs 12m) | Your Google Sheet |

**What to change in Phase 2:**
- **On Day 8:** pause any ad with over ৳2,500 spent, zero `InitiateCheckout`, and a CTR below half the best ad's CTR. See Part 28.
- **On Day 10:** if one video is clearly ahead on cost per `InitiateCheckout`, do nothing yet. Meta is already shifting impressions toward it. Let it.
- Update your landing page **only if** Phase 1 gate 1 told you to, and **only once**. Changing the page mid-phase makes everything before and after incomparable.

**Decision gate 2, end of Day 12:**

| Condition | Action |
|---|---|
| **≥ 5 verified members and CAC ≤ ৳1,500** | **Strong.** Go to Phase 3A: raise prospecting to ৳1,800/day and spend the reserve. |
| **2 to 4 verified members, CAC ৳1,500 to ৳3,000** | **Ambiguous, which is the most likely outcome.** Go to Phase 3B: hold budget, fix the single worst funnel step, run the full week. |
| **1 verified member, or ≥ 15 `InitiateCheckout` with 0 purchases** | **Checkout or payment problem, not a demand problem.** Pause ads for 24 hours. Do a real end-to-end purchase on a phone over mobile data, through the Facebook in-app browser, actually sending money. Fix what you find. Then Phase 3B. |
| **0 verified members, < 5 `InitiateCheckout`, CTR healthy** | **Offer or price problem.** Go to Phase 3C: change one thing about the offer (Part 20), run 5 days at ৳1,000/day, then stop regardless. |
| **0 verified members, CTR still below 0.8% after a creative refresh** | **Stop the experiment.** Preserve the remaining ~৳13,000. The answer for now is that cold paid social is not your channel. Go to Part 24 and get your first ten members by hand. |

---

### PHASE 3: Decide (Days 13 to 19). Budget depends on gate 2.

**Phase 3A, strong:** ৳1,800/day prospecting + ৳300/day retargeting × 7 = ৳14,700. This exceeds the plan and eats the reserve. That is exactly what the reserve is for. Change nothing except budget, and change it in one step, not daily. [Frequent budget edits reset learning.]

**Phase 3B, ambiguous:** ৳1,200/day prospecting + ৳250/day retargeting × 7 = ৳10,150. Fix exactly one thing based on the Part 27 diagnosis table. One thing. Run the full seven days.

**Phase 3C, weak:** ৳1,000/day prospecting × 5 = ৳5,000, then stop regardless of what happens. This is a confirmation run, not a rescue attempt.

**Decision gate 3, end of Day 19:** see Part 28.

## 15.3 Conditional rules, consolidated

```
IF  link CTR < 0.6% after ৳4,000 spent
THEN pause all ads, re-cut the first 3 seconds of all three videos, relaunch Phase 1.

IF  CTR ≥ 1.2% AND landing-page-view-to-ViewMembership < 15%
THEN the ad is over-promising or the landing page is under-delivering. Rewrite the hero. Do not touch the ads.

IF  ViewMembership ≥ 60 AND InitiateCheckout ≤ 3
THEN price or deposit is the barrier. Test the deposit presentation (Part 20), not the creative.

IF  InitiateCheckout ≥ 15 AND PaymentClaimed = 0
THEN the payment step is broken. Test it yourself on mobile data through the Facebook in-app browser before spending another taka.

IF  PaymentClaimed ≥ 5 AND verified ≤ 1
THEN people are claiming payments they did not make, or your verification process is too slow. Check bKash. Call them.

IF  CAC ≤ ৳900 with ≥ 5 members
THEN you have found something. Spend the reserve. Then stop and go operate. Do not scale further until you have delivered books to 25 people.

IF  CAC ৳900 to ৳1,800
THEN keep running to the end of Phase 3, fix one funnel step, do not increase budget.

IF  CAC > ৳2,500 after ৳15,000 spent
THEN stop. Preserve the remainder. Paid cold acquisition does not currently work at this price. Go to Part 24.

IF  total spend reaches ৳22,000 at any point
THEN stop and analyse before touching the reserve, regardless of results.

IF  the ad account is restricted or an ad is rejected
THEN do not create a new ad account. Appeal via Account Quality, and do not run duplicate accounts. That is the fastest route to a permanent ban.
```

---
---

# PART 16: Test duration

## 16.1 The numbers

| Question | Answer |
|---|---|
| Total test length | **19 days**, plus 3 days preparation, plus 3 days analysis. Call it 25 days end to end. |
| Minimum before any judgement | **72 hours**, and even then only on CTR and CPM, never on purchases |
| Minimum before a purchase judgement | **৳6,000 delivered spend or 7 days, whichever comes later** |
| Minimum before killing a single ad | **৳2,500 delivered spend on that specific ad**, and at least 3 days |
| Minimum before changing the landing page | **300 landing page views** |
| Minimum before changing the offer | **60 `ViewMembership` events** |

## 16.2 Why 19 days and not 7 or 60

Shorter than about two weeks and you are reading noise. Your expected total conversion count is 10 to 25 [ESTIMATE]; the difference between 3 conversions and 6 conversions in a week is not a signal, it is the same underlying rate observed twice.

Longer than about three weeks and two things go wrong. Your ৳26,500 stretched over 45 days is ৳590 a day, which is below the level where Meta can deliver consistently in a market this size, and you will get lumpy, unrepresentative delivery. And you will have burned a month of calendar time to learn something you could have learned in three weeks, which for a solo founder is the more expensive resource.

## 16.3 When NOT to judge performance

**Do not draw conclusions during any of these:**

- **The first 48 hours after launch.** Delivery is exploratory, CPMs are unrepresentative, and early conversions are usually the most eager segment, which flatters your numbers.
- **The first 48 hours after any edit to the ad set.** Editing audience, budget by more than ~20%, optimisation event, or placements restarts learning.
- **Friday.** Friday is the weekly holiday in Bangladesh; behaviour and costs differ materially. Never compare a Friday to a Tuesday, and never make a decision on a Friday's data.
- **Any single day.** Look at rolling 3-day windows minimum, 7-day preferred.
- **Any period containing a major event.** Around Eid, Pohela Boishakh, or any large national event, CPMs spike substantially [SECONDARY: reported increases of 30% to 50% in Bangladesh around major festivals] and your data is not comparable to normal periods.
- **The first day of retargeting.** The audience is tiny and frequency will look strange.

## 16.4 When to pause an ad

Pause a single ad when **all** of these are true:
- It has spent at least ৳2,500
- It has produced zero `InitiateCheckout`
- Its CTR is below half of the best-performing ad in the same ad set
- It has been running at least 3 days

Pausing one ad inside an ad set does **not** reset the ad set's learning, which is why this is the safest lever you have. Use it before you touch anything else.

## 16.5 When to shift budget

- **Between ads:** never manually. Meta does this continuously and better than you can at this volume.
- **Between campaigns:** only at a phase boundary (Day 6, Day 13).
- **Total budget up:** only after decision gate 2, only once, and by no more than about 30% in a single step. Larger jumps trigger a learning reset. [SECONDARY, but consistently reported.]
- **Total budget down:** avoid entirely. Reducing budget mid-flight both resets learning and makes your data harder to interpret. If you want to spend less, stop, do not throttle.

## 16.6 When to refresh creative

Refresh when **any** of these appear:
- **Frequency above 2.5** in your prospecting ad set. In a market the size of Dhaka you should not hit this in 19 days; if you do, your effective audience is far smaller than you think.
- **CTR declining more than 30% from its 3-day peak** while CPM holds steady. That is fatigue.
- **All three ads below 0.6% CTR by Day 5.** That is not fatigue, that is a message problem. Re-cut hooks.

"Refresh" at your budget means **re-cutting the first 3 seconds and the thumbnail**, not reshooting. See Part 20.

## 16.7 When to stop the experiment

Stop when any one of these is true:

1. You reach ৳22,000 delivered spend. Stop, analyse, then decide about the reserve deliberately.
2. You reach Day 19.
3. CAC exceeds ৳2,500 after ৳15,000 spent. Further spend buys you a more precise measurement of a number you already know is too high.
4. You get 15 or more verified members. Stop the ads and go operate. You cannot learn anything more useful from ad number sixteen than you can from delivering books to the fifteen people who already paid you. **Under-spending here is the correct move, and most founders get it wrong.**
5. Anything operationally breaks: you run out of books people want, courier fails repeatedly, or you cannot keep up with verification. Ads are the easy part; do not let them outrun fulfilment.

---
---

# PART 17: Metrics dashboard

Build this as one Google Sheet with three tabs. Update it once a day, at the same time, in under fifteen minutes.

## 17.1 The dashboard

### PRIMARY (the only numbers that decide anything)

| Metric | Formula / source | Target [ESTIMATE] |
|---|---|---|
| **Verified paid members** | Count of `status = verified` in your order sheet | ≥ 10 by Day 19 |
| **CAC** | Delivered ad spend ÷ verified members | ≤ ৳1,200 |
| **Membership revenue** | Sum of `membership_revenue` | ≥ ৳11,000 by Day 19 |
| **Contribution after CAC** | Membership revenue − delivered ad spend − variable costs | Above zero is a strong result at this stage |
| **Cash collected** | Sum of `cash_collected`, including deposits | Track it, never confuse it with revenue |

### SECONDARY (tells you where the funnel leaks)

| Metric | Source | Healthy [ESTIMATE] |
|---|---|---|
| Checkout starts | `InitiateCheckout` in Events Manager | ≥ 40 by Day 12 |
| Membership page views | `SPH - Membership Page View` custom conversion | ≥ 250 by Day 12 |
| LPV → ViewMembership | Events Manager | ≥ 25% |
| ViewMembership → InitiateCheckout | Events Manager | ≥ 15% |
| InitiateCheckout → PaymentClaimed | Events Manager | ≥ 20% |
| PaymentClaimed → verified | Your sheet | ≥ 80% |
| Landing page conversion (LPV → verified) | Your sheet ÷ Ads Manager | ≥ 0.8% |

### DIAGNOSTIC (explains the secondaries, decides nothing on its own)

| Metric | Healthy Bangladesh range [SECONDARY] |
|---|---|
| CPM | ৳100 to ৳250 |
| Link CTR | 1.0% to 2.5% |
| CPC (link) | ৳8 to ৳25 |
| ThruPlay rate | 15%+ |
| 3-second video views | Directionally, hook strength |
| 25% / 50% / 75% video retention | Where the script loses people |
| Cost per landing page view | ৳10 to ৳30 |
| Frequency | below 2.0 |
| Checkout abandonment | below 80% |

## 17.2 Which metrics matter at which stage

The most common way to waste a small budget is to watch the wrong number at the wrong time.

| Stage | Watch **only** these | Explicitly ignore |
|---|---|---|
| **Days 1-2** | Is it delivering? CPM. Nothing else. | CTR, conversions, everything |
| **Days 3-5** | CTR, cost per landing page view, video 25% retention | CAC, purchases, ROAS |
| **Days 6-9** | LPV → ViewMembership, ViewMembership → InitiateCheckout | CAC (still too few conversions to be real) |
| **Days 10-12** | Checkout starts, PaymentClaimed, first CAC estimate | CPM, individual-day swings |
| **Days 13-19** | **Verified members and CAC. Only these.** | Everything diagnostic, unless CAC is bad and you need to know why |
| **Post-test** | CAC, contribution, and then retention (which you cannot measure yet) | Every vanity metric |

## 17.3 Metrics to deliberately not track

- **ROAS.** Meaningless when your Purchase value is a membership fee and your real return comes from renewal. It will either flatter or terrify you, and neither is informative.
- **Post engagement, reactions, shares.** Fine as a mood signal. Never as a decision input.
- **Reach and impressions as goals.** They are inputs you are buying, not outputs you are achieving.
- **Cost per ThruPlay.** Optimising toward it means optimising for people who like watching videos.
- **Meta's own "Purchases" number, in isolation.** With server-side events at low volume, Meta's attribution is heavily modelled. **Your Google Sheet is the source of truth for how many people paid you.** Use Meta for direction, your sheet for the count. When they disagree, believe the sheet.

## 17.4 The daily fifteen minutes

1. Open Ads Manager. Record spend, impressions, CPM, link clicks, CTR, LPVs. (4 min)
2. Open Events Manager. Record `ViewMembership`, `InitiateCheckout`, `PaymentClaimed`. (3 min)
3. Open your order sheet. Verify any new claimed payments against bKash. Mark `status`. (5 min)
4. Write one sentence in a log: what happened, what you think it means. (3 min)

That last step sounds soft and is the most valuable. In three weeks you will not remember why day 9 looked odd, and the daily sentence is the only thing that will tell you.

---
---

# PART 18: CAC and unit economics

## 18.1 Revenue per member

| Plan | Membership revenue | Share [ASSUMPTION] |
|---|---|---|
| 6 months, founding | ৳900 | 75% |
| 12 months, founding | ৳1,600 | 25% |
| **Blended** | **৳1,075** | |

The 75/25 split is an assumption. For a brand this new, I would be unsurprised by 85/15. Track the actual split from day one; it is one of the most decision-relevant numbers in the test.

## 18.2 Variable cost per member (excluding CAC)

Books are **not** a variable cost. They are a reusable asset shared across all members. Treat inventory as capital expenditure, separately.

| Cost | Conservative | Optimistic | Basis |
|---|---|---|---|
| Delivery leakage over 6 months | ৳240 | ৳60 | 12 shipments, ৳20 under-recovery each vs ৳5 |
| Packaging | ৳180 | ৳90 | 12 shipments at ৳15 vs 6 at ৳15 |
| Damage and loss provision | ৳120 | ৳60 | ~3% of circulating value |
| Payment and admin | ৳50 | ৳25 | bKash cash-out, verification time |
| **Total per 6-month member** | **৳590** | **৳235** | |

**Contribution per 6-month member, before CAC:**
- Conservative: ৳900 − ৳590 = **৳310**
- Optimistic: ৳900 − ৳235 = **৳665**

**Contribution per 12-month member, before CAC:**
- Conservative: ৳1,600 − ৳1,100 = **৳500** (twice the shipments over twice the period)
- Optimistic: ৳1,600 − ৳450 = **৳1,150**

Note something important here: **the annual member is worth 1.78× the revenue but only about 1.6× the contribution**, because the delivery and packaging leakage roughly doubles. Your CAC tolerance for annuals is therefore about 1.6× your six-month tolerance, not 1.78×. This is the sort of thing that quietly makes an "obviously better" plan less better than it looks.

**Blended contribution before CAC:** ~৳358 conservative, ~৳786 optimistic.

## 18.3 The CAC bands

| Band | CAC | What it means | Action |
|---|---|---|---|
| **GREEN** | **≤ ৳600** | Roughly at or below optimistic contribution. First-order close to profitable. | Scale carefully. Spend the reserve. |
| **AMBER** | **৳600 to ৳1,200** | You lose on the first term, recover on the first renewal if retention is decent. | Keep testing. Do not scale. Fix the funnel. |
| **RED** | **৳1,200 to ৳2,500** | Needs roughly two renewals to break even. | Only continue with strong qualitative evidence. Reduce spend. |
| **BLACK** | **> ৳2,500** | Higher than a full annual membership. | Stop paid acquisition. Go to Part 24. |

In USD at ৳122: green ≤ $5, amber $5 to $10, red $10 to $20, black above $20.

**Sanity check:** a $5 CAC for a paying customer in Bangladesh is achievable for a good offer. A $20 CAC for a $7 first purchase is not a business.

## 18.4 First-order profitability

You are first-order profitable when **CAC < contribution in the first term**: ৳310 conservative, ৳665 optimistic, per six-month member.

[ESTIMATE] **You will almost certainly not hit this during the validation test.** A first cold campaign, with no creative iteration history, no pixel data, and a novel category, landing under ৳600 CAC would be an unusually good result. Expect amber.

**That is fine, if and only if you understand why.** Losing money on first-order acquisition is acceptable when you have reason to believe in renewal. It is not acceptable as a permanent state, and it is fatal if you scale before you have any renewal evidence, which you will not have for six months.

**Therefore:** during this test, treat CAC not as a profitability test but as a **feasibility test**. The question is "is CAC within striking distance of contribution once creative and funnel improve?" A CAC of ৳1,100 is encouraging, because creative iteration and funnel fixes routinely halve early CAC. A CAC of ৳4,000 is not, because nothing halves four times.

## 18.5 Lifetime value, with honest retention assumptions

You have zero retention data. Anyone who gives you a confident LTV is guessing. Here are two explicit guesses.

**Conservative case:**
- 35% renew once, 12% renew twice
- LTV = 1,075 × (1 + 0.35 + 0.12) = **৳1,580**
- Lifetime contribution ≈ ৳1,580 × 0.33 = **৳521**
- Max CAC at LTV/CAC of 3 = **৳527**

**Optimistic case:**
- 60% renew once, 35% twice, 20% three times
- LTV = 1,075 × (1 + 0.60 + 0.35 + 0.20) = **৳2,311**
- Lifetime contribution ≈ ৳2,311 × 0.42 = **৳971**
- Max CAC at LTV/CAC of 3 = **৳970**

**So your defensible long-run CAC ceiling is roughly ৳530 to ৳970**, which is tighter than the amber band suggests. The amber band exists because during validation you are also buying information, and information has value. But do not let amber become your permanent operating assumption.

**The thing that would change all of this:** if members average 8 books per six-month term rather than 12, your delivery leakage falls sharply and contribution improves substantially. Track books-per-member-per-month from day one. It may be your most important operating metric and you currently have no estimate for it.

## 18.6 Membership economics versus cash collected

Two ledgers. Keep them separate and never let one comfort you about the other.

| | Membership economics | Total cash collected |
|---|---|---|
| Includes deposits? | No | Yes |
| Per 6-month member | ৳900 | ৳1,400 |
| Tells you | Whether the business works | Whether you survive this month |
| Danger | Ignoring it means scaling a loss | Believing it means spending a liability |

**The specific trap:** twenty members bring in ৳28,000 of cash. That will feel like success against ৳26,500 of ad spend. But ৳10,000 of it is deposits you owe back, and ৳18,000 is membership revenue against ৳26,500 of spend. You lost ৳8,500 and your bank balance is roughly flat. Deposit float will make a failing unit economic look fine for exactly as long as you are growing, and will reveal itself the moment you stop.

**Rule: never spend deposit money on ads.** Ring-fence it, mentally at minimum and ideally in a separate bKash or bank account. The day you fund acquisition from deposits, you have built a structure that requires growth to remain solvent.

---
---

# PART 19: Retargeting

## 19.1 When to turn it on

**Not on day one.** Two reasons: the audiences are empty, and retargeting a 200-person pool produces high frequency, wasted spend, and a distorted read on your prospecting.

**Turn on when `WEB-ALL-30D` reaches at least 700 people.** [ESTIMATE: around day 6 at Phase 1 spend.] Meta's practical delivery floor for a website custom audience is around 1,000; below that, delivery is unreliable, but combining three audiences with an OR gets you there sooner than waiting for any single one.

## 19.2 The audiences

| Name | Definition | Window | Priority | Purpose |
|---|---|---|---|---|
| `WEB-CHECKOUT-30D` | URL contains `/checkout` | 30 days | **Highest** | Abandoners. Highest intent by a wide margin. |
| `WEB-MEMBER-30D` | URL contains `/member` | 30 days | High | Looked at pricing, did not start |
| `WEB-ALL-30D` | All website visitors | 30 days | Medium | Read something, left |
| `VID-25PCT-30D` | Watched 25%+ of any of the three videos | 30 days | Medium | Engaged with the message |
| `SOC-ENGAGE-90D` | Engaged with the Page or Instagram | 90 days | Low | Weakest signal, but free |
| `WEB-PURCHASE-180D` | Fired `Purchase` | 180 days | **Exclusion** | Never retarget a member |
| `WEB-CLAIMED-180D` | Fired `PaymentClaimed` | 180 days | **Exclusion** | In verification, do not chase |

## 19.3 The structure

**One retargeting ad set. Not five.**

```
AD SET: WARM-ALL-30D
├── Include (OR): WEB-ALL-30D, VID-25PCT-30D, SOC-ENGAGE-90D
├── Exclude: WEB-PURCHASE-180D, WEB-CLAIMED-180D
├── Advantage+ audience: OFF
├── Age 22-45, Dhaka, automatic placements
├── Optimise for: Purchase
├── Budget: ৳250/day
├── Destination: /member
├── AD: V3-WHY-RT
└── AD: STATIC-FAQ
```

Why not separate ad sets by intent level? Because with a total warm pool of perhaps 1,500 people, splitting it into three ad sets gives each one 400 to 600 people. At that size, frequency climbs past 5 within days, your CPMs spike, and you have annoyed a small number of people expensively. One combined ad set at ৳250/day is right.

**Do not** build a dedicated `WEB-CHECKOUT-30D` ad set. It will contain perhaps 20 to 60 people. That is not an audience, it is a contact list. Handle them the right way instead, see 19.5.

## 19.4 Retargeting creative and message

The mistake is showing the same ad again. A warm viewer has already seen the pitch; repeating it adds nothing except frequency. Retargeting creative must **handle the objection that stopped them.**

**Ad 1: `V3-WHY-RT`** (the founder video, retargeting copy)

```
You looked at Sphotik and did not join. That is fair, we are new.

So here is the honest version. We opened in September 2026. Our shelf is a few hundred books, not a few thousand. We only deliver in Dhaka. And every book our catalogue says is available is physically sitting here right now.

Membership is ৳900 for six months. The ৳500 deposit is not a fee, it comes back to you in full when your books come back to us. Delivery is ৳120 per swap, both directions.

If you join and it is not what you hoped, tell us within 14 days and we refund the membership fee.

sphotik.com
```

**Ad 2: `STATIC-FAQ`** (single image, 1080×1350, plain typographic on cream)

Image content, four short lines:
```
"Is the deposit really refundable?"
Yes. In full, when your books come back.

"What if I damage a book?"
We tell you the repair or replacement cost before deducting anything.

"What if I want to stop?"
Cancel within 14 days and we refund the membership fee.

"Can I actually reach you?"
[phone number]. A person answers.
```

Primary text:
```
The four questions people ask before joining Sphotik, answered plainly.

Membership library in Dhaka. ৳900 for six months, ৳500 refundable deposit, ৳120 delivery per swap. No fee for any individual book.

sphotik.com
```

## 19.5 Handle checkout abandoners by hand, not by ad

This is the most valuable paragraph in this section.

Your `DetailsStep` collects name, phone, and email **before** the payment step. So anyone who reaches step 3 and does not complete has already given you a working phone number, and it is already in your Google Sheet if you post the details step as well as the confirmation (do this: post a partial row at step 2 with `status = abandoned`).

**Call them.** Not a Facebook ad, a phone call, within 24 hours, from you personally.

> "Hi, this is [name] from Sphotik. You started signing up yesterday and did not finish. I am not calling to sell you anything, I just want to know what stopped you. Was it the price, the deposit, or something on the website that did not work?"

At twenty to forty abandoners over three weeks, this is perhaps two hours of phone calls. It will produce better information than your entire ৳26,500 ad spend, and it will convert some of them. A ৳250/day retargeting ad cannot do either of those things.

## 19.6 Budget and expectations

| Phase | Retargeting budget | Expected pool |
|---|---|---|
| Days 1-5 | ৳0 | Building |
| Days 6-12 | ৳250/day (৳1,750) | 700 to 1,500 |
| Days 13-19 | ৳250 to ৳300/day (৳1,750 to ৳2,100) | 1,500 to 2,500 |

**Total: roughly ৳3,500 to ৳3,900, about 15% of delivered spend.** That is the right proportion. Retargeting a pool this small cannot absorb more, and every taka above this is buying frequency, not reach.

## 19.7 When the audience is too small

| Pool size | Do this |
|---|---|
| Under 700 | Do not run it. Put the money in prospecting. |
| 700 to 1,000 | Run at ৳150/day maximum. Watch frequency daily. |
| 1,000 to 3,000 | Run at ৳250/day. This is the sweet spot for your scale. |
| Above 3,000 | Consider splitting out `WEB-MEMBER-30D` as its own ad set. |
| **Frequency above 4.0 at any size** | **Cut the budget in half immediately.** You are burning money re-showing ads to the same small group, and irritating your warmest prospects. |

---
---

# PART 20: A/B testing with three videos

## 20.1 The constraint

With 10 to 25 total conversions [ESTIMATE], you **cannot** run a statistically valid A/B test on anything. Detecting a 20% conversion-rate difference at conventional confidence needs hundreds of conversions per arm. You will have single digits.

So stop thinking about statistical testing and start thinking about **elimination and diagnosis**. You are not measuring effect sizes; you are looking for things that are obviously broken and things that are obviously working. Those show up at low volume. Subtleties do not.

**The operating rule: change one thing at a time, and only change something when a funnel step is visibly failing.**

## 20.2 What is testable at your volume, and what is not

| Variable | Testable? | Why |
|---|---|---|
| **Video hook (first 3 seconds)** | **Yes** | Measured by 3-second view rate and CTR, which accumulate in the thousands. High volume, fast signal. |
| **Which of the three videos** | **Yes, roughly** | Measured by cost per `InitiateCheckout`. Directional only, but usable. |
| **Landing page headline** | **Partly** | Measured by LPV → `ViewMembership`, which will reach a few hundred. Directional. |
| **Deposit presentation** | **Partly** | Measured by `ViewMembership` → `InitiateCheckout`. Weak but readable. |
| **Ad headline and primary text** | **No** | Needs conversion-level data you will not have. |
| **CTA button** | **No** | The effect size is small and your volume is smaller. |
| **Price (৳900 vs ৳1,000)** | **No, and do not try** | Would need hundreds of conversions per arm, and running two prices simultaneously is a fairness and trust problem with real customers. |
| **12-month price** | **No** | Too few annual purchases to read anything. |

## 20.3 The experiment matrix

One experiment at a time. Each runs to its stated volume threshold before you read it.

| # | Days | Variable | How | Measured by | Threshold to read | Decision |
|---|---|---|---|---|---|---|
| **E1** | 1-5 | Which video | 3 ads, one ad set, let Meta allocate | Cost per `InitiateCheckout`, then CTR | ৳6,000 spent | Pause the clear loser if it meets the Part 16.4 criteria |
| **E2** | 6-9 | Hook, on the weakest surviving video | Re-cut its first 3 seconds only, upload as a new ad, pause the original | 3-second view rate, CTR | ৳2,500 on the new cut | Keep whichever hook has the higher CTR |
| **E3** | 6-12 | Landing page hero | Change the H1 and subheadline once, on Day 6 | LPV → `ViewMembership` rate, before vs after | 300 LPVs after the change | Keep the better version |
| **E4** | 10-14 | Deposit presentation | Change how the deposit block reads on `/member` (see below) | `ViewMembership` → `InitiateCheckout` | 150 `ViewMembership` after the change | Keep the better version |
| **E5** | 13-19 | Offer framing | Add or remove the 14-day guarantee prominence near the CTA | `InitiateCheckout` → `PaymentClaimed` | 25 `InitiateCheckout` after | Keep the better version |

**Overlaps are deliberate but constrained:** E2 changes an ad, E3 changes the page. Those are independent surfaces, so running them in the same window is acceptable. **Never run two changes on the same surface at once.** Never change the page while a page test is reading.

## 20.4 Exactly how to test hooks

Hooks are your highest-leverage variable because you can read them in hours, not weeks.

For each video, prepare two alternative openings before launch, so you can swap fast:

**`V1-MATH`**
- A (default): price sticker, "I read this book once. ৳480."
- B: start on the wide shelf shot. "৳12,000 of books. Read once each."
- C: pure text card, hard cut. `Books you read once: ৳12,000. Books you borrow: ৳900.`

**`V2-HOW`**
- A (default): "This is not a bookshop. It is not a rental."
- B: start at the packing shot, no talking. Text: `What ৳900 gets you.`
- C: "৳900. Six months. As many books as you want."

**`V3-WHY`**
- A (default): "I own 312 books. Eighty I never opened again."
- B: "I am going to tell you three things Sphotik cannot do yet."
- C: shelf pan, then the founder entering frame. "I built a library because I could not stop buying books."

**Reading a hook test:** 3-second view rate at 5,000 impressions is enough to see a large difference. It is not enough to see a small one. If two hooks are within 15% of each other, they are the same hook; keep the one you prefer and move on.

## 20.5 Exactly how to test the deposit presentation

This is the change most likely to move your conversion rate, because the deposit is your biggest friction point (Part 1.4).

**Version A (current):** a table of three tiers with books, value cap, and days.

**Version B (recommended alternative):** lead with refundability, drop days entirely, use one line per tier.

```
Your deposit is not a fee. It is your money, held while you have our books, and returned in full when they come back.

৳500     Hold 2 books at a time
৳1,000   Hold 4 books at a time
৳1,500   Hold 6 books at a time

Every member gets 14 days per book, and you can extend if nobody is waiting.

Most founding members start at ৳500. You can increase it later at any time.
```

Three things are doing work here: refundability comes first, the days variable is removed, and "most start at ৳500" gives social permission to pick the cheapest option, which lowers the pay-today number to ৳1,400 and removes the tier-choice paralysis.

## 20.6 What NOT to test

- **Two prices at the same time.** Statistically hopeless, and if two customers compare notes you have a trust problem that costs more than the test is worth.
- **Two landing pages.** You do not have the traffic and you would double your maintenance.
- **Bangla-only versus English-only sites.** Your bilingual implementation already exists and works. Leave it.
- **Placements.** Automatic is right, and testing it disqualifies Advantage+ state.
- **Anything during the first 72 hours.** Let it deliver.
- **More than one thing per surface at a time.** If you change the hero headline and the pricing block on the same day, you learn nothing from either.

---
---

# PART 21: Pricing audit

## 21.1 The four options

| | 6 months | 12 months | Annual discount vs 2×6m | ৳/month, annual |
|---|---|---|---|---|
| **A** (current) | ৳900 | ৳1,600 | 11% | ৳133 |
| **B** | ৳900 | ৳1,700 | 5.5% | ৳142 |
| **C** | ৳1,000 | ৳1,700 | 15% | ৳142 |
| **D** (my recommendation) | ৳900 | ৳1,500 | **17%** | **৳125** |

## 21.2 Assessment of each

**Option A, ৳900 / ৳1,600.** The entry price is right. The annual is under-incentivised at 11%. Workable but leaves cash on the table. **Grade: B minus.**

**Option B, ৳900 / ৳1,700.** Strictly worse than A. A 5.5% discount for doubling commitment to an unproven service is not an offer, it is a rounding error. Nobody will take it, so your annual tier becomes pure decoy and your cash flow suffers. The only argument for it is that ৳1,700 is closer to your regular ৳2,000, which preserves the anchor. That is not worth it. **Grade: C.**

**Option C, ৳1,000 / ৳1,700.** The discount structure improves to 15%, and ৳1,000 is a clean round number that is easier to say. But it raises the entry price 11% at the exact moment you most need people to say yes, and it pushes pay-today from ৳1,400 to ৳1,500. Worse: it makes the founding discount on the six-month only 17% off regular instead of 25%, which weakens the whole founding narrative. **Grade: C plus.** Consider it after you have proven demand, never before.

**Option D, ৳900 / ৳1,500.** Recommended, for five reasons.

1. **The annual discount becomes real.** ৳1,500 versus ৳1,800 is 17% off, a genuine reason to commit rather than a token.
2. **The monthly framing gets much better.** ৳125 a month against ৳150 a month is a clean, memorable, quotable gap. ৳133 against ৳150 is not; nobody says "one hundred and thirty-three."
3. **৳1,500 is a psychologically clean number.** "Fifteen hundred taka" is one unit of thought. "Sixteen hundred" is arbitrary.
4. **Cash flow, which is your actual constraint.** Every annual member gives you ৳1,500 today instead of ৳900. That difference buys books. If option D shifts your mix from 25% annual to 35% annual, your blended revenue per member goes from ৳1,075 to ৳1,110, and more importantly your day-one cash per member rises meaningfully.
5. **It costs you almost nothing.** ৳100 less per annual member, on maybe 5 to 8 annual members during the test, is ৳500 to ৳800 total. That is not a real cost.

**Grade: A minus.** The minus is because I have no evidence, only reasoning. It is a judgement call, not a finding.

## 21.3 An option E worth considering, and why I am not recommending it

**Option E: launch with six months only, at ৳900. No annual tier at all.**

The case for it is genuinely strong. It removes a decision from the checkout, and every removed decision raises conversion. It stops you selling a twelve-month commitment you have not yet earned the right to sell, which is a real refund risk if you underdeliver in month three. And it makes your validation cleaner: one price, one product, one question.

The case against, which is why I stop short of recommending it: your `MembershipCard` and duration selector already exist and work, so there is no build cost to keeping both; the annual tier also functions as a price anchor that makes ৳900 feel like the sensible choice; and you would lose the ability to observe how many people *want* to commit for a year, which is itself a strong signal about how much they believe you.

**Compromise, and this is what I would actually do:** keep both tiers, but make the six-month **visually the default and clearly recommended**, and present the annual as the option for people who are sure.

### An urgent, concrete bug in your current defaults

I checked your code. Both defaults are set to the most expensive reasonable option:

```61:61:src/data/content.ts
export const defaultDepositIdx = 1
```

```92:99:src/app/checkout/page.tsx
  const [plan, setPlan] = useState<MembershipDuration>(
    () =>
      membershipDurations.find((m) => m.id === requestedDuration) ??
      membershipDurations[1],
  )
  const [tier, setTier] = useState<DepositTier>(
    () => depositTierById(requestedDeposit) ?? depositTiers[defaultDepositIdx],
  )
```

`membershipDurations[1]` is the twelve-month plan (৳1,600). `depositTiers[1]` is the ৳1,000 deposit. Since `payToday = plan.price + tier.deposit`, **a cold visitor landing on `/checkout` today sees a pay-today figure of ৳2,600.**

Your ad will say ৳900. Your checkout will open at ৳2,600. That is not a price gap, it is a factor of nearly three, and it is almost certainly the highest-cost single line of code in the repository.

The same default applies in `MembershipCard`, so `/member` shows the ৳1,000 deposit preselected too.

**Fix:** set `defaultDepositIdx = 0` and change the plan fallback to `membershipDurations[0]`. That makes the default path ৳900 + ৳500 = **৳1,400**, which matches the number your landing page and ads will state. Anyone who wants more capacity or a longer term can still choose it, and upgrading a deposit later is a frictionless upsell. Add this to MUST CHANGE.

## 21.4 What to actually launch with

| Item | Launch value |
|---|---|
| 6 months, founding | **৳900** |
| 12 months, founding | **৳1,500** |
| 6 months, regular (stated as future price) | ৳1,200 |
| 12 months, regular (stated as future price) | ৳2,000 |
| Default selected plan at checkout | **6 months** |
| Default selected deposit | **৳500** |
| Deposit tiers | ৳500 / ৳1,000 / ৳1,500, unchanged |
| Loan period | **Flat 14 days for all tiers** (SHOULD TEST, but I would launch with it) |
| Delivery | **৳120 flat per swap, both directions** |
| Guarantee | **14 days, membership fee refunded** |
| Anchor framing | "Price after launch: ৳1,200" rather than "was ৳1,200" |

**Resulting pay-today for the default path: ৳1,400.** That is the number your landing page must state above the fold.

## 21.5 What would change my mind

- If Phase 1 shows healthy `ViewMembership` volume with near-zero `InitiateCheckout`, price is the barrier, and the response is **not** to cut price. It is to reduce the pay-today number by better deposit framing, or to add a payment-splitting option (membership now, deposit at first delivery). Cutting the membership fee to ৳700 would teach you that people will pay ৳700, which is not a useful thing to learn.
- If the annual share exceeds 40%, you have underpriced the annual and should move it toward ৳1,700 for the next cohort.
- If nobody picks the ৳1,500 deposit tier, drop it. Three tiers where one is dead is two tiers plus clutter.

---
---

# PART 22: Checkout audit

## 22.1 The journey today, and what breaks

```
Facebook ad
  → sphotik.com (homepage)
  → /member
  → /checkout?duration=...&deposit=...
      Step 1: choose duration + deposit
      Step 2: name, phone, email, area
      Step 3: choose bKash/Nagad/Rocket, LEAVE THE SITE, send money,
              COME BACK, enter transaction ID, tick the rules box
      Step 4: "Payment submitted" (same URL)
  → localStorage membership written, Purchase fired, no server record
```

Six problems, ordered by how much money each is costing you.

### Problem 1: state loss on app switch (CRITICAL)

Step 3 requires the user to leave your site, open the bKash app, complete a Send Money, and return. On mobile, and especially inside the Facebook in-app browser, **returning does not reliably restore the page.** The webview may be reloaded, backgrounded and killed, or restored to a fresh state.

Your entire checkout state (`step`, `plan`, `tier`, `customer`, `method`) lives in React `useState`. If the webview reloads, all of it is gone and the user lands back on step 1 having already sent you ৳1,400.

**That user has paid you and has no way to tell you.** They will either message you angrily or write it off and hate you. Either way you have their money and no order.

**This is the single highest-value bug in your codebase right now.**

**Fix:** persist `{step, plan.id, tier.id, customer, method}` to `sessionStorage` on every change, and restore on mount. Guard the `PaymentClaimed` effect on a persisted boolean, not just the `recorded` ref, so restoring a confirmed state does not re-fire the event (see Part 5.6).

### Problem 2: no server record (CRITICAL)

Covered at the top of this document and in Part 2 Step 0. The order must exist somewhere other than the user's browser.

### Problem 3: unverified Purchase (CRITICAL)

Covered in Part 4.

### Problem 4: the clipboard copy may silently fail

`CopyableNumber` calls `navigator.clipboard?.writeText`. The optional chaining means that when `clipboard` is undefined (which happens in some in-app webviews and any non-secure context), **nothing happens and the UI still shows the copied checkmark**, because `setCopied(true)` runs regardless.

The user thinks the number is copied, pastes an old clipboard value into bKash, and sends ৳1,400 to a stranger.

**Fix:** await the promise, catch the failure, and on failure show the number as selectable text with a "select and copy manually" hint. Also always display the number in large, unambiguous type regardless of copy state.

### Problem 5: email is required

`DetailsStep` hard-requires a valid email. A meaningful share of Bangladeshi mobile-first buyers do not use email actively and will either abandon or enter a fake address, which then breaks your CAPI match quality and your ability to contact them.

**Fix:** make email optional, keep phone required, and label it "Email (optional, for your receipt)."

### Problem 6: no way to reach a human at the moment of doubt

Step 3 is where a first-time buyer, about to send ৳1,400 to a personal bKash number belonging to a brand they discovered forty seconds ago, has their crisis of confidence. There is currently nothing on that screen to resolve it.

**Fix:** a persistent line on step 3: `Not sure? WhatsApp us on [number] and we will walk you through it.` A single WhatsApp conversation at that moment is worth more than any trust badge.

## 22.2 "Pay today" clarity

Your `OrderSummary` already separates the membership fee from the deposit, which is exactly right. Two improvements:

1. **State the refundable portion in the same visual block as the total, not below it.** Your `Confirmed` screen does this well (`৳500 of this is your refundable deposit`), but the pre-payment summary should too, in the same prominent position.
2. **Add a delivery line reading ৳0**, with a note. Right now delivery is absent from the summary, which means the user's first encounter with the delivery cost is after they have paid. Show:
   ```
   Delivery today          ৳0
   Charged at ৳120 per swap when your books ship.
   ```
   Disclosing a future cost at the moment of purchase costs you a small number of conversions and saves you a large number of refund conversations.

## 22.3 Membership versus refundable deposit, in language

Everywhere the two appear together, the deposit must be described in refund language, not payment language.

| Instead of | Write |
|---|---|
| "Deposit: ৳500" | "Refundable deposit: ৳500" |
| "Total: ৳1,400" | "Pay today: ৳1,400 (৳500 comes back to you)" |
| "Choose your deposit" | "Choose how many books you can hold at once" |
| "Deposit amount" | "Your refundable deposit" |

The deposit selector on `/member` is currently framed as choosing an amount of money. It should be framed as choosing a capacity, with the money as the consequence.

## 22.4 Mobile and the Facebook in-app browser

Most of your traffic will arrive in the Facebook or Instagram in-app browser, which is not Chrome and behaves differently.

**Test this exact sequence on a real phone, on mobile data, before launch:**

1. Post a link to your site in a private Facebook post.
2. Tap it from the Facebook app. Do not open in Chrome.
3. Complete the full checkout.
4. At step 3, actually switch to the bKash app and send ৳10 to your own number.
5. Switch back to Facebook.
6. **Does the checkout still show step 3 with your details intact?**

If the answer is no, you have Problem 1 and you must fix it before launch.

Also check in that environment:
- Does the Pixel fire? Some in-app browsers restrict third-party scripts more aggressively.
- Does `navigator.clipboard` work?
- Does `sessionStorage` survive the app switch? (`sessionStorage` is per-tab; in a webview that is destroyed and recreated, it may not. Test `localStorage` as a fallback with a timestamp-based expiry.)
- Do the fonts load? Fraunces and Noto Serif Bengali are both webfonts and slow connections make the first paint text-invisible.

## 22.5 Payment friction

Manual Send Money plus a transaction ID is the right choice for you. It is what Bangladeshi customers expect from small businesses, it requires no gateway integration or merchant account, it has no per-transaction fee beyond bKash's own, and it works. **Do not replace it with SSLCommerz for this test.** The integration cost and the merchant onboarding time are not worth it at 20 transactions.

But reduce the friction inside it:

1. **One payment method, not three.** Offering bKash, Nagad, and Rocket is a choice the user must make with no information. Lead with bKash (largest user base by a wide margin) and put "Also accept Nagad or Rocket? Message us" below. Fewer options, faster decision.
2. **Show the exact amount in large type.** You do this already. Good.
3. **Accept "Send Money" only, and say so.** Do not accept "Payment" or "Cash Out," which have different fee structures and will confuse people.
4. **Tell them what the transaction ID looks like.** Your placeholder `8N7A6B5C4D` is good. Add: "bKash sends this to you by SMS immediately after the transfer. It is 10 characters."
5. **Verify fast and tell them you will.** Your `Confirmed` screen says "usually within one working day." Beat that. Verify within two hours during waking hours and send a WhatsApp message. The gap between payment and confirmation is the most anxious moment in the entire journey.

## 22.6 The ideal sequence

```
Ad
 ↓  Cold, message = "borrow books, no fee per book, ৳900 for six months, Dhaka"
Landing page (/)
 ↓  Above fold: what it is, pay-today ৳1,400 with ৳500 refundable, Dhaka, one CTA
Membership page (/member)
 ↓  Duration (6m DEFAULT) → capacity/deposit (৳500 DEFAULT) → running total always visible
Checkout step 1: confirm choices
 ↓  Pay-today box: ৳900 + ৳500 refundable = ৳1,400. Delivery ৳0 today, ৳120 per swap.
Checkout step 2: details
 ↓  Name + phone required. Email optional. Area with a "we will confirm delivery" note.
 ↓  >>> POST a partial row to your sheet here, status = abandoned <<<
Checkout step 3: payment
 ↓  bKash primary. Exact amount, large. Number, large, selectable, copy button.
 ↓  Rules checkbox. WhatsApp help line. State persisted to storage.
 ↓  "Enter the transaction ID from your bKash SMS"
Checkout step 4: submitted
 ↓  >>> POST the full row, status = claimed. Fire PaymentClaimed. <<<
 ↓  "We will confirm within 2 hours. We will message you on [their number]."
 ↓  WhatsApp button: "Message us your transaction ID"
YOU verify against bKash
 ↓  >>> Mark verified in sheet. Fire server-side Purchase. <<<
WhatsApp confirmation to the member, within 2 hours
 ↓  "You are in. Here is how to pick your first books: [link]"
First book selection
```

The two `>>>` steps at step 2 and step 4 are what turn your checkout from a demo into a business.

---
---

# PART 23: Trust system

A new brand asking a stranger for ৳1,400 through a personal bKash number is asking for a lot. Here is the minimum credible trust stack, and it contains nothing fake.

## 23.1 Real identity

| Signal | What to do |
|---|---|
| **Named founder with a face** | Your name and photo on the About page and in `V3-WHY`. Not "the Sphotik team." A named person who can be found is the single strongest trust signal available to you. |
| **Personal social presence** | Your real Facebook or LinkedIn profile, linked from About. It should show you existed before Sphotik did. |
| **Facebook Page Transparency ON** | Shows Page creation date and admin country. Turning it off is what fake Pages do. |
| **Consistent naming everywhere** | Same name, same logo, same handle, same domain, same phone across Page, Instagram, site, and invoices. Any inconsistency is read as a warning sign. |

## 23.2 Location

- A **real Dhaka address** on the contact page and the Facebook Page. It can be your home. It does not need to be a shop.
- **A photograph of the actual shelves**, in the actual room, on the About page and the homepage. This does more work than every other trust signal combined, because it is unfakeable in a way a logo is not.
- Do not claim a shopfront or opening hours for a place people can visit unless they genuinely can.

## 23.3 Support

| Channel | Commitment |
|---|---|
| WhatsApp | Same number as the Page. Displayed on the homepage, the FAQ, and checkout step 3. Reply within 2 hours during 10:00 to 20:00. |
| Phone | Real number, answered. **Delete the `+880 1700 000000` placeholder from `/contact` today.** |
| Email | `hello@sphotik.com.bd`, checked daily. |
| Facebook and Instagram DMs | Reply within 2 hours. Response rate is publicly visible. |
| Comments | Reply to every one publicly, including hostile ones. Especially hostile ones. |

**A stated response commitment you actually keep beats a stated 24/7 availability you do not.**

## 23.4 Refund and deposit rules

This is where trust is won or lost for your specific model. Put this on the homepage, the membership page, and `/membership-rules`, in the same words each time.

```
About your deposit

Your deposit is not a payment. It is your money, held while our books are with you.

You get all of it back when:
  · every book you borrowed is returned
  · nothing is outstanding on your account

You get it back within 3 to 7 working days of asking, to the same bKash, Nagad, or bank account you paid from.

If a book is damaged or lost, we tell you the repair or replacement cost in writing, with the receipt, before we deduct anything. You can pay it separately instead if you prefer, and get your full deposit back.

We never deduct anything without telling you first.
```

That last line is the one that matters. The fear is not losing the deposit; it is arbitrary deduction.

**Also fix your cancellation policy.** Currently, cancelling early forfeits the membership fee and the deposit is held until the original period ends. To an outsider that reads as punitive. With the 14-day guarantee (M6), rewrite it as:

```
If you cancel

Within 14 days of joining: we refund your membership fee in full, and your deposit once any books are back. Delivery already paid is not refunded.

After 14 days: your membership fee covers the period you signed up for and is not refunded for unused time. Your deposit is returned once your books are back and your account is settled.
```

## 23.5 Delivery details

Vagueness about logistics reads as inexperience. Publish, on `/delivery`:
- The named courier partners you actually use.
- Expected timelines: "Usually next day inside Dhaka, sometimes two days."
- The flat swap fee, ৳120, stated as a number.
- What happens if a book arrives damaged: "Tell us within 24 hours with a photo and we replace it at no cost to you."
- What happens if a delivery fails.

## 23.6 Payment trust

The riskiest moment in your funnel is asking a stranger to Send Money to a personal number. Make it feel institutional:

- **Use a bKash Merchant account, not a personal one, if you can get one.** Merchant accounts show a business name to the sender. That single difference is worth more than a page of copy. It requires a trade licence.
- If you must use a personal number, **say so plainly**: "This is our founder's bKash number. We are a new business and we do not have a merchant account yet. Your transaction ID is your receipt and we confirm every payment within two hours."
- **Confirm fast.** Two hours, by WhatsApp, with the order number and what happens next.
- **Never ask for a PIN or an OTP**, and say so on the payment screen: "We will never ask you for your bKash PIN or OTP. Anyone who does is not us."

## 23.7 Social proof, honestly

You have zero customers. Here is what to do about it, and what not to.

**Never do:**
- Buy reviews, ratings, likes, or followers.
- Write testimonials from invented people.
- Use stock photos of readers and imply they are members.
- Claim "500+ happy readers" or any number you cannot name.
- Use fake countdown timers or invented scarcity.

Beyond the ethics: Bangladeshi consumers are unusually well practised at spotting bought engagement, because so much of it exists locally. Getting caught is worse than having nothing.

**Do instead, in this order:**

1. **Substitute transparency for social proof.** "We opened in September 2026. You would be one of our first members." Being new, stated confidently, is more trustworthy than being fake-established. It also creates a genuine founding-member identity that people like belonging to.
2. **Show the inventory, not the customers.** Photograph your actual shelves weekly. Real books are proof of a real operation.
3. **Show the operation.** Photos of packing, of a courier pickup, of a book being logged back in. Process is proof.
4. **Collect real testimonials from your first ten, properly.** After their first successful return, ask: "Would you be willing to record fifteen seconds on your phone about how it went? Say whatever is true, including anything that annoyed you." Use them with the person's real name and photo, with permission.
5. **Publish the founding member count once it is real and non-trivial.** "47 founding members" at 47 is credible and useful. At 3, say nothing.

## 23.8 The trust checklist

- [ ] Named founder with a photo on the About page
- [ ] Founder appears on camera in `V3-WHY`
- [ ] Real, answered phone number everywhere (placeholder deleted)
- [ ] WhatsApp visible on homepage, FAQ, and checkout step 3
- [ ] Real Dhaka address published
- [ ] Photograph of actual shelves on homepage and About
- [ ] Deposit refund rules stated identically in three places
- [ ] 14-day guarantee stated near every CTA
- [ ] Delivery cost published as a number
- [ ] Courier partners named
- [ ] Page Transparency enabled
- [ ] 4+ organic posts before the first ad
- [ ] Every comment answered within a few hours
- [ ] "We will never ask for your PIN or OTP" on the payment screen
- [ ] Zero fake reviews, zero bought likes, zero invented numbers

---
---

# PART 24: Getting to the first 100 members

Paid ads are one input. For the first hundred, they are not the main one. This is the operating plan.

## Members 1 to 10: prove you can deliver

**Source:** friends, family, your own network, book groups you are genuinely part of, and the first few from ads. **Charge them full price.** A free or discounted first cohort teaches you nothing about willingness to pay, which is the only thing you are trying to learn.

**What to do:**
- Onboard each one personally, by phone or WhatsApp. Not email.
- Deliver the first shipment yourself where geographically possible. Meet them. Hand over the books.
- Ask, at handover: "What did you expect that you did not get?"
- Log absolutely everything: which books they chose, how long they took to choose, what they asked, what confused them, how long the return took.

**What to learn:**
1. Can you physically pack, ship, track, and recover books without loss?
2. How long is a real swap cycle, door to door?
3. What does delivery actually cost you, per leg, in practice?
4. Which books do people reach for first?
5. Where does the website confuse people? (Watch someone use it. Do not ask them about it.)

**Do not scale ads past this point until every one of the first ten has completed a full borrow-and-return cycle.** If returns do not work, nothing works.

## Members 10 to 25: find the repeatable message

**Source:** ads, plus the abandoner phone calls from Part 19.5, plus referrals from the first ten.

**What to do:**
- Call every checkout abandoner within 24 hours.
- Ask every new member the same single question: "What made you decide to join?" Write the answer down verbatim.
- Start a simple availability log: which requested books you did not have.

**What to learn:**
1. What sentence do members use to describe Sphotik to themselves? **Their words are your next ad.** This is the single most valuable output of this stage.
2. What is the most common objection from abandoners? (My prediction, worth checking: the deposit, then delivery cost, then "is this real.")
3. What is the real distribution of deposit tiers and plan lengths?
4. What is the gap between what your catalogue has and what people want?

**Milestone: rewrite your best-performing ad using a customer's actual words.** Not your words. Theirs.

## Members 25 to 50: find the operational breaking point

**Source:** ads at a stable budget, referrals, and the first organic word of mouth.

**What to do:**
- Introduce a referral offer: "Bring a friend, you both get one free swap delivery." Free delivery is the right referral currency because it costs you ~৳120 and removes the friction you already know is highest.
- Start tracking books-per-member-per-month. This drives your entire cost structure and you currently have no estimate for it.
- Buy second copies of any title requested three or more times.

**What to learn:**
1. **Where do you break?** At 25 to 50 members, a solo founder starts failing at something. Usually it is verification speed, or pickup coordination, or knowing what is on the shelf. Find out which, and fix that one thing before growing.
2. Actual books per member per month.
3. Real delivery cost versus your ৳120 flat fee. Are you losing money on it?
4. Damage and loss rate.
5. What percentage of the catalogue is dead stock?

**Milestone: your first genuine testimonials, from members who have completed two or more cycles.**

## Members 50 to 100: prove it holds

**Source:** ads at a CAC you have decided is acceptable, plus referrals, plus organic.

**What to do:**
- Reinvest membership revenue into inventory, not into ads, until your availability rate (requested book available) is above 70%.
- Formalise anything you are doing by memory. A simple loan spreadsheet with due dates.
- Start watching your first renewal cohort. Members 1 to 10 hit their six-month mark; whether they renew is the most important number in your business.

**What to learn:**
1. **Renewal rate.** Everything in Part 18 depends on it and until now it has been a guess.
2. Does CAC hold as you spend more, or does it climb?
3. Can one person run 100 members? (My guess: barely, at about 15 hours a week, and only with tight processes.)
4. Which member segment renews? Heavy borrowers or light ones?

**Decision at 100 members:** is this a business or an expensive hobby? You will have real CAC, real contribution, real operating hours, and the beginning of real retention data. That is enough to decide.

---
---

# PART 25: Customer feedback

Six touchpoints. Two questions each, maximum. Sent by WhatsApp, because email response rates will be near zero. Every one should take under thirty seconds to answer.

## 1. Immediately after signup (within 2 hours, with the confirmation)

```
You are in. Welcome to Sphotik.

Two quick questions while it is fresh, they take ten seconds:

1. What made you decide to join?
2. Was anything on the website confusing or annoying?

Reply here whenever you get a moment. It genuinely helps.
```

**Why these two:** question 1 gives you ad copy in the customer's own language. Question 2 gives you your funnel fixes. This is the highest-yield feedback moment of the entire relationship, because they have just made a decision and can still remember why.

## 2. After the first delivery arrives (same day)

```
Your books arrived today. How was it?

1. Did they arrive in good condition?
2. Was the delivery cost what you expected?

If anything is wrong, tell me now and I will fix it.
```

**Why:** question 2 is a direct test of the biggest assumption in Part 1.7. If people are surprised by the delivery cost after you published it, your disclosure is not working.

## 3. After the first book is finished (roughly day 10)

```
Have you finished any of your books yet?

1. Which one, and was it what you hoped?
2. Is there a book you wanted from us that we did not have?

The second one is how we decide what to buy next.
```

**Why:** question 2 builds your acquisition list from demand rather than guesswork. Log every answer.

## 4. After the first return completes

```
Your books are back with us, thank you.

1. How easy was the return, out of 5?
2. What would have made it easier?

We know the return is the fiddly part. We are trying to fix it.
```

**Why:** the return is your weakest operational link and your most likely source of churn. Naming it as a known weakness invites honest answers instead of polite ones.

## 5. After the second borrow starts

```
Second set on the way. You are officially a repeat borrower.

One question: if a friend asked you what Sphotik is, what would you say?
```

**Why:** one question, and it is the most valuable single question in this document. Someone on their second cycle understands the product and can explain it. **Their sentence is your next headline.** Collect twenty of these and the pattern in them is your positioning.

## 6. At renewal, 3 weeks before expiry

```
Your membership ends on [date].

1. Are you likely to renew? (Yes / No / Not sure)
2. If not sure or no, what is the reason?

Honest answers are more useful to me than polite ones.
```

**Why:** three weeks gives you time to save the ones who say no, and the reasons are your retention roadmap.

## Rules for all of it

- **WhatsApp, not email, not a form.** Reply rates differ by an order of magnitude.
- **Two questions maximum.** Three gets ignored.
- **Never use a rating scale of more than 5.** And use scales sparingly; free text tells you why.
- **Reply to every answer personally**, even a one-word one. It is the difference between a survey and a relationship.
- **Log every answer in one sheet**, one row per response, with the member ID and the touchpoint.
- **Never offer an incentive for feedback.** It biases the answers toward positive and trains people to expect payment for honesty.
- **Read all of it once a week**, in one sitting, and write down the top three things you learned. Feedback you do not read is worse than feedback you do not collect, because it makes you feel like you are listening.

---
---

# PART 26: Inventory

## 26.1 The MVP shape

| Parameter | Recommendation |
|---|---|
| Total titles at launch | **180 to 250** |
| Copies per title | **1**, except the top 10 to 15 predictable hits, which get 2 |
| Total physical books | **200 to 275** |
| Capital required | **৳55,000 to ৳80,000** at wholesale [ESTIMATE, assuming 25% to 35% off cover] |
| Average cover price | ৳350 to ৳450 |

**Why 200 and not 500:** at 20 members holding an average of 2.5 books each, only 50 books are in circulation at any time. A 200-book shelf gives you a 4:1 catalogue-to-circulation ratio, which is enough to feel like a library without over-investing. At 500 books you would have ৳90,000 tied up in stock that nobody has asked for, before you know what anybody wants.

**Why one copy of most titles:** duplicates are the most expensive way to be wrong. Your `/book/[id]` page already has waitlist functionality, and your homepage already promises honest availability. A waitlist on a popular book is a demand signal you get paid to collect. Two copies of a book nobody wants is ৳800 you cannot get back.

## 26.2 Starting categories

[ASSUMPTION: this mix reflects the Bangladeshi urban reading market as I understand it. Validate against your own knowledge and against Rokomari's bestseller lists, which are public and free.]

| Category | Share | Titles | Notes |
|---|---|---|---|
| **Contemporary Bangla fiction** | 25% | 50 | Humayun Ahmed is non-negotiable, plus Zafar Iqbal, Anisul Hoque, Sunil Gangopadhyay |
| **Translated fiction (Bangla)** | 20% | 40 | Murakami, Marquez, Orhan Pamuk, Khaled Hosseini in Bangla. High demand, high cover price, so high borrow value per book. |
| **English contemporary fiction** | 15% | 30 | Skews toward your English-medium and corporate segment |
| **Self-development and business** | 15% | 30 | High turnover, lower literary status, but people genuinely borrow these. Do not let brand snobbery cost you circulation. |
| **Biography, history, essays** | 10% | 20 | Includes Bangladeshi history and Liberation War titles |
| **Thriller, mystery, crime** | 8% | 16 | Fastest read, fastest returns, best for turns |
| **Classics, both languages** | 5% | 10 | Low turnover but they signal you are a real library |
| **Islamic and spiritual** | 2% | 4 | Genuinely large market segment. Start small, expand if requested. Do not ignore it out of squeamishness; do not overcommit without evidence. |

**Deliberately excluded at launch:** textbooks and exam prep (different business, different customer, high damage), children's books (different buyer, needs different marketing), coffee-table and art books (high value, high damage risk, low turnover), and anything above ৳1,200 cover price (breaks your deposit value caps).

## 26.3 How to detect demand

You have four demand signals and they cost nothing:

1. **`BookRequest` events from `/request`.** Your form already exists. Log every submission with the member's name so you can tell them when it arrives. That message is a retention moment.
2. **`JoinWaitlist` events on book pages.** Three waitlist joins on one title is a clear buy signal.
3. **Catalogue search terms.** Your `/catalog` page has search with URL params. Log the searches that return zero results. **This is your most valuable and most commonly ignored demand signal**: it is people telling you exactly what they wanted and did not find.
4. **Feedback question 3 from Part 25**, "is there a book you wanted that we did not have."

Keep one tab called `Demand`: `date | title | author | source | requester | count`. Sort by count weekly.

## 26.4 When to buy more copies

| Trigger | Action |
|---|---|
| Same title requested or waitlisted **3 times in 30 days** | Buy a second copy |
| A title is out on loan **more than 70% of days** over 30 days | Buy a second copy |
| Same title requested **6 times in 30 days** | Buy a third copy |
| A title has **not been borrowed in 90 days** | Stop buying anything in that category; consider selling the copy |
| Availability rate (requested book actually available) **below 60%** | Stop ad spend and buy inventory instead. An unavailable library is worse than no library. |

**The rule that matters most:** buy the second copy from **revenue**, never from deposits. Deposits are liabilities (Part 18.6). Funding inventory from deposits means every book on your shelf is financed by money you owe back.

## 26.5 How to avoid over-investing

1. **Never buy a book you cannot name a specific reason for.** "It seems popular" is not a reason. "Three people requested it" is.
2. **Buy in small, frequent batches.** ৳10,000 every two weeks, not ৳60,000 at once. Frequent small buys let you respond to real demand.
3. **Negotiate a return arrangement.** Some Bangladeshi wholesalers will accept returns on unsold stock. Ask before you buy. It converts inventory risk into a borrowing cost.
4. **Buy second-hand for the classics.** Nilkhet has good used stock at a fraction of the cover price. Used books are perfectly acceptable in a library, and it lets you widen the catalogue for the same money. **Be honest about condition in the catalogue.** Your entire brand rests on honest listings.
5. **Set a hard ceiling before launch.** Decide now what your maximum inventory spend is before 50 members, and do not exceed it. My recommendation: **৳80,000 before member 50, ৳150,000 before member 100.**
6. **Track inventory turns.** Books borrowed per month divided by total books. Below 0.3 means you have bought too much or the wrong things. Above 1.0 means you are under-stocked and losing members to unavailability.

## 26.6 The uncomfortable arithmetic

At 20 members from this test:
- Revenue: ~৳21,500 membership
- Ad spend: ~৳26,500
- Inventory: ~৳70,000
- **Total invested: ~৳96,500 against ~৳21,500 of revenue**

Plus ~৳10,000 of deposits you are holding but do not own.

**This is normal and you should expect it.** But it means you must be clear-eyed that the USD 250 ad test is the *small* part of your launch cost. The books are the business; the ads are the experiment. If you are choosing between ৳26,500 of ads and ৳26,500 more of books, and your first ten members already tell you the model works, **buy the books.** The library that has what people want will grow by word of mouth. The library that has ads and empty shelves will not grow at all.

---
---

# PART 27: Failure diagnosis

Find the row matching your pattern. Diagnose before acting.

| Pattern | Most likely cause | Second possibility | Action |
|---|---|---|---|
| **High CTR (>1.5%), low landing engagement** (LPV → `ViewMembership` under 15%) | The ad promises something the page does not immediately confirm. Message mismatch. | Page is slow or broken in the in-app browser | Rewrite the hero to echo the ad's exact language. Test load speed on 3G. **Do not change the ad**, it is working. |
| **High landing engagement (>30%), low checkout** (`ViewMembership` → `InitiateCheckout` under 8%) | Price shock at the pay-today number, or deposit confusion | Deposit tier choice is causing paralysis | Run experiment E4 (Part 20.5). Default to the ৳500 tier. Lead with refundability. |
| **High checkout starts, low purchase** (`InitiateCheckout` → `PaymentClaimed` under 10%) | The payment step is technically broken, or state is lost on app switch | Trust collapse at the personal bKash number | **Test the full flow yourself on mobile data through the Facebook in-app browser.** Then add the WhatsApp help line to step 3 and fix state persistence (Part 22, Problem 1). |
| **`PaymentClaimed` high, verified low** | People are claiming payments they did not send | Your verification is too slow and people are giving up | Check bKash against every claim. If the claims are real and you are just slow, verify within 2 hours. If they are fake, add "we verify every transaction ID" to the payment screen. |
| **Good purchases, poor borrowing** (members join and never pick books) | Catalogue is too thin, or the post-purchase path is unclear | Delivery cost is deterring the first order | Personally WhatsApp every member who has not borrowed within 5 days. Ask what they are waiting for. Consider making the first swap delivery free. |
| **High CAC, strong retention signals** (expensive to acquire, but members love it) | Paid social is the wrong channel, not the wrong product | Creative has not been iterated enough yet | Cut ad spend. Shift to referral (free swap for both parties) and community seeding in book groups. A product people love with expensive acquisition is a distribution problem, and referral is the cheapest fix. |
| **Low CTR (<0.8%), strong site conversion** (few click, but most who do convert) | Creative is failing, offer is fine | Audience is wrong | This is the **best** bad outcome. Your offer works. Re-cut hooks (Part 20.4), test new thumbnails, keep everything else identical. |
| **High CPM (>৳350), everything else normal** | Audience too narrow, Page quality issue, or a policy flag | Seasonal competition | Check `Account Quality`. Widen age range. Confirm Advantage+ audience is on. Check for an upcoming festival. |
| **Everything looks fine, zero purchases after ৳12,000** | The offer itself is being rejected: total price too high for the perceived value | The category is not understood despite the explainer | Call ten abandoners (Part 19.5). This is not a diagnosis you can make from a dashboard. |
| **Purchases from outside Dhaka** | Your geo-targeting is set to "in or recently in" | Real demand outside your service area | Fix the setting. Then note the demand. If it repeats, it is a Phase 2 expansion signal. |
| **All conversions from one video** | That video has found the message | Random variance at low volume | Below 8 conversions, assume variance and do not act. Above 8, re-cut the other two using the winner's angle. |
| **Frequency above 3.0 in week one** | Your effective audience is far smaller than you think | Advantage+ audience is off, or interests are too narrow | Check that Advantage+ audience is on. Remove all but two interests. Widen age to 20-50. |
| **Events fire in Test Events but not in live reporting** | Ad blockers, in-app browser restrictions, or the environment variable is missing in production | Domain mismatch | Confirm `NEXT_PUBLIC_META_PIXEL_ID` is set in Cloudflare Pages, not just locally. Check Diagnostics. |
| **Meta reports more purchases than your sheet** | Modelled and attributed conversions at low volume | Duplicate events | **Believe your sheet.** Check for duplicates per Part 5.6. |
| **Your sheet has more members than Meta reports** | Normal. Not all conversions are attributed, especially with delayed server events. | CAPI failing silently | Check `capi_sent`. This is not a problem for decision-making; your sheet is the truth. |

---
---

# PART 28: Decision rules

Explicit. Mechanical. Follow them even when your instinct disagrees, because at this volume your instinct is reading noise.

## 28.1 Kill

```
KILL an ad IF
  spend ≥ ৳2,500 on that ad
  AND InitiateCheckout = 0
  AND CTR < 50% of the best ad in the ad set
  AND it has run ≥ 3 days
→ Pause the ad only. Do not touch the ad set.

KILL the retargeting campaign IF
  frequency > 5.0
  OR spend ≥ ৳2,000 with 0 purchases
→ Pause. Put the budget into prospecting.

KILL the whole experiment IF
  spend ≥ ৳15,000 AND verified members = 0
  OR CAC > ৳2,500 after ৳15,000
  OR CTR < 0.8% after two creative refreshes
→ Stop. Preserve the remaining budget. Go to Part 24.
```

## 28.2 Keep

```
KEEP running unchanged IF
  it has been live < 72 hours
  OR the ad set was edited < 48 hours ago
  OR CTR ≥ 1.0% and it is before Day 6
  OR you have < 5 verified members and are before Day 12
→ Do nothing. Doing nothing is a decision and usually the right one.
```

## 28.3 Scale

```
SCALE IF
  verified members ≥ 5
  AND CAC ≤ ৳1,200
  AND funnel rates are stable across two consecutive 3-day windows
→ Raise the campaign budget by 30%. Once. Wait 72 hours.
→ Repeat only if CAC holds.

HARD STOP ON SCALING IF
  you have fewer books than members × 3
  OR you cannot verify payments within 4 hours
  OR any of your first 10 members has not completed a return
→ Ads are the easy part. Never let acquisition outrun fulfilment.
```

## 28.4 Change creative

```
CHANGE CREATIVE IF
  all 3 ads CTR < 0.8% after ৳6,000
→ Re-cut the first 3 seconds of all three. Keep everything else.

  OR frequency > 2.5
→ Fatigue. New hooks.

  OR CTR dropped > 30% from its 3-day peak while CPM held steady
→ Fatigue. New hooks.

  OR one ad is 2x better on cost per InitiateCheckout with ≥ 8 events
→ Re-cut the other two toward the winner's angle.

DO NOT change creative IF
  CTR ≥ 1.2% (the creative is working; the problem is downstream)
```

## 28.5 Change the landing page

```
CHANGE THE LANDING PAGE IF
  CTR ≥ 1.2% AND LPV → ViewMembership < 15% after 300 LPVs
→ Rewrite the hero to match the ad's language exactly.

  OR ViewMembership → InitiateCheckout < 8% after 60 ViewMembership
→ Rework the deposit and pay-today presentation.

RULES
  Change ONE element at a time.
  Change it ONCE per phase, never mid-phase.
  Note the date and time in your log so you can split the data.
```

## 28.6 Change the offer

```
CHANGE THE OFFER IF
  ViewMembership ≥ 100 AND InitiateCheckout ≤ 5
→ Price or total-cost perception is the barrier.

WHAT TO CHANGE, IN THIS ORDER
  1. Make the 14-day guarantee more prominent (free, no revenue impact)
  2. Default to the ৳500 deposit tier (lowers pay-today to ৳1,400)
  3. Offer the first swap delivery free (costs ~৳120 per member)
  4. Split payment: membership now, deposit at first delivery (operationally risky, do last)

WHAT NOT TO CHANGE
  The membership price. Cutting to ৳700 would only teach you that
  people pay ৳700, which is not the question you are funding.
```

## 28.7 Budget

```
INCREASE BUDGET IF
  CAC ≤ ৳1,200 AND ≥ 5 verified members AND stable for 3 days
→ +30%, once, then wait 72 hours.

HOLD BUDGET IF
  CAC ৳1,200 to ৳2,000
→ Do not increase. Fix one funnel step instead.

DECREASE BUDGET
→ Never mid-phase. It resets learning and muddies the data.
   If you want to spend less, stop entirely at a phase boundary.

STOP SPENDING IF
  total delivered spend ≥ ৳22,000
→ Stop and analyse before deciding about the reserve.
```

## 28.8 The final decision, Day 19

| Result | Verdict | Next |
|---|---|---|
| ≥ 10 members, CAC ≤ ৳1,200 | **Validated.** People pay. | Stop ads. Deliver excellently to all of them. Reassess at renewal. |
| 5 to 9 members, CAC ৳1,200 to ৳2,000 | **Promising, not proven.** | Stop ads. Serve them. Fix the worst funnel step. Retest in 6 weeks with new creative built from their words. |
| 2 to 4 members, CAC > ৳2,000 | **Not via cold paid social, yet.** | Stop paid. Go to Part 24. Get to 25 members by hand. Retest paid when you have real testimonials. |
| 0 to 1 members | **Rejected at this price, on this channel, with this message.** | Stop. The honest conclusion is that a ৳1,400 first payment to an unknown brand is too big an ask from a cold ad. Options: lower the first payment, warm the traffic first, or find a channel with more inherent trust (book communities, university groups, a physical presence). |

**In every one of these cases, stop the ads and go operate.** The members you have are worth more than the members you might get, because they are the ones who will tell you why this works or does not.

---
---

# PART 29: Launch calendar

## Preparation, Days -3 to -1

### Day -3: Infrastructure
- [ ] Fix the order recorder (Part 2, Step 0a)
- [ ] Capture `fbp`, `fbc`, and UTMs into sessionStorage (Step 0b)
- [ ] Split `Purchase` into `PaymentClaimed` and the server event (Step 0c)
- [ ] Persist checkout state across app switching (M4)
- [ ] Replace the placeholder bKash, Nagad, Rocket numbers
- [ ] Replace the placeholder phone number on `/contact`
- [ ] Decide the domain: `sphotik.com` or `sphotik.com.bd`. One.

### Day -2: Meta setup
- [ ] Facebook Page created, filled, 4+ organic posts published
- [ ] Instagram created, bio set, 6 posts published
- [ ] Business Portfolio created, business info complete, email confirmed
- [ ] Ad account created (BDT, Asia/Dhaka)
- [ ] Payment method added, TIN and BIN entered
- [ ] Domain verified via DNS TXT
- [ ] Dataset `Sphotik - Web` created, Pixel installed, deployed to production
- [ ] All 7 custom audiences created
- [ ] 4 custom conversions created

### Day -1: Content and verification
- [ ] Three videos exported in 9:16, 1:1, 4:5 with burned-in subtitles
- [ ] Landing page copy updated (Part 8.5)
- [ ] Pricing updated to ৳900 / ৳1,500
- [ ] **M9 fixed: checkout defaults to 6 months + ৳500, pay-today reads ৳1,400**
- [ ] Flat ৳120 swap delivery published
- [ ] 14-day guarantee published in three places
- [ ] **Every line of Part 5 verified**
- [ ] 3 real end-to-end test checkouts, including one on a phone through the Facebook in-app browser with a real ৳10 bKash transfer
- [ ] Five strangers shown the landing page for 8 seconds and asked "what is this and what does it cost"
- [ ] Campaign built and saved as a **draft**, not published

**If any Part 5 line fails, delay the launch. A day of delay costs nothing. Launching broken costs the whole budget.**

---

## Launch day, Day 1 (a Saturday)

**Morning:**
- [ ] Final check: Pixel firing in production, in a private window
- [ ] Publish the campaign at ৳1,200/day
- [ ] Confirm it is `Active`, not `In review` and stuck
- [ ] Screenshot the campaign settings for your records

**Afternoon:**
- [ ] Confirm first impressions are delivering (usually within 2 hours)
- [ ] Confirm `PageView` events appearing in Events Manager
- [ ] Click your own ad once, from a phone, and complete a landing page view. Confirm the UTMs land correctly.

**Evening:**
- [ ] Record CPM, impressions, clicks in your dashboard sheet
- [ ] Write one sentence in the log

**What NOT to do on Day 1:**
- Do not judge performance
- Do not pause anything
- Do not change budget
- Do not edit any ad
- Do not check the dashboard more than twice
- Do not message friends asking them to click. It corrupts your data and teaches the algorithm to find people like your friends.

---

## Days 2 to 7

| Day | Do | Do NOT |
|---|---|---|
| 2 | Record metrics. Reply to every comment and DM. | Change anything |
| 3 | Record metrics. First look at CTR by ad. | Pause anything. Change budget. |
| 4 | Record metrics. Check Events Manager Diagnostics. | Change anything |
| 5 | **Decision gate 1** (Part 15). Record metrics. | Change more than the gate specifies |
| 6 | Move to Phase 2 budget. Turn on retargeting **if** `WEB-ALL-30D` ≥ 700. Apply the gate 1 decision. | Change targeting, placements, or the optimisation event |
| 7 | Record metrics. Do nothing else. Wait for post-edit learning to settle. | Judge the Phase 2 change yet |

**The whole-week rule: exactly one change, at Day 6, and only the one the gate specified.** Every other day is record, respond, wait.

---

## Week 2, Days 8 to 14

| Day | Do | Do NOT |
|---|---|---|
| 8 | Apply the kill rule to any failing ad (Part 28.1) | Kill more than one ad |
| 9 | Record metrics. Call any checkout abandoners. | Change the page |
| 10 | Start experiment E4 (deposit presentation) if the trigger is met | Start two experiments |
| 11 | Record metrics. Verify every claimed payment same-day. | Anything else |
| 12 | **Decision gate 2** (Part 15) | Ignore the gate because you feel optimistic |
| 13 | Apply the gate 2 phase (3A, 3B, or 3C) | Blend two phases |
| 14 | Record metrics. Wait for learning to settle. | Judge the Day 13 change |

**Also this week, and it matters more than the ads:** deliver books to every member who has joined. Personally. Ask them the Part 25 questions. **The information from ten real members outweighs the information from ৳12,000 of ad spend.**

---

## Weeks 3 to 4, Days 15 to 25

| Day | Do |
|---|---|
| 15-18 | Record metrics daily. Serve members. Collect feedback. No campaign changes. |
| 19 | **Final decision (Part 28.8).** Pause everything. |
| 20-21 | Full analysis. Reconcile Meta's numbers against your sheet. Calculate real CAC, real contribution, real cash position. |
| 22-23 | Write down what you learned: what converted, what did not, what members actually said, what the funnel numbers were, where the money went. One page. |
| 24-25 | Decide the next experiment. Not the next campaign. The next experiment. |

**Things you must NOT change during the entire 19 days:**
- Membership price
- Deposit tiers or amounts
- Geographic targeting
- The optimisation event (except under the single narrow condition in Part 13.4)
- Placements
- Campaign objective
- The three videos, other than re-cutting hooks
- More than one landing page element per phase

**Things you should change constantly:**
- How fast you verify payments
- How fast you reply to messages
- What you learn from members
- Your inventory, based on requests

---
---

# PART 30: Final execution checklist

Follow top to bottom. Do not tick anything you have not actually verified.

## Infrastructure (Task Zero, before everything)

- [ ] Order recorder live: a test POST appears as a row in the Google Sheet
- [ ] `fbp`, `fbc`, and all five UTMs captured on landing and stored to sessionStorage
- [ ] Checkout state persists across an app switch (tested on a real phone, real bKash app)
- [ ] Browser `Purchase` removed; `PaymentClaimed` fires instead
- [ ] Server-side `Purchase` sends only for rows marked verified
- [ ] Real bKash, Nagad, and Rocket numbers replace `01XXXXXXXXX`
- [ ] Real phone number replaces `+880 1700 000000` on `/contact`
- [ ] One domain chosen; the other redirects to it
- [ ] `NEXT_PUBLIC_META_PIXEL_ID` set in **Cloudflare Pages**, not just `.env.local`

## Meta assets

- [ ] Facebook Page ready: name, username, category `Library`, profile, cover, bio, CTA, contact, pinned post
- [ ] 4+ organic posts published, including a photo of the real shelves
- [ ] Instagram ready: business account, bio, link, 4 highlights, 6 posts
- [ ] Business Portfolio ready: created, business info complete, email confirmed
- [ ] Page and Instagram both added to the portfolio and cross-assigned
- [ ] Ad Account ready: `Sphotik - BD - Ads 01`, **BDT**, **Asia/Dhaka**, you as admin
- [ ] Payment method added and verified with a successful first charge
- [ ] TIN, and BIN if available, entered in billing
- [ ] Domain verified: green badge in Brand Safety, and `nslookup -type=TXT` confirms it

## Tracking

- [ ] Dataset `Sphotik - Web` created and assigned to the ad account
- [ ] Pixel installed and firing in **production**
- [ ] `PageView` fires on client-side route changes (navigate `/` → `/member` without reloading)
- [ ] All browser events fire once each, with correct parameters, in the correct order
- [ ] `value` is a number, `currency` is `"BDT"`, on every valued event
- [ ] Zero personal data in any browser event
- [ ] CAPI access token stored in Apps Script properties, absent from the repo
- [ ] Test `Purchase` visible in Test Events with source **Server** and good match quality
- [ ] No duplicate `Purchase` after running the CAPI job twice
- [ ] Events Manager Diagnostics: zero errors
- [ ] 4 custom conversions created and receiving events
- [ ] 7 custom audiences created (they must exist before launch to start collecting)

## Product

- [ ] Landing page rewritten: kicker, H1, subheadline with the ৳1,400 pay-today, one CTA, trust strip
- [ ] Pay-today module above the fold
- [ ] Real shelf photograph on the homepage
- [ ] Founder section with a photo and a name
- [ ] Flat ৳120 swap delivery published on the homepage and `/delivery`
- [ ] 14-day guarantee published on the homepage, `/member`, and `/membership-rules`
- [ ] Pricing set to ৳900 / ৳1,500 with regular framed as "the price after launch"
- [ ] **Checkout defaults fixed (M9): `defaultDepositIdx = 0` and plan fallback `membershipDurations[0]`. Load `/checkout` in a fresh browser and confirm it shows pay-today ৳1,400, not ৳2,600.**
- [ ] Email made optional in `DetailsStep`
- [ ] WhatsApp help line on checkout step 3
- [ ] "We will never ask for your PIN or OTP" on the payment screen
- [ ] Deposit refund rules stated identically in three places
- [ ] Full checkout completed on a phone, via mobile data, in the Facebook in-app browser, with a real ৳10 transfer
- [ ] Five strangers passed the eight-second comprehension test

## Inventory and operations

- [ ] 180+ titles physically on the shelf and listed accurately
- [ ] Courier partner chosen; swap cost confirmed against your ৳120 flat fee
- [ ] Packaging materials bought
- [ ] Loan tracking sheet ready
- [ ] You can verify a bKash payment and reply within 2 hours

## Creative

- [ ] `V1-MATH` exported: 9:16, 1:1, 4:5, subtitles burned in
- [ ] `V2-HOW` exported: same
- [ ] `V3-WHY` exported: same
- [ ] `STATIC-FAQ` image created for retargeting
- [ ] Ad copy written for all five ads: primary text, headline, description
- [ ] Alternative hooks prepared for all three videos, ready to swap

## Campaign

- [ ] `SALES-PROSPECT-DHK-2609` built as a **draft**
- [ ] Objective Sales, conversion event `Purchase`, highest-volume bidding
- [ ] Ad set `BROAD-2245-AUTO`: Dhaka (people **living** in), 22-45, all genders, **no language set**
- [ ] 4-6 reading interests as an OR group; Advantage+ audience **ON**
- [ ] Automatic placements
- [ ] Purchaser and claimer audiences excluded
- [ ] Three ads attached with correct identity, destination, and CTA
- [ ] UTM block in the URL parameters field on every ad
- [ ] Advantage+ creative enhancements **OFF** except aspect-ratio adaptation
- [ ] `SALES-RETARGET-DHK-2609` built as a draft, **paused**, for Day 6

## Tracking verification (do this last, immediately before publishing)

- [ ] Walk the entire funnel one more time in production with Pixel Helper open
- [ ] Every event fires, once, with correct parameters
- [ ] A row lands in your Google Sheet with UTMs, `fbp`, and `fbc` populated
- [ ] No `Purchase` fires from the browser

## Launch

- [ ] Publish at ৳1,200/day on a Saturday morning
- [ ] Confirm `Active` status within 2 hours
- [ ] Click your own ad once and confirm the UTMs arrive intact

## Monitor

- [ ] Dashboard sheet updated daily, at the same time, in 15 minutes
- [ ] One sentence written in the log every day
- [ ] Every comment and DM answered within a few hours
- [ ] Every claimed payment verified same day
- [ ] Every abandoner called within 24 hours
- [ ] Decision gate 1 taken on Day 5
- [ ] Decision gate 2 taken on Day 12

## Analyse

- [ ] Meta's numbers reconciled against your sheet (believe the sheet)
- [ ] Real CAC calculated
- [ ] Contribution after CAC calculated
- [ ] Cash collected recorded separately from revenue
- [ ] Full funnel conversion rates at every step
- [ ] Member quotes collected from the Part 25 questions

## Decide

- [ ] Final decision made against the Part 28.8 table
- [ ] One page written on what you learned
- [ ] Next experiment defined (not the next campaign)

---
---

# Closing: what this test can and cannot tell you

**It can tell you:** whether a cold stranger in Dhaka, who has never heard of you, will pay ৳1,400 after watching a video and reading a website. That is a real and important question and almost nobody validates it before building.

**It cannot tell you:** whether the business works. That depends on renewal, on books per member per month, on real delivery costs, and on whether you can run it as one person. None of those are measurable in nineteen days, and no amount of ad spend will make them measurable sooner.

**So the most likely useful outcome of USD 250 is not twenty members.** It is a clear read on which of the three angles people respond to, a specific diagnosis of where your funnel leaks, twenty phone conversations with people who nearly bought and did not, and five to fifteen real members you can serve properly and learn from.

If you get that, the money was well spent, even at an uncomfortable CAC.

**One last thing.** The most common failure mode for a solo founder running their first paid test is not choosing the wrong audience or the wrong creative. It is checking the dashboard eleven times a day, editing the ad set on day two because a number looked bad for six hours, and thereby resetting learning so many times that the test never produces a readable result.

Set it up carefully. Launch it. Then leave it alone and go pack some books.

---

## Sources

**Meta official (primary):**
- Meta Business Help Centre, "Create a business portfolio in Meta Business Suite" (help ID 1710077379203657)
- Meta Business Help Centre, "About datasets in Meta Events Manager" (help ID 750785952855662)
- Meta Business Help Centre, "About Meta's Aggregated Event Measurement" (help ID 721422165168355)
- Meta Business Help Centre, "Taxes on Meta Ads Placement" (help ID 133076073434794)
- Meta Business Help Centre, "About business verification in Meta Business Suite"
- Meta Business Help Centre, "How to create an ad account in Meta Ads Manager"
- Meta for Developers, "Meta Pixel Reference" (standard events and parameters)
- Meta for Developers, "Meta Pixel Implementation: Conversion Tracking"
- Meta for Developers, "Deduplicate Pixel and Server Events"
- PPC Land, "Meta launches unified API structure for Advantage+ campaigns" (reporting Meta's 29 May 2025 announcement)

**Secondary (practitioner and industry, identified as such in the text):**
- Bangladesh CPM, CPC, and CPA benchmark ranges: Agent Wise X, Socialeum (2026 Bangladesh figures)
- New ad account spending limits: aggregated practitioner reports; not published by Meta
- bKash as a Meta Ads payment method in Bangladesh: Views Bangladesh and Network Bangladesh (Aug 2026); Meta has made no official announcement
- The 17.25% / 32.25% bKash-plus-tax figures: a single LinkedIn practitioner post, unverified against official sources
- Learning phase practice and the event-proxy ladder: multiple 2026 practitioner guides, consistent with Meta's published ~50 events per week guidance
- Advantage+ setup behaviour after the February 2026 Ads Manager overhaul: 1ClickReport, MBADV

**Your codebase (verified directly):**
- `src/data/content.ts`, `src/data/checkout.ts`, `src/app/checkout/page.tsx`, `src/lib/analytics.ts`, `src/app/layout.tsx`, `src/app/ClientProviders.tsx`, `src/app/api/return-requests/route.ts`, `.env.example`, `public/_redirects`, `package.json`
