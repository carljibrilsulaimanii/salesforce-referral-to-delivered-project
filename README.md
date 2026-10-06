# Salesforce: Referral Lead to Delivered Project

A Salesforce Developer Org build that runs a consulting firm's **referral-to-delivery** process end to end:
a partner referral comes in through a branded intake form, becomes a qualified lead, converts into an
Account, Contact, and Opportunity, moves through a guided sales **Path**, and the moment a deal is
**Closed Won** a **Project** is created automatically and the account is promoted to a **Client**.

Built as a Solution Architect interview assignment for Fast Slow Motion, with a focus on business
acumen, clear communication, and keeping the build simple (standard objects first; one custom object,
one automation).

## 🎥 Video walkthrough
https://youtu.be/ps1hYrhsKBM

## What's in this repo
| File | What it is |
|------|------------|
| [FSM_Salesforce_Build_Plan.md](FSM_Salesforce_Build_Plan.md) | Click-by-click build SOP — objects, record types, the custom Sales Process + Path, the auto-create-Project Flow, and the account Prospect→Client automation. An implementation runbook you could hand to a team. |
| [FSM_Presentation_Script.md](FSM_Presentation_Script.md) | Timed (~24 min) walkthrough script for presenting the build as if training a client. |
| [web-to-lead.html](web-to-lead.html) | Branded partner **referral intake form** (Salesforce Web-to-Lead). Captures the referred business and the referring partner; tagged as a referral automatically. |
| [fsmdemo-thankyou.html](fsmdemo-thankyou.html) | Branded confirmation page the form redirects to after submit. |
| [fsmdemo-welcome.html](fsmdemo-welcome.html) | Branded intro / agenda page shown at the start of the walkthrough. |
| [fsm referal form.html](fsm%20referal%20form.html) | The original Salesforce-generated Web-to-Lead markup (superseded by `web-to-lead.html`). |

## The data model (kept deliberately simple)

```
Lead (partner referral, captured via Web-to-Lead)
   │  qualify (link the referring partner) → convert
   ▼
Account ── Record Types: Prospect | Client | Referral Partner | Competitor
   │
   ├── Contact
   │
   └── Opportunity (custom "FSM Sales Process" + Path; Referral Partner lookup)
            │  Stage = Closed Won
            ▼  (one Record-Triggered Flow)
        Project (auto-created) + Account promoted Prospect → Client
```

- **Marketing leads & referrals** — standard Lead object + a branded Web-to-Lead intake form; the referring partner is captured on the lead and carried to the opportunity.
- **Sales opportunities** — a lean custom Sales Process with a **Path** (key fields + guidance for success per stage) so the team is coached, not buried in fields.
- **Projects** — one custom object, auto-created by a single Record-Triggered Flow when an opportunity is won, with the client, deal value, and a link back to the source opportunity.
- **Entities** — prospects, clients, referral partners, and competitors are tracked with **record types on one Account object**, rather than four separate objects.

## Why it's built this way
Standard objects wherever possible, exactly one custom object, and one automation — so the system is
easy for a team to adopt and cheap to maintain. The build intentionally avoids over-engineering
(no custom lead object, no separate referral pipeline, no heavy project module).

---
Built by [Jibril Sulaiman](https://github.com/carljibrilsulaimanii).
