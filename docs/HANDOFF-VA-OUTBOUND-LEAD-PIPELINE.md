# Handoff: VA outbound lead pipeline → `cursor.rules.prompts`

**Date:** 2026-08-07  
**Source conversation:** BoardRecord CRM outbound leads + Instantly sync (implemented & merged to `main` via PR #23).  
**Next workstream:** Enhance `app/cursor.rules.prompts` so the VA can run that flow from Cursor.

---

## Where to work

| Path | Role |
|------|------|
| `/Users/adriaanbalt/PROJECTS/app/cursor.rules.prompts` | **Primary workspace for this work** — VA opens this repo in Cursor (Option A). |
| `/Users/adriaanbalt/PROJECTS/app/boardrecord` | Product source of truth for APIs/schema; do **not** put VA playbooks only here (VA does not live in that workspace). |

Upstream of the rules repo: `git@github.com:adriaanbalt/ai.rules.prompts.git`.

---

## Product flow already shipped (BoardRecord)

CRM is the system of record (`crm_accounts` / `crm_contacts`). Permits are a linked signal via `source_permit_lead_id`.

1. **Discover** — Exa MCP  
2. **Enrich** — Apollo MCP  
3. **Intake** — `POST /api/leads/intake` with `Authorization: Bearer $LEADS_INTAKE_API_KEY`  
4. **Review** — Admin CRM Contacts; `outreach_status`: `new` → founder **Approve** → `approved`  
5. **Export** — `GET /api/admin/crm/contacts/export?outreach_status=approved` (CSV for Instantly/Apollo)  
6. **Send** — Instantly/Apollo MCP (cold only — **not** BoardRecord SES drips)  
7. **Sync** — Instantly → `POST /api/webhooks/instantly` advances status + logs `crm_activities` + cross-suppresses unsubs/bounces  

Key files in BoardRecord:

- `app/api/leads/intake/route.ts`
- `lib/leads/intake-auth.ts`
- `lib/crm/types.ts` (`crmLeadIntakeSchema`, `crmOutreachStatuses`)
- `lib/crm/contacts.ts` (`upsertLeadContact`)
- `lib/crm/outreach-transitions.ts`
- `app/api/webhooks/instantly/route.ts`
- `app/api/admin/crm/contacts/export/route.ts`
- `app/admin/crm/contacts/CRMContactsClient.tsx`
- Migration: `supabase/migrations/20260808000000_crm_outreach_leads.sql`
- Env: `LEADS_INTAKE_API_KEY`, `INSTANTLY_WEBHOOK_SECRET` (in `.env.example`)

Intake payload shape (flat, max 200/batch): `email`, optional `full_name`, `title`, `phone`, `role_type`, `account_name`, `account_segment`, `website`, `lead_source` (default `manual`), `source_permit_lead_id`, `notes`.

Outreach statuses: `new` | `approved` | `contacted` | `interested` | `not_interested` | `converted` (null = not a cold lead).

---

## Decision already made for `cursor.rules.prompts`

**Recommended approach: Hybrid H = B + F + short runbook**

1. Extend `.cursor/mcp.json.example` with **Exa, Apollo, Instantly** (placeholders; live keys in gitignored `.cursor/mcp.json`).  
2. One skill: `skills/outbound-lead-pipeline/SKILL.md` — full 6-step VA playbook (mirror `skills/qa-session/`).  
3. One thin trigger rule (like `07-qa-regression`): fires on “start lead gen / enrich leads / push to CRM / run Instantly”; enforces MCP-first + **never skip founder approval** + never invent emails.  
4. Short `docs/VA-LEAD-PIPELINE.md` for humans.  
5. **Do not** make it always-on. **Do not** put this only in `boardrecord/.cursor/`. Prefer one end-to-end skill over many phase skills for v1.

Naming: prefer `outbound-lead-pipeline` / `outbound-sales` for filenames; BoardRecord URLs/env names inside the skill body are fine.

Hard constraints:

- Never commit secrets / API keys.  
- Reference intake schema; BoardRecord code remains canonical.  
- Cold outreach stays on Instantly/Apollo; DIY drips are warm/lifecycle only.  
- Hard stop after intake until `outreach_status=approved`.

---

## Current state of `cursor.rules.prompts` (gaps)

- Engineering SDLC framework; VA uses it as workspace.  
- Strong MCP-first patterns: `rules/06-qa-report.mdc`, `07-qa-regression.mdc`, `09-daily-standup.mdc`, `skills/qa-session/`.  
- MCP example today: **Linear only** (`.cursor/mcp.json.example`).  
- **Zero** Exa / Apollo / Instantly / BoardRecord lead-flow content.  
- Contributing note: “No PII, secrets, or internal project names” — keep secrets out; BoardRecord as the named product inside the skill is OK for this VA use case.  
- Active rules via `.cursor/` symlinks to `rules/00–09` and `skills/*`.

---

## Suggested implementation order (next agent)

1. MCP example (+ optional GOVERNANCE note that Instantly/Apollo are write/send risk).  
2. `skills/outbound-lead-pipeline/SKILL.md` (+ symlink under `.cursor/skills/` if needed).  
3. Thin trigger rule under `rules/` (number consistently; symlink into `.cursor/rules/`).  
4. `docs/VA-LEAD-PIPELINE.md`.  
5. README touch: document the new skill + MCP setup for the VA.

Ground in existing patterns: read `skills/qa-session/SKILL.md` and `rules/07-qa-regression.mdc` before writing.

Also useful: `/Users/adriaanbalt/.cursor/skills-cursor/create-rule/SKILL.md` and `create-skill/SKILL.md`.

---

## Starter prompt for the other agent

Copy everything below into the new chat (and `@` this file):

```
Implement the VA outbound lead pipeline enhancements in @app/cursor.rules.prompts per the handoff at @app/cursor.rules.prompts/docs/HANDOFF-VA-OUTBOUND-LEAD-PIPELINE.md

Follow the recommended Hybrid approach:
1. Extend .cursor/mcp.json.example with Exa, Apollo, Instantly (placeholders only)
2. Add skills/outbound-lead-pipeline/SKILL.md (end-to-end playbook mirroring qa-session)
3. Add a thin trigger rule (not alwaysApply) enforcing approval gate + MCP-first
4. Add docs/VA-LEAD-PIPELINE.md
5. Symlink into .cursor/ as this repo does for other rules/skills; update README briefly

Do not put this only in boardrecord/.cursor/. Do not commit secrets. Ground in CODING conventions of this rules repo and mirror 07-qa-regression + qa-session patterns.
```
