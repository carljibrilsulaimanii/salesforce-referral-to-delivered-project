# Referral form buildout & verification

The referral intake form ([`web-to-lead.html`](../web-to-lead.html)) is a **Salesforce Web-to-Lead**
form, rebuilt as a branded, partner-facing page and verified end to end. Claude was used to build
the HTML/CSS and to verify the submission landed as a Lead.

## What it is
An **internal partner referral form**: a marketing/referral partner uses it to refer a business to
the firm. The standard fields capture the **referred business** (which becomes the Lead); a separate
field captures **the referring partner**; `Lead Source` is hardwired to `Referral Partner` because
every submission from this form is a referral.

## Buildout
1. **Generate the base form in Salesforce.** Setup → Web-to-Lead → *Create Web-to-Lead Form*. This
   produces HTML whose `<form action>`, hidden `oid` (org id), `retURL`, and every input `name` are
   what make it post correctly. These were preserved **verbatim**, including the custom field's
   `00N…` id (the Web-to-Lead name for the "Referred By (Name)" lead field, retrieved from the
   field's `DurableId` via MCP: `Lead.00N…`).
2. **Rebrand it.** Rewrote the markup/CSS to match the firm's brand (navy + burnt-orange, logo),
   as a clean two-section card: *the business you're referring* and *about you (the referring
   partner)*. Verified rendering in a browser.
3. **Restructure for a referral, not a generic lead.** Removed the auto-generated City/State/Country
   dropdowns (noise), added a standard `phone` field, and changed `lead_source` from a hidden `Web`
   value to a hidden `Referral Partner` value.
4. **Mapping (critical):** the public form captures the referrer as **text** only. A lookup to the
   partner Account can't live on a public web form, so the text name is linked to the real
   **Referral Partner** account record during qualification (a one-field step). At scale this would
   move to a unique per-partner referral link carrying the partner id, auto-linked on arrival.
5. **Thank-you page.** A branded confirmation page ([`fsmdemo-thankyou.html`](../fsmdemo-thankyou.html))
   that the form's `retURL` redirects to after submit.

### Don't change these (they make it post)
`<form action>` (incl. `orgId`), hidden `oid`, hidden `retURL`, hidden `lead_source`, and every input
`name` (`first_name`, `last_name`, `email`, `company`, `phone`, and the custom `00N…` field). Style
everything else freely.

## Verifying the build
1. **Submit a test** through the hosted form (filled the fields in a browser and submitted).
2. **Confirm it landed** with MCP SOQL:
   `SELECT Id, Name, Company, LeadSource, Referred_By_Name__c, Referred_By_Partner__c FROM Lead WHERE Company = '<test company>'` —
   the Lead was created with the referred business, `Lead Source = Referral Partner`, and the
   referring partner captured in the text field (the lookup intentionally blank until qualification).
3. **Confirm the redirect** landed on the branded thank-you page.

## Hosting gotcha (page builder / CDN cache)
The form was hosted on a HighLevel page. The page builder showed the correct `retURL`, but the live
**bare URL served a cached copy with the old `retURL`** (it redirected to the wrong page). The cache
holds for hours.
- **Verify the live page, not the builder:** read the served HTML and check the actual hidden input
  value (e.g. `document.querySelector('input[name="retURL"]').value`).
- **Bust the cache with a query string** (`?v=1` / `?ref=demo`) — the bare URL keeps serving the
  cached version until the CDN expires, but any query string forces a fresh copy. Use the
  query-string URL for a demo so you don't hit the stale redirect.

---
Built by [Jibril Sulaiman](https://github.com/carljibrilsulaimanii).
