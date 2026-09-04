# Sphotik Meta Launch Master Prompt

Act as an experienced digital marketer, Meta Ads specialist, growth strategist, CRO specialist, content strategist, and early-stage startup operator.

I am a solo founder launching SPHOTIK in Bangladesh. I cannot afford a professional digital marketer, so I need you to act as my complete growth team and give me a practical, step-by-step operating manual that I can execute myself.

full context of the project is in the file folder you can go through.

## Core business

Sphotik is a membership-based library/book rental service in Bangladesh.

The model is:

- Membership fee = access to the library
- Security deposit = borrowing capacity
- Delivery fee = logistics
- No per-book rental fee

Customer journey:

Facebook/Instagram ad -> website -> understand Sphotik -> membership -> choose deposit -> checkout -> payment -> paid member -> borrow -> return -> borrow again

Brand:
"Read more. Own less."

Positioning:
"A neighbourhood library, run the modern way."

Brand personality:
modern, literary, calm, premium but accessible, transparent, honest, trustworthy.

## Current pricing

Regular:
- 6 months: ৳1,200
- 12 months: ৳2,000

Founding launch pricing:
- 6 months: ৳900
- 12 months: ৳1,600

Security deposit:
- ৳500 -> up to 2 books -> max total book value ৳700 -> 10 days
- ৳1,000 -> up to 4 books -> max total book value ৳1,400 -> 14 days
- ৳1,500 -> up to 6 books -> max total book value ৳2,000 -> 21 days

Delivery:
- paid separately by member for each shipment
- outbound and return are separate trips
- multiple books in one shipment count as one trip
- launch with private courier
- later may test Bangladesh Post/Speed Post in selected areas

Early cancellation:
- membership access ends
- membership fee is not refunded for unused time
- deposit remains held until original membership period ends
- then member may request eligible deposit refund
- refund within 3 to 7 working days after request, assuming all books/charges are settled

These are V1 assumptions and may be changed after real-world data.

## Validation goal

This is a startup validation campaign, not a mature scaling campaign.

Total initial Meta Ads budget:
USD 250

The real question is:

"Will cold Facebook/Instagram users in Bangladesh understand Sphotik and pay to become members?"

Primary success signal:
PAID MEMBERS

Not:
- impressions
- likes
- comments
- cheap clicks
- video views
- form fills

Those are diagnostic signals.

## Critical research requirement

This task depends on current Meta platform behavior. Before giving implementation instructions, research the latest official Meta documentation and current best practices.

Verify current processes/UI for:
- Meta Business Portfolio / Business Manager
- Facebook Page
- Instagram connection
- ad account
- payment method
- Meta Pixel
- Conversions API
- domain verification
- Events Manager
- standard events
- custom events
- custom conversions
- campaign objectives
- Sales objective
- Purchase optimization
- audience setup
- Advantage+ options if relevant
- placements
- attribution/reporting
- retargeting
- any current setup requirements

Use official Meta sources first. Use secondary sources only where useful and identify them as secondary.

Do not rely on old Meta interface instructions.

## Part 1: Audit the business before launch

Critically evaluate:
1. Is ৳900/৳1,600 a good founding offer?
2. Is ৳1,200/৳2,000 a good regular price?
3. Is the deposit structure psychologically attractive?
4. Could the deposit create conversion friction?
5. Is membership-library positioning stronger than "book rental"?
6. Is "Read more. Own less." strong enough?
7. Does delivery create too much friction?
8. What should change before spending money?

Separate recommendations into:
- MUST CHANGE BEFORE LAUNCH
- SHOULD TEST
- CAN WAIT

Do not automatically agree with my assumptions.

## Part 2: Zero-to-launch checklist

Give me a sequential checklist from zero:

1. Prepare business assets
2. Create Facebook Page
3. Create Meta Business Portfolio
4. Connect Instagram
5. Create ad account
6. Add payment method
7. Complete business details
8. Verify domain
9. Install Pixel
10. Set up Conversions API if appropriate
11. Configure events
12. Test events
13. Create UTMs
14. Connect analytics if appropriate
15. Create audiences
16. Build campaigns
17. Launch

For every step, tell me:
- exactly where to go
- what to click
- what to name it
- what data I need
- what success looks like
- common errors
- how to verify it

Assume I am technical but not a Meta Ads expert.

## Part 3: Naming convention

Create a clean naming convention for:
- business portfolio
- page
- ad account
- pixel
- campaigns
- ad sets
- ads
- audiences
- UTMs

Keep it simple for a solo founder.

## Part 4: Tracking architecture

Design a complete event architecture.

