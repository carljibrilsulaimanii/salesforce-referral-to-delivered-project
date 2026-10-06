<!--
  Author:        C. Jibril Sulaiman II
  Date:          2026-09-26
  For:           Fast Slow Motion - Solution Architect interview assignment (video presentation)
  Deliver to:    Loom/Zoom recording (<= 30 min) replied to michael.smith@fastslowmotion.com
  Due:           ~2026-09-29
  What:          Timed presentation outline + spoken script for the FSM Salesforce build
  Why:           Graded on business acumen, communication, and Salesforce knowledge. The brief says
                 "treat us like a client, train us on the customizations, teach us how to use the system."
                 So this is a TRAINING walkthrough, not a feature tour. Opens with the Miro process flow,
                 then a live Salesforce demo following that same flow. Keep it simple; name the tradeoffs.
  Assets:        Miro flow  -> https://miro.com/app/board/uXjVHiXb-zU=/
                 Build plan  -> FSM_Salesforce_Build_Plan.md
-->

# FSM Presentation Script - Lead to Delivered Project

## Delivery facts (do not skip)
- **Format:** screen-recorded walkthrough, **under 30 minutes** (target ~24). Reply to Michael's email with the video link.
- **Framing:** you are the consultant; **FSM is your client.** Talk like you are onboarding them, not demoing to an interviewer.
- **The one instruction that wins or loses this:** *train and teach.* Show them how to USE it, and say the business *why* behind every click.
- **Say once, out loud:** "We deliberately kept this simple and did not build X." (Restraint is graded here.)

## Quick links (open these tabs / click during the demo)
Org base: `https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com`

| What | Link |
|------|------|
| Miro flow (start here) | https://miro.com/app/board/uXjVHiXb-zU=/ |
| Salesforce org home | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com |
| Acme Inc **Opportunity** | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Opportunity/006fj00000MwtdlAAB/view |
| Jordan Bailey / Riverside **Lead** (convert live in Sec 4) | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Lead/00Qfj00000nKWNiEAO/view |
| **Leads** list | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/o/Lead/list |
| **Accounts** list (the 4 entities) | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/o/Account/list |
| **Projects** list | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/o/Projects__c/list |
| **Pipeline & Revenue by Referral Partner** report | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Report/00Ofj00000Jjs1JEAR/view |
| Setup: **Flows** | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/setup/Flows/home |
| Setup: **Path Settings** | https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/setup/PathAssistantSetupHome/home |
| **Partner referral form** (hosted) | https://carl.jibrilsulaiman.com/fsmdemo-page?ref=demo  *(use the `?ref=demo` version - see cache note)* |

