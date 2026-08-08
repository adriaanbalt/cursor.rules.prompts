---
name: outbound-lead-pipeline
description: Run the BoardRecord outbound lead workflow from discovery and enrichment through CRM intake, founder approval, campaign handoff, and status sync. Use when starting lead generation, enriching prospects, pushing leads to CRM, or preparing an Instantly or Apollo campaign.
---

# Outbound Lead Pipeline

## Quick Start

Run a controlled outbound session. BoardRecord CRM is the system of record.
Use connected MCP tools before manual browsing or ad hoc scripts.

## Non-Negotiable Guardrails

1. **Never invent contact data.** An email must come from a source or enrichment provider. Record uncertainty instead of guessing.
2. **Never expose secrets.** Read keys from local configuration or environment variables; do not print, paste into chat, or commit them.
3. **Stop after CRM intake.** No export, campaign creation, enrollment, scheduling, or sending until the founder approves the leads in BoardRecord.
4. **Use only approved leads for outreach.** Source the send list from the approved CRM export, not from the discovery or enrichment working set.
5. **Keep cold outreach external.** Use Instantly or Apollo for cold campaigns. BoardRecord SES drips are only for warm/lifecycle email.
6. **Honor suppression.** Never re-enroll unsubscribed, bounced, or otherwise suppressed contacts.

## Workflow

Copy and maintain this checklist:

```text
Outbound Lead Pipeline:
- [ ] Step 1: Define the target
- [ ] Step 2: Discover with Exa
- [ ] Step 3: Enrich and verify with Apollo
- [ ] Step 4: Submit to BoardRecord CRM
- [ ] GATE: Founder reviewed and approved leads
- [ ] Step 5: Export approved leads
- [ ] Step 6: Create or update the cold campaign
- [ ] Step 7: Verify delivery and CRM sync
```

## Step 1: Define the Target

Before querying providers, confirm:

- Ideal customer profile, geography, company type, and role/title
- Required fields and target lead count
- Campaign or list name
- Exclusions, including existing customers and known suppression lists

If these materially affect the search and are missing, ask the user. Do not broaden
the target silently.

## Step 2: Discover with Exa

1. Confirm the Exa MCP is connected.
2. Search for companies and people matching the target.
3. Capture source URLs and concise evidence for why each prospect matches.
4. Deduplicate by normalized company domain before enrichment.

Do not substitute speculative web results when Exa is available. If the MCP is
unavailable, report the blocker and ask before using a fallback.

## Step 3: Enrich and Verify with Apollo

1. Confirm the Apollo MCP is connected.
2. Enrich the discovered companies and decision-makers.
3. Keep only provider-returned emails that are validly formatted.
4. Prefer verified deliverable emails when verification status is available.
5. Normalize email casing, website domains, names, titles, and phone numbers.
6. Deduplicate by normalized email.

Preserve provenance in `lead_source` and `notes`. Never construct an email from a
name and domain.

## Step 4: Submit to BoardRecord CRM

Post batches of 1–200 leads to:

```text
POST <BOARDRECORD_BASE_URL>/api/leads/intake
Authorization: Bearer $LEADS_INTAKE_API_KEY
Content-Type: application/json
```

Payload:

```json
{
  "leads": [
    {
      "email": "verified@example.com",
      "full_name": "Example Person",
      "title": "Property Manager",
      "phone": null,
      "role_type": "property_manager",
      "account_name": "Example Management",
      "account_segment": "property_manager",
      "website": "https://example.com",
      "lead_source": "exa_apollo",
      "source_permit_lead_id": null,
      "notes": "Matched target criteria; sources recorded in working notes."
    }
  ]
}
```

`email` is required. Optional fields are `full_name`, `title`, `phone`,
`role_type`, `account_name`, `account_segment`, `website`, `lead_source`,
`source_permit_lead_id`, and `notes`. BoardRecord's
`lib/crm/types.ts` (`crmLeadIntakeSchema`) is canonical.

Check the response counts for `received`, `created`, `enriched`, and `failed`.
Investigate failed rows; do not claim the whole batch succeeded when any row failed.
Successful rows land in CRM Contacts with `outreach_status = new`.

## Founder Approval Gate — Hard Stop

After intake:

1. Report the batch totals and any failures.
2. Direct the founder to Admin → CRM Contacts → Needs review.
3. Wait for the founder to review and select **Approve**.

Do not interpret intake success, a VA review, or a conversational "looks good" from
someone else as founder approval. Do not call campaign write/send tools while leads
remain `new`.

## Step 5: Export Approved Leads

After explicit confirmation that founder review is complete, obtain the approved
CSV from:

```text
GET <BOARDRECORD_BASE_URL>/api/admin/crm/contacts/export?outreach_status=approved
```

This admin route uses the founder's authenticated BoardRecord session. Treat the
export as the only authorized campaign input and re-check for duplicates and
suppressed contacts.

## Step 6: Create or Update the Cold Campaign

Use the selected provider's MCP:

1. Inspect the existing campaign/list before creating a duplicate.
2. Present the campaign, sender accounts, schedule, copy, and approved lead count.
3. Obtain explicit founder confirmation before activation or sending.
4. Add only approved exported contacts.
5. Keep the campaign paused until the founder confirms activation.

Use Instantly or Apollo for cold outreach. Never route this list into BoardRecord
SES lifecycle drips.

## Step 7: Verify Delivery and CRM Sync

1. Confirm the campaign state and enrolled count in Instantly or Apollo.
2. Verify Instantly webhook events reach `/api/webhooks/instantly`.
3. Spot-check that CRM activities are logged and outreach states advance.
4. Confirm unsubscribes and bounces are suppressed across future sends.
5. Report discrepancies without silently replaying sends or webhooks.

## Session Summary

End with:

```markdown
# Outbound Lead Session — [Date]

- Target: [ICP and campaign]
- Discovered: [count]
- Enriched with verified email: [count]
- CRM created / enriched / failed: [counts]
- Founder approval: PENDING | CONFIRMED
- Approved export count: [count or not run]
- Campaign status: NOT CREATED | PAUSED | ACTIVE
- Sync check: NOT RUN | PASS | ISSUES
- Follow-ups: [owners and actions]
```