Consider:
PageView
ViewContent
ViewMembership
SelectMembership
SelectDeposit
InitiateCheckout
Purchase
JoinWaitlist
BookRequest

Tell me:
- which are standard Meta events
- which are custom events
- which should be custom conversions
- exact trigger/page/action
- recommended parameters
- currency
- value
- what data should not be sent

### Important purchase-value question

A founding customer may pay:
৳900 membership + ৳500 refundable deposit = ৳1,400 today.

Should Meta Purchase value be:
A. ৳1,400
B. ৳900
C. another value

Analyze from both performance marketing and accounting perspectives and recommend one. Explain the tradeoff.

## Part 5: Verify tracking

Give me a verification checklist for:
- Pixel installed
- browser events
- CAPI if used
- event deduplication
- Purchase fires only after successful payment
- no duplicate Purchase
- InitiateCheckout
- parameters
- domain verification
- event configuration
- Test Events
- Diagnostics

## Part 6: Facebook Page setup

Give exact recommendations and ready-to-use copy for:
- page name
- username
- category
- profile image
- cover image
- description
- bio
- CTA
- website link
- contact details
- pinned post
- credibility setup

## Part 7: Instagram setup

Give:
- username ideas
- bio
- link strategy
- profile setup
- highlights
- first few posts/Reels
- cross-posting approach

Keep it MVP-level.

## Part 8: Landing page

Analyze the website for cold traffic.

Tell me:
- what must be above the fold
- what should be removed
- what should be added
- hero headline
- subheadline
- CTA
- pricing
- deposit explanation
- delivery
- availability
- trust
- FAQ
- membership rules

Give exact recommended landing-page copy.

## Part 9: Create exactly 3 campaign videos

I want 3 main videos, generally 30 to 90 seconds.

They must use DIFFERENT psychological angles, not just different edits.

Choose the best 3 angles yourself.

For each video give:
- strategic objective
- psychological trigger
- exact hook
- exact script
- scene-by-scene storyboard
- voiceover
- on-screen text
- visual direction
- music/sound direction
- pacing
- CTA
- ideal duration
- thumbnail text
- first 3 seconds
- last 5 seconds
- what to avoid
- language choice: English/Bangla/mixed and why

The scripts must be usable directly by a video editor.

## Part 10: Ad copy

For each video give:
- primary text
- headline
- description
- CTA
- short version
- longer version

Everything must work for cold users who have never heard of Sphotik.

## Part 11: Audience strategy

Recommend practical targeting for Bangladesh.

Analyze:
- broad targeting
- interest targeting
- book/literature interests
- students
- young professionals
- households
- fiction readers
- self-development
- Bangla literature
- translated fiction

Do not over-segment.

Budget is only USD 250.

Explain:
- what to target
- what NOT to target
- why
- when broad beats interests
- retargeting
- lookalikes and whether they are useful at this stage

## Part 12: Geography

Recommend the launch geography based on:
- actual serviceable area
- delivery economics
- customer density
- likely reader population
- operational practicality

Do not recommend areas we cannot fulfill.

## Part 13: Campaign objective

Compare:
- Awareness
- Traffic
- Engagement
- Leads
- Sales

Recommend the best objective for the validation goal.

Explain:
- primary optimization event
- cold-start issue if Purchase volume is low
- what to do without corrupting the experiment

Do not simply say "use Sales" without explaining the data issue.

## Part 14: Exact campaign structure

Give the exact:
- campaigns
- ad sets
- ads
- budgets
- schedule
- exclusions
- placements
- audience structure

Keep it appropriate for USD 250.

Avoid excessive fragmentation.

## Part 15: USD 250 budget plan

Create a day-by-day or phase-by-phase plan covering the full test.

Show:
- budget/day
- purpose
- what is running
- what to inspect
- what decision to make

Do not assume I should spend all USD 250 automatically.

Use conditional rules:
IF X -> DO Y
IF X -> DO Z
IF X -> STOP

## Part 16: Test duration

Recommend:
- total test length
- minimum learning period
- when NOT to judge performance
- when to pause an ad
- when to shift budget
- when to refresh creative
- when to stop the experiment

Do not overreact to a few hours of data.

## Part 17: Metrics

Create a dashboard.

PRIMARY:
- paid members
- CAC
- membership revenue
- contribution after CAC

SECONDARY:
- checkout starts
- membership views
- landing-page conversion

DIAGNOSTIC:
- CPM
- CTR
- CPC
- video retention
- landing-page engagement
- checkout abandonment

Tell me which metrics matter at each stage.

## Part 18: CAC and unit economics