*(Tip: pre-load the Opportunity, Accounts list, Projects list, and Reports in separate tabs before recording so you're not typing URLs on camera.)*

## Setup before you hit record
- Open tabs: the **Miro board** (start here), the hosted **referral form**, and the **Salesforce org** (Acme Inc opportunity + records staged).
- **Pre-create a `Summit Growth Partners` account (record type Referral Partner)** so the Section 4 qualify step can link the Riverside lead's referrer to a real partner record on camera.
- Acme Inc opportunity is at **Prospecting** and its account is a **Prospect** (already reset) - so Closed Won flips it to Client and creates the Project LIVE.
- Delete or ignore the earlier test leads (Taylor Morgan / Northwind Bakery) so the Leads list looks clean.
- Zoom the browser to ~110-125% so text is readable on playback.
- Close Slack/email/notifications. Test mic. Do one dry run.

---

## Run of show (target 24:00 / hard cap 30:00)

| # | Section | Time | Screen |
|---|---------|------|--------|
| 1 | Intro & agenda | 0:30 | You / Miro |
| 2 | The system at a glance (Miro flow) | 3:00 | Miro |
| 3 | Capture: referral lead via the form | 2:30 | Salesforce |
| 4 | Qualify & convert | 3:00 | Salesforce |
| 5 | Work the opportunity (custom Path) | 3:30 | Salesforce |
| 6 | Close Won: project + client, automatically | 3:00 | Salesforce |
| 7 | The four entities you track | 2:30 | Salesforce |
| 8 | Referrals, measured | 1:30 | Salesforce |
| 9 | Why it stays simple (design choices) | 2:30 | Miro / You |
| 10 | What we would do next (roadmap) | 1:30 | Miro / You |
| 11 | Close & questions | 0:30 | You |

---

## 1. Intro & agenda - 0:30
**Say:**
> "Hi FSM team - thanks for the time. I'm going to show you the Salesforce we built to run your business end to end: from a referral coming in, through the sale, to a delivered project. I'll start with the big picture on one diagram, then take you into the system live and show you how your team actually uses it. I'll keep it simple on purpose and explain why at each step."

## 2. The system at a glance - 3:00  (START ON THE MIRO BOARD)
**SHOW:** the swimlane flow -> https://miro.com/app/board/uXjVHiXb-zU=/
**Say:**
> "Here's the whole process on one page, and notice the three lanes down the left - because everyone has a clear job.
> - **Marketing / Referral Partner** owns the top lane. A referral comes in through a web form.
> - **Sales** owns the middle lane: they qualify the lead, convert it, work the opportunity through a guided path, and close it.
> - **Delivery** owns the bottom lane: the moment a deal is won, a project is created automatically and delivery begins.
>
> Follow the blue line: a referral becomes a qualified lead, converts into an account and an opportunity, moves through the sales stages, and hits the 'Closed Won?' decision. **Won** drops into Delivery - the system spins up the project and flips the account to a client. **Lost** doesn't dead-end; it goes to Nurture so we can re-engage later. And leads that aren't a fit also go to Nurture rather than getting lost.
>
> One thing to call out - the green bar at the bottom - **the referring partner is captured up front and carried through every step**, so every won deal credits the partner who sent it. Referrals are your best channel, so we made sure they're never dropped.
>
> Now let me show you this working live."

*(Transition to Salesforce.)*

## 3. Capture: a partner submits a referral - 2:30
**SHOW:** the hosted **partner referral form** (https://carl.jibrilsulaiman.com/fsmdemo-page?ref=demo), then the created Lead in Salesforce.

> ⚠️ **Use the `?ref=demo` URL, not the bare `/fsmdemo-page`.** HighLevel caches the bare URL for hours, and the cached copy still has the OLD return URL (it redirects to FSM's assessment page). The `?ref=demo` query string forces a fresh copy that redirects to your branded `/fsmdemo-thankyou`. Once the CDN cache clears (~10h), the bare URL works too.

**Sample data to type on camera** (have this ready so you don't stall):

| Field | Value |
|-------|-------|
| Contact First Name | `Jordan` |
| Contact Last Name | `Bailey` |
| Company | `Riverside Dental Group` |
| Contact Email | `jordan.bailey@example.com` |
| Contact Phone | `(555) 018-2245` |
| Your name / company (referring partner) | `Summit Growth Partners` |

*(Lead Source is hidden and auto-set to Referral Partner - nothing to pick.)*

> ⚠️ **The Jordan Bailey / Riverside record already exists** (from testing). To submit LIVE without a duplicate warning, either delete that test lead first, or use one of the fresh alternates below. Each referring partner already exists as an account, so the qualify-step link works.

**Alternate sample referrals (pick a fresh one for the live submission):**

| Contact | Company | Email | Phone | Referring partner |
|---------|---------|-------|-------|-------------------|
| Priya Nair | Lakeshore Physical Therapy | `priya.nair@example.com` | `(555) 023-8890` | Peak Advisory Group |
| Marcus Reed | Cornerstone Realty Group | `marcus.reed@example.com` | `(555) 041-7712` | ABC Referrals |
| Elena Torres | Brightwave Fitness Studios | `elena.torres@example.com` | `(555) 067-3345` | Coastline Consulting |
| Devin Clarke | Harborview Accounting | `devin.clarke@example.com` | `(555) 088-1290` | Summit Growth Partners |

**In Section 4**, open whichever lead you just created (from the Leads list), set **Referred By (Partner)** to the matching partner account, then Convert.

**Teach + say:**
> "Referrals are your best channel, so we gave your partners a simple, branded form to send business your way. A partner fills in two things: **the business they're referring** - contact, company, email - and **who they are**, so we can credit them. Every submission is tagged as a Referral Partner lead automatically. I'll submit one now."
> *(Submit -> lands on the branded thank-you page.)* "And over in Salesforce, here it is as a new Lead - Riverside Dental Group, with **Summit Growth Partners** captured as the referrer and the source already set to Referral Partner. No one re-typed anything, and the referral is attributed from the first touch."

> **Demo-record choreography (important):**
> - **Section 4** — do the qualify + **Convert** live on this fresh **Riverside** lead (one button; shows Account + Contact + Opportunity created). Quick and low-risk.
> - **Sections 5-6** — then say *"and here's a deal that's already further along"* and switch to the pre-staged **Acme Inc** opportunity (has the Path, referral partner, competitor, amount, sitting at Prospecting) to show the Path and the Closed-Won automation.
> This shows Convert on a real lead without building a whole opportunity live.

**Business why:** "Make it effortless for partners to refer, and never lose track of who sent what - that's how you grow the channel that matters most to you."

## 4. Qualify & convert - 3:00
**SHOW:** open the fresh **Jordan Bailey / Riverside Dental Group** Lead (https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Lead/00Qfj00000nKWNiEAO/view). Set the **Referred By (Partner)** lookup = Summit Growth Partners (Lead Source is already Referral Partner from the form). Then Convert.
**Teach + say:**
> "Qualifying is where a rep confirms this is real and links the referral to the actual partner account - that's the human step that turns raw form data into accurate attribution. The form captured the referrer as text; here I connect that to the real **Referred By (Partner)** record so the credit is exact. I'll also set the source to Referral Partner.
> Then I click **Convert** - one action that creates the **Account**, the **Contact**, and the **Opportunity**, all linked. This is standard Salesforce, and it's why we didn't build a custom lead object - conversion already does the heavy lifting."

> **Say this scaling note (shows range):** "Today the partner is a text field a rep links on qualification. Ideally, and at scale, we'd give each partner a **unique referral link** carrying their id, and a small automation would link the lead to that partner the moment it comes in - exact attribution, no typos, no manual step. I kept it simple for now on purpose."

**Business why:** "One click moves a lead into the pipeline with no re-typing and nothing lost - and the referral credit is tied to a real record, not a loose name."

## 5. Work the opportunity - the custom Path - 3:30
**SHOW:** the Opportunity (https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Opportunity/006fj00000MwtdlAAB/view). **Expand the Path** (click the chevron at the far-left of the stage bar) so Key Fields + Guidance for Success stay visible. Fill a key field, then advance a stage.

**Sample data to enter on camera** (most key fields are pre-filled - the empty one is Next Step):

| Field | Value |
|-------|-------|
| Referral Partner | *already set* - Summit Growth Partners |
| Close Date | *already set* - 9/30/2026 |
| Amount | *already set* - $20,000 |
| Primary Competitor | *already set* - RivalCRM Co |
| **Next Step** (Prospecting) | `Book discovery call with the ops lead` |
| **Next Step** (Needs Analysis) | `Confirm scope and decision makers` |
| **Next Step** (Proposal) | `Send proposal; confirm budget` |
| **Next Step** (Negotiation) | `Finalize terms; target signature next week` |

**Teach + say:**
> "This is the sales journey, and it's built to be easy for your team. We created a lean, custom sales process - six stages, standard names your reps already know. On top of it is a **Path**: for each stage it shows the **key fields** to fill in and short **guidance for success** - what 'good' looks like at that step.
> On Prospecting, it asks for the referral partner and a realistic close date. On Proposal, it asks for the amount and next step. Reps aren't guessing; the system coaches them. To move a deal forward, they click the stage and mark it current."
> *(Advance one stage.)* "We also added one guardrail - you can't move to Proposal without an amount - so pipeline numbers stay honest. That's the only rule we added; we didn't over-engineer it."

**Business why:** "Adoption dies when reps face forty fields. A short guided path is what actually gets used, and it captures the key info at each stage."

## 6. Close Won: project + client, automatically - 3:00  (THE WOW)
**SHOW:** on the Opportunity, mark it **Closed Won**. Then open the **Projects** related list (new project) and the **Account** (now record type = Client). Projects list: https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/o/Projects__c/list
**Teach + say:**
> "Here's the moment that matters. When a rep marks a deal **Closed Won**..."
> *(Mark it, save. Pause.)*
> "...two things happen automatically, with no handoff and no double entry. **One** - a **Project** is created for the delivery team, already filled in with the client, the value, and a link back to the deal. **Two** - the **account is promoted from Prospect to Client**. Both driven by a single behind-the-scenes automation that fires only on the win.
> So the delivery team starts with full context the instant a deal closes, and your account list always reflects who's actually a client."

**Business why:** "The handoff that usually gets dropped between sales and delivery is now instant and automatic."

## 7. The four entities you track - 2:30
**SHOW:** the Accounts list (https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/o/Account/list) filtered by record type - Prospect, Client, Referral Partner, Competitor. Show the Primary Competitor lookup on the opportunity.
**Teach + say:**
> "You asked to track prospects, clients, partners, and competitors. Rather than build four separate objects, we used **one Account object with four record types** - each with its own tailored view. A prospect becoming a client is a one-field change, which, as you just saw, we automate on the win.
> Competitors we track two ways: as a **Competitor account** for the company profile, and as a **Primary Competitor** on each deal for win/loss. Quick note - Salesforce's built-in competitors list is a Classic-only feature, so in your Lightning org the right move was a lookup that actually links to the competitor record."

**Business why:** "One flexible object instead of four means less to maintain and one clean place to report from."

## 8. Referrals, measured - 1:30
**SHOW:** the *Pipeline & Revenue by Referral Partner* report (https://orgfarm-1c09ed113a-dev-ed.develop.lightning.force.com/lightning/r/Report/00Ofj00000Jjs1JEAR/view) - four partners, $282K won total (Peak Advisory $100K, ABC Referrals $88K, Summit Growth $70K, Coastline $24K).
**Teach + say:**
> "Because we captured the partner all the way through, we can answer the question that matters: which partners actually drive revenue. Here's pipeline and won revenue grouped by referral partner. That's how you decide where to invest in the relationships that feed your best deals."

## 9. Why it stays simple - design choices - 2:30
**SHOW:** back to the Miro board (or stay on screen).
**Say:**
> "A few deliberate choices. We used **standard objects wherever possible** - Leads, Accounts, Contacts, Opportunities - and built exactly **one custom object**, the Project, and **one automation**. That keeps it cheap to own and fast for your team to adopt.
> We also chose **not** to build several things: no custom lead object, no separate referral pipeline, no heavy project module. The brief said don't over-build, and honestly that's the right call for a system people have to live in every day. Every field on the page earns its place."

## 10. What we'd do next - roadmap - 1:30
**Say:**
> "If we kept going, in priority order: **unique referral links per partner** so a lead auto-links to the referring partner on arrival (no manual step); lead scoring so reps work the best referrals first; a couple of dashboards for pipeline and delivery health; project templates or milestones as your delivery process matures; and eventually a partner portal so referrers can submit and track their referrals. All scoped as its own step - not now, because today the goal was a simple system that works end to end."

## 11. Close & questions - 0:30
**Say:**
> "That's the system: a referral becomes a client and a delivered project, with the sales team guided at every step and the referral credited the whole way through. Thanks for watching - happy to answer any questions or walk through any piece in more detail."

---

## Talking-points bank (drop in wherever natural)
- "Referrals are your best channel, so we made capturing the partner a habit, not an afterthought."
- "Standard objects on purpose: less to maintain, faster to adopt, cheaper to own."
- "The path coaches reps - they're never guessing what 'good' looks like at a stage."
- "Won to project to client is automatic - the dropped handoff is now instant."
- "Prospects and clients are one company at two moments; partners and competitors are different relationships."
- "We deliberately did not build X, to keep it simple."

## Do / Don't on camera
- **DO** narrate the business *why* before or after each click, not the feature name.
- **DO** pause on the Closed Won moment - let the automation land.
- **DO** say one "we chose not to build X" line.
- **DON'T** read fields aloud like a list. Tell the story of a deal moving.
- **DON'T** apologize for simplicity - frame it as judgment.
- **DON'T** run long. If tight, trim the roadmap, not the live demo.

## Pre-record checklist
- [ ] Flow tested: one project created (no duplicate), account flips Prospect -> Client
- [ ] Path shows Key Fields + Guidance on the opportunity
- [ ] Hosted referral form (https://carl.jibrilsulaiman.com/fsmdemo-page?ref=demo) submits, lands as a Lead, AND redirects to /fsmdemo-thankyou (use the ?ref=demo URL to dodge the CDN cache)
- [ ] Referral thread intact: partner on Lead -> Opportunity -> report
- [ ] Sample records staged; one opportunity left just short of Closed Won for the live win
- [ ] Leftover sample-org fields removed from the Opportunity layout
- [ ] Recording <= 30 min, audio clear, browser readable
- [ ] Replied to michael.smith@fastslowmotion.com with the video link before ~2026-09-29
