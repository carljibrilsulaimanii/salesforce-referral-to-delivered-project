<!--
  Author:        C. Jibril Sulaiman II
  Date:          2026-09-21
  For:           Fast Slow Motion — Solution Architect interview assignment
  Deliver to:    Salesforce Developer Org (build) + Loom/video reply to michael.smith@fastslowmotion.com
  Due:           ~2026-09-29 (14 days from 2026-09-15 email); recording <= 30 min
  What:          Click-by-click build plan + presentation outline for the FSM Salesforce assignment
  Why:           Model FSM's business (leads -> opportunities -> auto-created projects) across
                 prospects/clients/partners/competitors, using standard objects + minimal
                 customization. Graded on business acumen, communication, and Salesforce knowledge.
                 Guiding rule from the brief: "don't boil the ocean" / "focus on simplicity."
-->

# Fast Slow Motion — Salesforce Interview Assignment Build Plan

## Task summary

Fast Slow Motion (a Salesforce/HubSpot consulting firm) sent an interview assignment for the
Solution Architect role: build a Salesforce **Developer Org** that models FSM's own business, then
present it on video **as if FSM were your client** — training them on how to use the system.

You are being graded on three things: **business acumen, communication skills, and Salesforce
knowledge.** The requirements video repeats "don't boil the ocean" and "focus on simplicity," so the
winning move is a clean, mostly-standard build — not an over-engineered one.

**What the org must do:**
1. Track **marketing leads**, with emphasis on **referrals** (capture which partner referred).
2. Track **sales opportunities** using a **custom, easy-to-use sales process**.
3. **Automatically create a Project** when an Opportunity is **Closed Won**, with simple project tracking.
4. Track the entities FSM interacts with: **prospects, clients, referral partners, and competitors.**

**Deliverable & deadline:** record a walkthrough presentation **≤ 30 minutes** and reply with the
video link to michael.smith@fastslowmotion.com. Assignment received 2026-09-15, 14-day window →
**due ~2026-09-29**.

**Solution in one line:** Lead → convert → Account (four Record Types) → Opportunity (custom Sales
Process) → one Record-Triggered Flow creates a single custom Project object on Won. One custom
object, one flow, everything else standard configuration.

---

## 0. The brief, decoded

Source: assignment email (Michael Smith, 2026-09-15) + requirements video (Matt).

**Graded on:** business acumen · communication skills · Salesforce knowledge.
**Delivery:** build in a Developer Org, present as if FSM is your client, *train them* on the system, record ≤ 30 min, reply to the email with the video link.

**Requirements:**

| # | Requirement | Design response | Simplicity trap to avoid |
|---|-------------|-----------------|--------------------------|
| 1 | Track **marketing leads**; referrals "really important" | Standard **Lead** + referral capture | Don't add dozens of fields |
| 2 | Track **sales opportunities** with a **custom sales process**, easy for reps | Standard **Opportunity** + custom Sales Process | Keep stages lean |
| 3 | **Project auto-created when Opportunity is Won**; track key project data, keep simple | One custom **Project__c** + one Record-Triggered Flow | One object, one flow |
| 4 | Track **prospects, clients, partners, competitors** | **Account Record Types** (not 4 custom objects) | Don't over-model |

**North star:** standard objects wherever possible, minimal custom build, every customization tied to a stated business reason. Say out loud "we chose *not* to build X to keep it simple" at least once.

---

## 1. Data model overview

```
Lead (marketing leads, referral capture)
   │  convert
   ▼
Account ── Record Types: Prospect | Client | Referral Partner | Competitor
   │
   ├── Contact (people at the account)
   │
   └── Opportunity (custom Sales Process; referral + competitor lookups)
            │  Stage = Closed Won
            ▼  (Record-Triggered Flow)
        Project__c (Client, Source Opportunity, Status, dates, amount)
```

One custom object (`Project__c`). One flow. Everything else is standard configuration.

> ⚠️ **As-built naming note:** in the live org the object's Object Name was left plural, so its real **API name is `Projects__c`** (child relationship `Projects__r`), not `Project__c`. Use `Projects__c` in any SOQL/code. The flow, fields (`Status__c`, `Project_Amount__c`, `Client__c`, `Source_Opportunity__c`), related lists and the demo all work regardless. Verified 2026-09-26: marking an Opportunity Closed Won auto-creates the Project and flips the Account to Client.

