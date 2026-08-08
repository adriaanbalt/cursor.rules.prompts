# AI Rules & Prompts

[![GitHub stars](https://img.shields.io/github/stars/adriaanbalt/ai.rules.prompts?style=social)](https://github.com/adriaanbalt/ai.rules.prompts)

A production-tested framework for AI-assisted development. Rules, skills, hooks, and governance that make Cursor behave like a senior engineer who knows your codebase.

**Stack-agnostic.** The methodology works for Next.js, Django, Rails, Go, Rust — anything. Stack-specific rules are included as a reference implementation you adapt.

---

## What's Included

| Layer | Purpose | Fires when |
|-------|---------|------------|
| **Rules** | Constraints the AI follows every session | Automatically (always-on or glob-triggered) |
| **Skills** | Multi-step workflow playbooks | On demand ("Run a QA session") |
| **Hooks** | Post-event behavioral nudges | After file saves |
| **Docs** | Rollout playbook, metrics, governance | Reference for team adoption |

---

## Quick Start

### Option A: Use this repo as your workspace (zero setup)

```bash
git clone https://github.com/adriaanbalt/ai.rules.prompts.git
cd ai.rules.prompts
cursor .    # Open in Cursor — rules and skills are active immediately
```

The `.cursor/` directory has symlinks to all rules and skills. Nothing to copy.

If you use MCP (Linear, Exa, Apollo, or Instantly), copy the example and
configure credentials locally:
```bash
cp .cursor/mcp.json.example .cursor/mcp.json
# Edit .cursor/mcp.json with your API key (gitignored, won't be committed)
```

### Option B: Copy into an existing project

```bash
git clone https://github.com/adriaanbalt/ai.rules.prompts.git
cd ai.rules.prompts

# Copy universal rules
cp rules/0*.mdc your-project/.cursor/rules/

# Copy skills
cp -r skills/* your-project/.cursor/skills/

# Copy AI trust boundary
cp .cursorignore.template your-project/.cursorignore

# (Optional) Copy hooks
cp hooks/hooks.json your-project/.cursor/hooks.json
```

Or use the setup script:
```bash
bash setup-cursor-rules.sh   # Run from your project root
```

---

## Repository Structure

```
ai.rules.prompts/
│
├── rules/                          # ← Copy into .cursor/rules/
│   │
│   │ # Universal (any project, any stack)
│   ├── 00-engineering-discipline   # Core reasoning & verification
│   ├── 01-surgical-fixes           # Debugging methodology
│   ├── 02-testing                  # Test patterns & coverage
│   ├── 03-typescript-types         # Type safety constraints
│   ├── 04-react-performance        # React/animation patterns
│   ├── 05-markdown-creation        # Documentation standards
│   ├── 06-qa-report                # QA report formatting
│   ├── 07-qa-regression            # Regression testing checklist
│   ├── 08-onboarding-scaffold      # Guided first-contribution workflow
│   ├── 09-daily-standup            # MCP-first daily activity summary
│   ├── 26-outbound-sales           # Lead-pipeline approval guardrails
│   │
│   │ # Stack example (adapt for your stack)
│   └── nextjs-supabase/
│       ├── 10-api-routes           # Route handler patterns
│       ├── 11-database-queries     # Supabase client usage
│       ├── 12-error-handling       # Domain errors & logging
│       ├── 13-security             # Auth, validation, secrets
│       ├── 14-supabase-migrations  # Migration workflow
│       ├── 15-hooks-react          # Custom hook patterns
│       ├── 16-design-system-tokens # CSS variables & theming
│       ├── 17-https-oauth          # Local HTTPS & OAuth config
│       ├── 18-environment-variables# Env var naming & validation
│       ├── 19-logging-monitoring   # Structured logging
│       ├── 20-multi-tenancy        # Tenant isolation (app_id)
│       ├── 21-stripe-connect       # Payments & webhooks
│       ├── 22-aws-lambda-sam       # Serverless handlers
│       ├── 23-email-pipeline       # Inbound/outbound email
│       ├── 24-rag-ai-pipeline      # Embeddings & hybrid search
│       └── 25-provider-abstraction # Swappable service providers
│
├── skills/                         # ← Copy into .cursor/skills/
│   ├── sdlc-workflow/              # Full 8-phase AI-native SDLC
│   ├── qa-session/                 # Full QA testing workflow
│   ├── outbound-lead-pipeline/      # VA discovery-to-outreach playbook
│   ├── feature-scaffold/           # Phased feature implementation
│   ├── deploy-checklist/           # Multi-service deployment
│   └── rule-authoring/             # How to write effective rules
│
├── hooks/                          # ← Copy hooks.json into .cursor/
│   ├── hooks.json                  # Post-save quality nudges
│   └── README.md                   # Hook philosophy & customization
│
├── docs/                           # Team adoption & governance
│   ├── AI-NATIVE-SDLC.md          # 8-phase methodology explained
│   ├── ROLLOUT-PLAYBOOK.md         # 4–6 week adoption plan
│   ├── PLATFORM-RUNBOOK.md         # Maintaining rules long-term
│   ├── SUCCESS-METRICS.md          # Measuring impact
│   └── GOVERNANCE.md               # Security & trust boundaries
│
├── .cursorignore.template          # AI trust boundary template
└── setup-cursor-rules.sh           # One-command install script
```

---

## AI-Native SDLC

This repo covers the **full software development lifecycle** — not just code generation:

```
Signal → Investigate → Plan → Ticket → Align → Execute → Review → Own
  1          2           3       4        5        6         7       8
```

| Phase | What this repo provides |
|-------|------------------------|
| 1–2 | `skills/sdlc-workflow` guides investigation & root-cause |
| 3–4 | `skills/sdlc-workflow` structures planning & acceptance criteria |
| 5 | Human step (AI drafted the brief) |
| 6 | `rules/` enforce conventions; `skills/feature-scaffold` structures execution |
| 7 | `skills/qa-session` for QA; review checklist in SDLC skill |
| 8 | `skills/deploy-checklist` for shipping; SDLC skill for monitoring |

Most teams only use Phase 6 (code generation). Phases 2–4 and 7–8 are where the real transformation happens. See [docs/AI-NATIVE-SDLC.md](docs/AI-NATIVE-SDLC.md) for the full methodology.

---

## How It Works

### Four Layers of Enforcement

```
┌─────────────────────────────────────────────────────┐
│  Rules (always-on)                                   │
│  "Match existing naming conventions"                 │
│  "Never leave TODOs or placeholders"                 │
├─────────────────────────────────────────────────────┤
│  Skills (on-demand)                                  │
│  "Run a full QA session from scope to report"        │
│  "Scaffold a feature with phased checkpoints"        │
├─────────────────────────────────────────────────────┤
│  Hooks (post-event)                                  │
│  "Did you check types after saving this .ts file?"   │
│  "Does this test cover error cases?"                 │
├─────────────────────────────────────────────────────┤
│  CI (blocks merge)                                   │
│  Lint, type-check, test suite                        │
│  Rules complement CI — they don't duplicate it       │
└─────────────────────────────────────────────────────┘
```

### Rule Format

Every `.mdc` rule file has YAML frontmatter + markdown body:

```markdown
---
description: One-line purpose (shown in Cursor's rule picker)
globs: "**/*.ts"      # Which files trigger this rule
alwaysApply: false    # true = active on every prompt (use sparingly)
---

# Rule Title

Constraints go here. Under 100 lines.
Include WHY — the incident or pattern that motivated this rule.
```

### Numbering Convention

| Range | Scope | Who writes them |
|-------|-------|-----------------|
| 00–09 | Universal (any project) | Framework maintainer |
| 10–19 | Stack-specific (framework patterns) | Stack expert |
| 20–29 | Domain-specific (payments, AI, email) | Domain expert |

---

## Skills

Skills are multi-step workflow playbooks. Install:

```bash
cp -r skills/* your-project/.cursor/skills/
```

| Skill | What it does | Example invocation |
|-------|-------------|-------------------|
| **sdlc-workflow** | Full 8-phase lifecycle: signal → investigate → plan → execute → own | "Walk me through fixing this bug end-to-end" |
| **qa-session** | Scope → execute tests → report → update tickets | "Run a QA session on this PR" |
| **outbound-lead-pipeline** | Exa discovery → Apollo enrichment → CRM approval → cold campaign | "Run the outbound lead pipeline for property managers" |
| **feature-scaffold** | Phased build with checkpoints at each layer | "Scaffold the new billing feature" |
| **deploy-checklist** | Multi-service deploy with rollback plan | "Deploy this to production" |
| **rule-authoring** | Write rules following the methodology | "Create a rule for our API patterns" |

**When to use skills vs rules:**
- Rules = "always do this" (constraints, automatic)
- Skills = "walk me through this" (workflows, on-demand)

---

## For QA Engineers

This repo is built for QA workflows:

| Resource | Purpose |
|----------|---------|
| `rules/06-qa-report` | Standardized report format the AI generates consistently |
| `rules/07-qa-regression` | Regression checklist covering all feature areas |
| `skills/qa-session` | Full workflow: scope → test → report → ticket updates |
| `rules/08-onboarding-scaffold` | Guided first contribution with validation checkpoints |

Start with: copy the universal rules + the `qa-session` skill into your project.

---

## For Outbound Lead VAs

Configure the Exa, Apollo, and Instantly MCPs from `.cursor/mcp.json.example`, then
invoke `outbound-lead-pipeline`. The workflow saves researched leads to BoardRecord
CRM and stops for founder approval before any export, enrollment, or send. See
[docs/VA-LEAD-PIPELINE.md](docs/VA-LEAD-PIPELINE.md).

---

## Adapting for Your Stack

The `rules/nextjs-supabase/` directory is a **reference implementation**. To create rules for your stack:

1. Create `rules/your-stack/`
2. Replace patterns with your framework's conventions
3. Keep the constraints-not-tutorials philosophy
4. Number 10–19 for stack patterns, 20–29 for domain patterns

**Example — Django:**
```
rules/django/
├── 10-views.mdc           # View patterns, DRF serializers
├── 11-models.mdc          # Model conventions, migrations
├── 12-auth.mdc            # Authentication/authorization
├── 13-testing.mdc         # pytest patterns, fixtures
└── 14-celery.mdc          # Task queue patterns
```

**Example — Go:**
```
rules/go/
├── 10-handlers.mdc        # HTTP handler patterns
├── 11-repository.mdc      # Data access layer
├── 12-errors.mdc          # Error wrapping conventions
├── 13-testing.mdc         # Table-driven tests
└── 14-concurrency.mdc     # Goroutine/channel patterns
```

---

## Design Principles

1. **Constraints, not tutorials.** Rules tell the AI what NOT to do. It already knows how to code.
2. **Under 100 lines.** Longer = tutorial. Split or trim.
3. **Include WHY.** "Don't use `any`" is weak. "Don't use `any` — it caused 3 prod type errors in June" is strong.
4. **Match, don't invent.** Encode YOUR patterns, not ideal patterns from documentation.
5. **Fail open.** Hooks remind; only CI blocks. A broken rule should never prevent work.
6. **Lean > comprehensive.** 10 sharp rules beat 50 verbose ones. Every token costs context.

---

## Team Adoption

See `docs/` for the full operational framework:

- **[AI-NATIVE-SDLC.md](docs/AI-NATIVE-SDLC.md)** — The 8-phase methodology: why most teams plateau at code generation
- **[ROLLOUT-PLAYBOOK.md](docs/ROLLOUT-PLAYBOOK.md)** — 4–6 week phased adoption plan (lighthouse → enable → mature)
- **[PLATFORM-RUNBOOK.md](docs/PLATFORM-RUNBOOK.md)** — Adding, modifying, and removing rules over time
- **[SUCCESS-METRICS.md](docs/SUCCESS-METRICS.md)** — Measuring impact (north star: time to first merged PR)
- **[GOVERNANCE.md](docs/GOVERNANCE.md)** — Security, privacy, MCP access control, agent guardrails
- **[VA-LEAD-PIPELINE.md](docs/VA-LEAD-PIPELINE.md)** — Safe VA workflow from lead research through approved outreach

---

## Contributing

1. Fork the repo
2. Add/modify rules following the numbering convention
3. Keep rules under 100 lines
4. Include a WHY annotation for any new constraint
5. Test in a real Cursor session before submitting
6. No PII, secrets, or internal project names

Use the `rule-authoring` skill for guided rule creation.

---

## License

MIT
