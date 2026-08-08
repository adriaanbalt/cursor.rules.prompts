# VA Outbound Lead Pipeline

This runbook lets a VA research and enrich prospects from this rules workspace,
save them into BoardRecord, and prepare an approved list for cold outreach.
BoardRecord CRM is the system of record.

## One-Time Setup

1. Copy the MCP example:

   ```bash
   cp .cursor/mcp.json.example .cursor/mcp.json
   ```

2. Configure Exa, Apollo, and Instantly locally. Follow each provider's current
   authentication flow. `.cursor/mcp.json` is gitignored; never commit or paste keys.
3. Restart Cursor and verify each MCP connection with a read-only request.
4. Ask the founder for the BoardRecord base URL and a scoped
   `LEADS_INTAKE_API_KEY`. Store them in the approved local secret manager or
   environment, not in this repository.

The MCP integrations have different risk levels: Exa discovery is primarily read
access; Apollo enrichment may consume credits; Instantly/Apollo campaign writes can
send external email. Use minimum permissions and require founder confirmation before
campaign activation.

## Routine Workflow

Start in Cursor with:

> Run the outbound lead pipeline for [target market, geography, roles, and count].

Cursor should load `skills/outbound-lead-pipeline/SKILL.md` and then:

1. Define the target and exclusions.
2. Discover matching prospects with Exa.
3. Enrich and verify contacts with Apollo.
4. Deduplicate and submit 1–200 leads per batch to BoardRecord.
5. Stop while the founder reviews `new` leads in Admin → CRM Contacts.
6. After founder approval, export only `approved` contacts.
7. Create or update the cold campaign through Instantly or Apollo, keeping it paused
   until the founder confirms activation.
8. Verify campaign state, webhook sync, CRM activities, and suppression handling.

## BoardRecord Intake Endpoint

```text
POST <BOARDRECORD_BASE_URL>/api/leads/intake
Authorization: Bearer $LEADS_INTAKE_API_KEY
Content-Type: application/json
```

The body is a `leads` array with 1–200 flat records:

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
      "notes": "Reason this prospect matches the target and source provenance."
    }
  ]
}
```

`email` is required. BoardRecord's `crmLeadIntakeSchema` in `lib/crm/types.ts` is
the canonical contract.

The endpoint:

- Rejects requests unless the configured scoped bearer key matches
- Upserts CRM contacts by normalized email
- Creates or links CRM accounts
- Adds missing enrichment without overwriting established contact data
- Sets new cold leads to `outreach_status = new`
- Returns per-batch and per-row `created`, `enriched`, or `failed` results

Imported leads appear in Admin → CRM Contacts under **Needs review**. The founder
uses **Approve** to move selected records to `approved`.

## Approval Boundary

`new` means saved for review, not authorized for outreach.

Do not export, enroll, schedule, activate, or send a lead until the founder approves
it. After approval, use:

```text
GET <BOARDRECORD_BASE_URL>/api/admin/crm/contacts/export?outreach_status=approved
```

This export requires the founder's authenticated admin session and is the only
authorized input to a cold campaign.

## Data Quality and Safety

- Never guess or construct an email address.
- Retain source/provenance in `lead_source` and `notes`.
- Deduplicate company domains before enrichment and emails before intake.
- Never commit keys, production URLs, exports, or lead PII to this repository.
- Never re-enroll unsubscribed, bounced, or suppressed contacts.
- Use Instantly/Apollo for cold outreach; BoardRecord SES is for warm/lifecycle
  communication only.

## Troubleshooting

- **MCP unavailable:** Check local `.cursor/mcp.json`, authentication, and restart
  Cursor. Do not silently switch to invented or unsourced data.
- **401/authorization error:** Confirm BoardRecord has `LEADS_INTAKE_API_KEY`
  configured and the VA is using the matching local value.
- **Validation error:** Compare the batch with `crmLeadIntakeSchema`; split batches
  over 200.
- **Failed rows:** Review each returned row. Do not report full success when failures
  remain.
- **Lead not ready to send:** Confirm it is visible as `approved`, not merely `new`.
- **Campaign event missing in CRM:** Check Instantly webhook delivery before retrying.
  Never replay a send to repair status.