---

## 2. Build order (do in this sequence)

> Tip: build the object your automation depends on FIRST (the Project object) so the Flow has fields to map to.

### Naming rule — read this once, applies to everything below
Salesforce has **two names** for every object and field:
- **Label / Plural Label** = what *humans* see (tabs, reports, page layouts) → **use spaces**, e.g. `Won FSM Project`
- **Object Name / Field Name** = the *API* name → **use single underscores, no spaces**, e.g. `Won_FSM_Project`
- **Never type `__` (double underscore)** — that's reserved. Salesforce appends `__c` itself, giving `Won_FSM_Project__c`.
- Convention: labels can be plural, the Object/API name is usually singular.

---

### Step 1 — Create the Developer Org
1. Go to https://developer.salesforce.com/signup and fill the form (free org, never expires).
2. Check email → click the verification link → set a password.
3. Log in. Confirm the top-right shows **Lightning Experience** (not Salesforce Classic). If in Classic, click your avatar → **Switch to Lightning Experience**.
4. Click the **gear icon** (top right) → **Setup**. Everything below happens in Setup or Object Manager.

---

### Step 2 — Build the custom Project object
**Path:** Setup → **Object Manager** (top tab) → **Create** (top-right) → **Custom Object**.

**2.1 — Object Information section**
- **Label:** `Project`  *(spaces — human-readable)*
- **Plural Label:** `Projects`
- **Object Name:** `Project`  *(singular, no spaces; API name becomes `Project__c`. Salesforce standard objects are singular — match that convention.)*
- **Description:**
  > Tracks client delivery projects at Fast Slow Motion. A Project record is created automatically when a sales Opportunity is marked Closed Won, carrying over the client, deal value, and a link back to the originating Opportunity so the delivery team starts with full context. Used to manage project status and timelines from kickoff through completion.

