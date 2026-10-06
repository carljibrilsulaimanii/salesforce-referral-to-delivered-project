# QA & verification (Salesforce via Claude MCP)

After the org was built in the UI, the automation and data model were **verified with Claude
connected to Salesforce over MCP** — querying the live org, testing the Won automation end to end
on a throwaway record, and seeding demo data. This caught issues a local file or a code read would
have missed.

## The MCP toolchain
| Tool | Used for |
|------|----------|
| `getUserInfo` | Confirm which user the connector runs as (it was the System Administrator) |
| `soqlQuery` | Read records, field metadata (`FieldDefinition`), and aggregate revenue by referral partner |
| `getObjectSchema` | Inspect object/field structure |
| `createSobjectRecord` | Seed sample accounts, opportunities, and projects |
| `updateSobjectRecord` | Flip stages/record types for tests and demo prep |
| `deleteSobjectRecord` | Clean up test artifacts |

## What verification found
- **The Won Flow works end to end.** Marking a spare opportunity `Closed Won` through the API fired
  the Record-Triggered Flow: a **Project** was created (client, amount, `Status = Not Started`) *and*
  the **Account was promoted Prospect → Client**. Both effects confirmed, then the test opportunity,
  its project, and the account change were reverted.
- **The custom object's real API name is `Projects__c` (plural), not `Project__c`.** A direct query
  on `Project__c` failed; the child-relationship query `(SELECT Id FROM Projects__r …)` worked, which
  is how the true name surfaced. The Flow and fields still worked because they reference the object
  directly — but any SOQL/code must use `Projects__c`.
- **A stale "Client" account** existed on a deal that was never actually won (set manually), which
  would have broken the live "watch it flip" moment. It was reset to **Prospect** so the Closed-Won
  demo is honest.
- **Record types were missing** on most sample accounts, so the four-entity story looked half-blank.
  All accounts were assigned a record type (Prospect / Client / Referral Partner / Competitor).

## Reusable micro-skill: QA a Salesforce automation via MCP
A compact, repeatable checklist for verifying any trigger/flow build this way:

1. **Confirm the running user** (`getUserInfo`). A "sObject not supported" error is often a
   permissions or metadata-cache issue, not proof the object is missing.
2. **Verify the config from metadata, not memory.** Pull field API names and picklist values from
   `FieldDefinition` (`SELECT QualifiedApiName, Label, DataType FROM FieldDefinition WHERE
   EntityDefinition.QualifiedApiName = '<Object>'`). Field/object labels and API names diverge
   (e.g. an object named with a plural label → plural API name).
3. **Prove the automation on a throwaway record, not the demo record.** Capture the record's
   before-state, trigger the condition (`updateSobjectRecord` stage → `Closed Won`), then query the
   result — including child records via the relationship name (`…(SELECT Id FROM Child__r)`).
4. **Check every side effect**, not just the obvious one. A "create a project on win" flow may also
   update the parent account; verify both.
5. **Clean up.** Revert the stage, delete the created child, restore any changed field. Note that a
   null value may not clear a lookup/record-type field — set it back explicitly.
6. **Don't trust local copies or session snapshots** — read the live org. Query counts can run high
   versus the search API; prefer precise SOQL with explicit bounds.
7. **Seed demo data so reports have something to show.** Group by the dimension that matters
   (`SELECT Referral_Partner__r.Name, SUM(Amount) … GROUP BY …`) and confirm the totals before
   building the report in the UI.

## Verified numbers (referral revenue, from seeded data)
`SELECT Referral_Partner__r.Name, COUNT(Id), SUM(Amount) FROM Opportunity WHERE StageName = 'Closed Won' AND Referral_Partner__c != null GROUP BY Referral_Partner__r.Name`

| Referral Partner | Deals | Won revenue |
|------------------|-------|-------------|
| Peak Advisory Group | 2 | $100,000 |
| ABC Referrals | 2 | $88,000 |
| Summit Growth Partners | 2 | $70,000 |
| Coastline Consulting | 1 | $24,000 |
| **Total** | **7** | **$282,000** |

---
Built by [Jibril Sulaiman](https://github.com/carljibrilsulaimanii).