Build a framework for:
- acceptable CAC
- warning CAC
- dangerous CAC
- first-order profitability
- annual vs six-month CAC tolerance
- membership-only economics vs total cash collected

Do not assume perfect retention.

Use conservative and optimistic cases.

## Part 19: Retargeting

Design practical retargeting for:
- video viewers
- social engagers
- website visitors
- membership viewers
- checkout starters
- abandoners

Give:
- audience windows
- budget
- creative/message
- exclusions
- when audience size is too small

## Part 20: A/B testing

I only have 3 videos.

Tell me how to test:
- hooks
- offers
- CTA
- landing-page headline
- pricing presentation
- deposit explanation

Avoid testing too many variables simultaneously.

Create a simple experiment matrix.

## Part 21: Pricing audit

Compare:
A. 6m ৳900 / 12m ৳1,600
B. 6m ৳900 / 12m ৳1,700
C. 6m ৳1,000 / 12m ৳1,700
D. any better structure you recommend

Consider:
- conversion psychology
- perceived value
- annual commitment
- CAC
- cash flow
- refundable deposit
- friction

Tell me what I should actually launch with.

## Part 22: Checkout

Audit:
Ad -> landing -> membership -> deposit -> checkout -> payment -> success.

Focus on:
- "Pay today"
- membership vs refundable deposit
- delivery being separate
- trust
- mobile experience
- Facebook in-app browser
- payment friction

Give the exact ideal sequence.

## Part 23: Trust system

Design minimum trust signals for a new brand:
- real identity
- location
- support
- refund/deposit rules
- delivery details
- phone/WhatsApp
- Facebook/Instagram presence
- genuine testimonials only
- payment trust

Do not recommend fake reviews or fake social proof.

## Part 24: First 100 members

Give an operating plan for:
- first 10
- 10 to 25
- 25 to 50
- 50 to 100

Tell me what to learn at each stage.

## Part 25: Customer feedback

Design lightweight feedback after:
- signup
- first delivery
- first book
- first return
- second borrow
- renewal

Give exact questions.

## Part 26: Inventory

Recommend an MVP inventory approach:
- starting categories
- number of titles
- copies per title
- how to detect demand
- when to buy more copies
- use of book requests
- avoid over-investing

## Part 27: Failure diagnosis

Create a table such as:

High CTR + low landing engagement -> likely issue -> action

High landing engagement + low checkout -> likely issue -> action

High checkout + low purchase -> likely issue -> action

Good purchase + poor borrowing -> likely issue -> action

High CAC + strong retention -> action

Low CTR + strong site conversion -> action

## Part 28: Decision rules

Create explicit IF/THEN rules for:
- kill
- keep
- scale
- change creative
- change landing page
- change offer
- increase/decrease budget

## Part 29: Launch calendar

Give me:
- preparation day(s)
- launch day
- days 2-7
- week 2
- week 3-4

Tell me exactly what I should do and what I should NOT change.

## Part 30: Final execution checklist

Finish with one checklist I can literally follow top-to-bottom:

[ ] Facebook Page ready
[ ] Business Portfolio ready
[ ] Ad Account ready
[ ] Domain verified
[ ] Pixel installed
[ ] CAPI configured if recommended
[ ] Events tested
[ ] UTMs ready
[ ] Landing page ready
[ ] Membership page ready
[ ] Checkout ready
[ ] Payment working
[ ] 3 videos ready
[ ] Ad copy ready
[ ] Audiences ready
[ ] Campaign created
[ ] Tracking verified
[ ] Launch
[ ] Monitor
[ ] Analyze
[ ] Decide next step

## Important constraints

- I am a solo founder.
- Total initial Meta Ads budget is USD 250.
- I want validation, not premature scaling.
- I want real paying members.
- I want to protect cash.
- I can change pricing and rules later based on evidence.
- Avoid unnecessary paid tools.
- Avoid over-engineering.
- Clearly distinguish facts, current Meta behavior, assumptions, estimates, and recommendations.
- Cite current Meta-specific facts.
- Do not claim an implementation is complete unless it actually is.
- Do not use em dashes or long dash punctuation in user-facing copy. Use commas, colons, parentheses, semicolons, or regular hyphens.

## Final goal

Take me from:

ZERO

to:

Business setup
-> Facebook Page
-> Meta Business
-> Ad Account
-> Domain verification
-> Pixel
-> CAPI if appropriate
-> Events
-> Tracking verified
-> Landing page
-> 3 campaign videos
-> Ads live
-> First paying members
-> Campaign analysis
-> Next decision

I want a practical operating manual, not generic advice.

Use the project context I provide, challenge my assumptions, research current Meta practices, and tell me exactly what to DO.
give me the output in a .md file