**2.2 — Record Name section**
- **Record Name:** `Project Name`  *(space, no underscores — this is the label of the record's name field)*
- **Data Type:** `Text`  *(simplest. Auto Number `PRJ-{00000}` also fine if you want to show that off.)*

**2.3 — Optional Features (checkboxes)**
- ✅ **Allow Reports**  *(needed for the referral/pipeline reports later)*
- ✅ **Allow Activities**  *(log tasks/calls on a project)*
- ✅ **Track Field History**  *(optional — lets you show status changes over time)*
- *(Leave the Object Classification boxes — Allow Sharing / Bulk API / Streaming API — at their checked defaults.)*

**2.4 — Deployment Status**
- Select **Deployed** (not "In Development").

**2.5 — Search Status**
- ✅ **Allow Search**  *(so Project records appear in global search — a polished default).*

**2.6 — Object Creation Options (bottom)**
- ✅ **Launch New Custom Tab Wizard after saving this custom object**  *(so it gets a tab and shows in the app)*
- ☐ Add Notes and Attachments related list  *(optional; skip to keep it simple)*

**2.7 — Save.** If the Tab Wizard opens: pick any tab style icon → Next → Next → Save.

**2.8 — Add the fields.**
**Path:** Object Manager → **Project** → **Fields & Relationships** → **New**. Repeat for each row:

> ‼️ **How to type the Field Name:** enter ONLY what's in the "Field Name to type" column — letters and single underscores, no spaces. **Do NOT type `__c`** — Salesforce adds it automatically. Typing `__c` yourself causes a "cannot contain two consecutive underscores" error. The "Stored as" column is just what Salesforce ends up saving.

| Field Label | Data Type | Field Name to type | Stored as (auto) | Related To | Child Relationship Name | Required? |
|-------------|-----------|--------------------|------------------|-----------|-------------------------|-----------|
| `Client` | **Lookup** | `Client` | `Client__c` | **Account** | `Projects` | **No** (Flow populates it) |
| `Source Opportunity` | **Lookup** | `Source_Opportunity` | `Source_Opportunity__c` | **Opportunity** | `Projects` | **No** |
| `Status` | **Picklist** | `Status` | `Status__c` | — | — | **No** (has a default) |
| `Start Date` | **Date** | `Start_Date` | `Start_Date__c` | — | — | **No** |
| `Target End Date` | **Date** | `Target_End_Date` | `Target_End_Date__c` | — | — | **No** |
| `Project Amount` | **Currency** | `Project_Amount` | `Project_Amount__c` | — | — | **No** |
| `Project Owner` | **Lookup** | `Project_Owner` | `Project_Owner__c` | **User** | `Projects` | **No** |

> ⚠️ **Leave every field NOT required** (don't tick "Required" on the field, and for lookups leave "Clear the value..." selected — not "Always require a value"). Reason: the **Flow** creates each Project and fills these fields; if a field were Required and the Flow didn't set it, **the Flow would fail** and the project wouldn't be created. Optional fields keep both the automation and manual entry working. You enforce data quality with **defaults** (Status) and **validation rules**, not field-level Required, in this design.

Per-field notes:
- **Status** picklist values (one per line): `Not Started`, `In Progress`, `On Hold`, `Complete`. Set `Not Started` as **default**. (The default is why it doesn't need to be Required.)
- **Project Amount:** Length 16, Decimals 2.
- **Project Owner:** optional — the standard Owner field already exists.

> **Best practice — type the Field Label FIRST and let the Field Name auto-fill.** When you type the Label, Salesforce fills the Field Name for you with correct underscores and no `__c`. Only touch the Field Name if it auto-filled with the *related object's* name on a lookup (see next note). If you type into the Field Name box yourself, remember: no spaces, no `__c`, don't end with an underscore.
>
> **Watch the Field Name on lookups specifically:** on a Lookup, Salesforce sometimes auto-fills the Field Name with the *related object's* name (e.g. `Account`, `Opportunity`, `User`) instead of your label. If it does, overwrite it with the correct name from the table so it matches the Flow mappings in Step 6.
>
> **Child Relationship Name** appears only on **Lookup / Master-Detail** fields (Step 3 of 6). It's the label of the related list of Projects shown on the *parent* record (Account, Opportunity, User). Set all three to `Projects`. **Note:** this field is also an API-style name — **no spaces** (single words like `Projects` are fine; multi-word names need underscores, e.g. `Referred_Leads`).
>
> For each field: New → pick Data Type → Next → (Lookup: pick Related To → Next) → set **Field Label** and correct the **Field Name** → add **Description** + **Help Text** (see §2.9) → (Lookup: set **Child Relationship Name** = `Projects`) → Next → leave field-level security default → Next → add to layout → **Save & New** (or **Save** on the last field).

Stop there. Don't add task lists, time tracking, or milestones — that's the over-build trap the brief warned about.

**2.9 — Description & Help Text for each field** (paste as you create them):

| Field | Description | Help Text |
|-------|-------------|-----------|
| Client | Lookup to the Account this project is delivered for. Set automatically from the won Opportunity's account when the project is created. | The client this project is being delivered for. |
| Source Opportunity | Lookup to the Opportunity that generated this project. Set automatically when the Opportunity is marked Closed Won. | The won deal that created this project. |
| Status | Current delivery stage of the project. Defaults to Not Started when the project is auto-created. | Where this project stands: Not Started, In Progress, On Hold, or Complete. |
| Start Date | Date project delivery work begins. | When work on this project starts. |
| Target End Date | Planned completion date for the project. | The date this project is expected to be completed. |
| Project Amount | Contracted value of the engagement. Copied from the Opportunity Amount when the project is created. | The dollar value of this project, carried over from the won deal. |
| Project Owner | The team member responsible for delivering this project. | Who owns delivery of this project. |

---

### Step 3 — Account Record Types (the four entities: prospect / client / partner / competitor)
**Path:** Object Manager → **Account** → **Record Types** → **New**. Create four (repeat the New flow each time):

| Record Type Label | Record Type Name | Notes |
|-------------------|------------------|-------|
| `Prospect` | `Prospect` | A company you're pursuing but haven't won. |
| `Client` | `Client` | A won customer. |
| `Referral Partner` | `Referral_Partner` | Sends you referrals. |
| `Competitor` | `Competitor` | Who you compete against. |

For each: **New** → Existing Record Type = `Master` → enter Label (Name auto-fills) → add the Description → **Active** ✅ → **click the "Make Available" checkbox in the column HEADER to tick all profiles** → *(optional: tick **Make Default** on the Prospect row only, so new Accounts default to Prospect)* → **Next** → assign **Account Layout** to all profiles → **Save & New** (just **Save** on the last one, Competitor).

> ⚠️ **Make Available gotcha:** only the **first** record type you create (Prospect) auto-checks Make Available for all profiles. **Every subsequent record type (Client, Referral Partner, Competitor) has it UNCHECKED by default** — you must click the Make Available header checkbox each time, or the record type is saved unusable (you won't be able to select it on an Account). If you missed it on one, fix it via Record Types → [name] → Edit → tick Make Available → Save.

**3.1 — (Optional) tailor a layout per type.** Object Manager → Account → **Page Layouts**. Clone/rename layouts so each type shows only relevant fields:
- Prospect / Client: Industry, Annual Revenue, Website, Owner
- Referral Partner: add a custom checkbox `Active Partner`
- Competitor: keep minimal — Name, Website, a notes field

> **Camera line:** "A prospect and a client are the same company at different stages — promoting one to Client is a single record-type change. Partners and competitors are different *relationships*, so each gets its own tailored layout."
>
> **Business-acumen tradeoff to say out loud:** the standard **Account `Type`** picklist already ships with Prospect / Customer / Partner / Competitor — that's the even-lighter option. You chose Record Types for tailored layouts + cleaner reporting. Naming the tradeoff is the point.

---

### Step 4 — Customize the standard Lead (marketing leads + referrals)
> **Do NOT build a custom leads object.** Use the standard **Lead** — it has built-in conversion to Account/Contact/Opportunity. A custom rebuild loses all of that and reads as not knowing the platform.

**Path:** Object Manager → **Lead** → **Fields & Relationships**.

**4.1 — Lead Source picklist:** click **Lead Source** → under Values, **New** → add `Referral Partner` if it's not already there → Save.

**4.2 — New field "Referred By (Partner)":** **New** → **Lookup** → Related to **Account** → Label `Referred By (Partner)`, Field Name to type `Referred_By_Partner`, Child Relationship Name `Referred_Leads` *(underscore — no spaces allowed here either)*, **Required: No** (only referral leads have it) → *(optional)* add a lookup filter `Account.RecordType = Referral Partner` → Next → Save (add to layout). Captures *which* partner sent the lead.
- **Description:** `Lookup to the Referral Partner account that sent this lead. Used to measure and reward referral relationships.`
- **Help Text:** `Which partner referred this lead, if it came from a referral.`

**4.3 — Confirm the layout** (Lead → Page Layouts) shows: Company, Name, Email, Phone, **Lead Source**, **Referred By (Partner)**, Status, Owner. Don't over-add.

> **Camera line:** "Every lead records where it came from — and if it's a referral, exactly which partner sent it — so we can measure and reward our best referral relationships."

**4.5 — Web-to-Lead: internal PARTNER REFERRAL form (how referral leads enter the system).** This is a partner-facing form: a marketing/referral **partner** submits a business they're referring. The standard fields capture the **referred business** (the Lead); the "Referred By (Name)" field captures **the partner**; `lead_source` is hardwired to **Referral Partner**. Salesforce generates the base form natively; a branded version lives at `web-to-lead.html`.

*(Recommended — a real artifact to show on camera, and it's the front door of the referral thread the brief stresses.)*

1. **Enable + generate:** Setup → quick-find **Web-to-Lead** → **Create Web-to-Lead Form**.
2. **Choose the fields** to expose on the form: `Name`, `Company`, `Email`, `Phone`, `Lead Source`, and `Referred By (Name)` *(see caveat below)*.
3. Set a **return URL** (any page, e.g. a thank-you page) → **Generate**.
4. Salesforce hands you an **HTML snippet**. Save it as `web-to-lead.html` and open it in a browser to preview / submit a test lead.

> ⚠️ **Lookup caveat (mention on camera):** the **Referred By (Partner)** field is a *lookup to Account* — lookups don't work on a public web form (a visitor can't know an internal record ID). So for the form, capture the referrer as **plain text**:
> - Add a Lead text field **`Referred By (Name)`** (Field Name `Referred_By_Name`, not required) and put *that* on the form.
> - When a rep qualifies the lead, they set the **Referred By (Partner)** lookup to the matching partner Account.
> - **Camera line:** "The public form captures the referrer's name as text; when we qualify the lead, we link it to the actual partner record — clean intake, accurate attribution."

---

### Step 5 — Opportunity: custom Sales Process + fields
**5.1 — Create the Sales Process.**
Path: Setup → quick-find **Sales Processes** → **New**.
- **Existing Sales Process:** Master  |  **Sales Process Name:** `FSM Sales Process` → Save.
- On the next screen, keep this lean set of **standard** stages in "Selected" and remove the rest (using the standard names avoids creating new picklist values — less to maintain):
  `Prospecting` → `Needs Analysis` → `Proposal/Price Quote` → `Negotiation/Review` → `Closed Won` → `Closed Lost` → Save.
  - *(Salesforce's default Selected list is already close to this — just remove Qualification, Value Proposition, Id. Decision Makers, and Perception Analysis to keep it to six.)*

**5.2 — Create the Opportunity Record Type.**
Path: Object Manager → **Opportunity** → **Record Types** → **New**.
- Label `Sales Opportunity` → **Sales Process:** `FSM Sales Process` → Active ✅ → assign to System Administrator → choose layout → Save.

**5.3 — Add the Opportunity referral field.** Object Manager → Opportunity → Fields & Relationships → New:

| Field Label | Data Type | Field Name to type | Related To | Child Relationship Name | Required? | Description | Help Text |
|-------------|-----------|--------------------|-----------|-------------------------|-----------|-------------|-----------|
| `Referral Partner` | Lookup | `Referral_Partner` | **Account** | `Referred_Opportunities` | No | Lookup to the Referral Partner account that sourced this deal. Feeds referral pipeline/revenue reporting. | The partner who referred this deal, if applicable. |

- Confirm standard **Amount** and **Close Date** are on the page layout.

**Competitors — add a Primary Competitor lookup (Lightning-native).**

> ⚠️ **Why not the standard Competitors related list:** Salesforce's built-in Opportunity **Competitors** related list is a **Classic-only** feature — it sits on the page layout but does NOT render in Lightning Experience. So in a Lightning org, use a custom lookup instead. (This reverses an earlier "use the standard list" call, for that reason.)

Object Manager → Opportunity → Fields & Relationships → New:

| Field Label | Data Type | Field Name to type | Related To | Child Relationship Name | Required? | Description | Help Text |
|-------------|-----------|--------------------|-----------|-------------------------|-----------|-------------|-----------|
| `Primary Competitor` | Lookup | `Primary_Competitor` | **Account** | `Competing_Opportunities` | No | Lookup to the competitor (Competitor account) you're up against on this deal. Used for win/loss context in Lightning. | The main competitor on this opportunity. |

- *(Optional, slick)* **Lookup Filter** → Show Filter Settings → `Account: Record Type` **equals** `Competitor` — so the lookup only offers Competitor accounts.
- Add to **Opportunity Layout** → Save. It appears on the **Details** tab in Lightning.

> **Competitors are satisfied two complementary ways:** as **Accounts** (the Competitor record type = the company/entity) and as the per-deal **Primary Competitor** lookup (win/loss context on each Opportunity, linked to the real Competitor account).
>
> **Camera line:** "The requirement was to track competitors — I do that as a Competitor account type for the company profile, and a Primary Competitor lookup on each deal for win/loss context. Note: Salesforce's built-in Competitors list is Classic-only, so in a Lightning org the right move is a lookup that actually links to the competitor record."

**5.4 — (Optional) one Validation Rule** (shows knowledge; don't over-build).
Path: Object Manager → Opportunity → **Validation Rules** → New.
- **Rule Name:** `Require_Amount_At_Proposal`
- **Description:** `Requires an Amount before an opportunity can advance to the Proposal stage, so quoted deals always have a value for pipeline reporting.`
- **Error Condition Formula:** `AND(ISPICKVAL(StageName, "Proposal/Price Quote"), ISBLANK(Amount))` *(use the exact standard stage name you selected)*
- **Error Message:** `Enter an Amount before moving this deal to Proposal/Price Quote.`
- **Error Location:** Field → `Amount` → Save.

**5.5 — Lightning Path (key fields + guidance per stage).** This is what makes the sales process "track key info throughout the journey" and "easy for the sales team" — it maps word-for-word to the brief.

1. Setup → Quick Find **Path Settings** → **Enable** (if disabled).
2. **New Path** → Path Name `FSM Sales Path`, Object **Opportunity**, Record Type **Sales Opportunity**, Picklist **Stage** → Next.
3. For each stage add **Key Fields** (up to 5) and **Guidance for Success**:

| Stage | Key Fields | Guidance for Success |
|---|---|---|
| Prospecting | Referral Partner, Close Date, Next Step | Confirm this is a real opportunity. Record who referred it and set a realistic Close Date. |
| Needs Analysis | Amount, Primary Competitor, Next Step | Document the client's needs and scope. Identify the main competitor and decision makers. |
| Proposal/Price Quote | Amount, Close Date, Next Step | Send the proposal with a clear Amount. Confirm budget, timeline, and who signs off. |
| Negotiation/Review | Amount, Close Date, Primary Competitor, Next Step | Align on terms and pricing. Address competitor objections and lock the close plan. |
| Closed Won | Amount, Description | Won. The project auto-creates and the account becomes a Client. Confirm the delivery contact. |
| Closed Lost | Description | Record why we lost. Move the account to Nurture for future re-engagement. |

4. *(Optional)* enable **Celebration** on Closed Won. **Finish**, then confirm the Path shows Key Fields + Guidance on an Opportunity.

---

### Step 6 — Automation: auto-create the Project when an Opportunity is Won
**Path:** Setup → quick-find **Flows** → **New Flow** → **Record-Triggered Flow** → Create.

**6.1 — Configure Start:**
- **Object:** `Opportunity`
- **Trigger the Flow When:** *A record is updated*
- **Set Entry Conditions:**
  - Condition Requirements: **All Conditions Are Met (AND)**
  - Field: `StageName`  |  Operator: **Equals**  |  Value: `Closed Won`
- **When to Run the Flow for Updated Records:** select **Only when a record is updated to meet the condition requirements** ← this is the anti-duplicate setting; without it a project is created on every later save.
- **Optimize the Flow for:** **Actions and Related Records** (runs after-save) → Done.

**6.2 — Add the Create Records element:**
- On the canvas, click **+** → **Create Records**.
- **Label:** `Create Project`
- **How Many Records to Create:** One
- **How to Set the Record Fields:** *Use separate resources, and literal values*
- **Object:** `Project` (`Project__c`)
- **Set Field Values:**

| Project field | Value / Resource |
|---------------|------------------|
| `Name` (Project Name) | `{!$Record.Name}`  *(the Opportunity's name)* |
| `Client__c` | `{!$Record.AccountId}` |
| `Source_Opportunity__c` | `{!$Record.Id}` |
| `Project_Amount__c` | `{!$Record.Amount}` |
| `Status__c` | `Not Started` |
| `Start_Date__c` | `{!$Flow.CurrentDate}` *(or `{!$Record.CloseDate}`)* |

- Click **Done**.

**6.3 — Save & Activate:**
- **Save** → Flow Label `Create Project on Opportunity Won` → Save.
- Click **Activate** (top right). An inactive flow does nothing.

**6.4 — Extend the same Flow to flip the Account Prospect → Client on Won.** Ties the "prospects vs clients" entities to the won event (open the flow → Save As New Version → Activate when done).

1. **Get Records** element — `Get Client Record Type`:
   - Object: **Record Type**; conditions `SObject Type` Equals `Account` AND `Developer Name` Equals `Client`
   - Store only the first record, all fields.
2. **Update Records** element — `Set Account to Client`:
   - Find: *specify conditions* → Object **Account** → `Id` Equals `{!$Record.AccountId}`
   - Set `RecordTypeId` = `{!Get_Client_Record_Type.Id}` *(hardcode alt: Client RT Id `012fj000008GFcnAAG`)*
   - *(Optional: also set `Type` = `Customer - Direct`.)*
3. **Save As New Version** → **Activate**.

> Flow now runs: Won → Create Project → Get Client RecordType → Set Account to Client.

> **Camera line:** "The moment a rep marks a deal Won, the delivery team's project is already waiting — same structure every time, no dropped handoff, no double entry."

---

### Step 7 — Load sample data (so the demo tells a story)
Set up your reference data first, then bring the lead in **through the form** so the intake is realistic.

**Reference data (create manually — this is internal setup data, not form-captured):**
1. **Referral Partner** account — e.g. `Acme Advisors` (Account, record type Referral Partner).
2. **Competitor** account — e.g. `RivalCRM Co` (Account, record type Competitor).

**The lead — enter it via the Web-to-Lead form (§4.5), not by hand:**
3. Open your `web-to-lead.html` in a browser and **submit a lead** — Name, Company, Email, Phone, **Lead Source = `Referral Partner`**, **Referred By (Name) = `Acme Advisors`**. It lands in Salesforce as a new Lead. *(This is the "how leads come in" beat on camera.)*
4. Open the new Lead, and as the qualifying rep set the **Referred By (Partner)** lookup = `Acme Advisors` (linking the text name to the real partner record).
5. **Convert** the Lead (Lead detail → **Convert** button) → creates a **Prospect** Account + Contact + an **Opportunity** on the FSM Sales Process.
6. On the Opportunity: set the **Referral Partner** lookup = `Acme Advisors`, add an **Amount** and **Close Date**, and add `RivalCRM Co` in the standard **Competitors** related list.
7. Leave the Opportunity at an early stage — you'll push it to **Closed Won live on camera** so the **Project auto-creates** in front of them.

> The referral thread must stay unbroken: `Referred By (Name)` on the form → `Referred By (Partner)` on the Lead → `Referral Partner` on the Opportunity → Project (via Source Opportunity) → referral report.

**Test the flow now (before recording):** flip one opportunity to Closed Won → confirm exactly **one** Project record appears under it, populated correctly. Flip another save to confirm no duplicate is created.

---

### Step 8 — One report (optional, high-value)
**Path:** App Launcher → **Reports** → **New Report** → report type **Opportunities** → Continue.
- **Group Rows** by `Referral Partner`.
- Add a **Sum** on `Amount`.
- **Save** as `Pipeline & Revenue by Referral Partner`.
- Ties straight to the "referrals are really important" emphasis — a strong closing beat in the demo.

---

## 3. Presentation outline (≤ 30 min; aim 20–25)

Record in Loom/Zoom. Screen-share the org. Talk like you're onboarding a paying client, not demoing features.

1. **Agenda (30s)** — "I'll show how Salesforce now runs FSM end to end: from a marketing lead to a delivered project, and how we track everyone you work with."
2. **Play back their business (1 min)** — leads → opportunities → projects, inside an ecosystem of prospects, clients, partners, competitors. Proves you listened.
3. **Live journey demo (10–14 min)** — the spine:
   - Marketing lead arrives → **capture the referral** (who sent it)
   - **Convert** → Prospect account + contact + opportunity created
   - Work the opp through the **custom stages**
   - Mark **Closed Won → Project auto-appears** ← pause here, this is the wow
   - Show the **four account types**; how competitor + partner relate to the deal
   - Glance at the **referral report**
4. **Why it's simple (2 min, business acumen)** — "Standard objects + record types, not custom-everything. Cheap to maintain, fast for your team to adopt. We deliberately did *not* build [X] so the system stays simple." Name the record-type-vs-standard-Type tradeoff.
5. **Roadmap (1–2 min, matches their 'grow you up to stay' culture)** — lead scoring, project templates/milestones, partner portal, dashboards. Scoped, not now.
6. **Close / Q&A (30s).**

**Two things most candidates miss:**
- Narrate the *why* (business reason) behind every click, not the feature name.
- Say once, explicitly: "We chose not to build X, to keep this simple."

---

## 4. Pre-send checklist
- [ ] Flow **Activated** (test: flip an opp to Won, confirm a Project appears once, not twice)
- [ ] All four Account record types selectable + tailored layouts
- [ ] Lead "Referred By" + Lead Source = Referral Partner working
- [ ] Opportunity on the custom Sales Process with lean stages
- [ ] Sample records staged for a clean live demo
- [ ] Recording ≤ 30 min, audio clear, org readable (zoom browser to ~110–125%)
- [ ] Reply to michael.smith@fastslowmotion.com with the video link before ~2026-09-29

---

## 5. Talking points bank (drop into narration)
- "Referrals are your best channel, so we make capturing the referring partner a required habit, not an afterthought."
- "The sales team sees a short, clear stage path — adoption dies when reps face 12 stages and 40 fields."
- "Won deal to live project is automatic — the handoff that usually gets dropped is now instant."
- "Prospects and clients are one company at two moments; partners and competitors are different relationships, so they're modeled differently."
- "We used standard Salesforce objects on purpose — less to maintain, faster to adopt, cheaper to own."
