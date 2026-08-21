# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, Cursor, Copilot, etc.) when working with code in this repository.

## Repository Overview

A general-purpose collection of skills for AI coding agents, covering everything from Vercel/web development to cybersecurity, legal, finance, data, and dozens of third-party SaaS integrations. Skills are packaged instructions and scripts that extend agent capabilities.

## Skill Categories

`skills/` holds ~2,600 skills. Every skill directory name and its `SKILL.md` frontmatter description are self-documenting, so browse `skills/` directly for the full, current list. At a high level, they span:

- **Vercel & web development** — `deploy-to-vercel`, `react-best-practices`, `react-view-transitions`, `web-design-guidelines`, `vercel-optimize`, `vercel-cli-with-tokens`, `writing-guidelines`, `composition-patterns`, `react-native-skills`
- **Cybersecurity & incident response** (~800) — malware analysis, threat hunting, penetration testing, SOC playbooks, cloud/container/identity security, e.g. `analyzing-cobalt-strike-beacon-configuration`, `hunting-for-lateral-movement-via-wmi`, `implementing-zero-trust-network-access`
- **Vendor & SaaS SDKs/integrations** (~500) — Auth0, Twilio, Zoom, AWS, Azure, Cloudflare, Figma, HubSpot, Salesforce, Notion, Zapier, and dozens more, e.g. `auth0-nextjs`, `twilio-voice-twiml`, `figma-use`
- **Data & cloud infrastructure** (~350) — BigQuery, Snowflake, CockroachDB, Qdrant, DuckDB, data pipeline orchestration, e.g. `bigquery-data`, `qdrant-scaling`, `cloud-sql-postgres-admin`
- **Finance & investment** (~190) — cap table management (Carta), equity research, DCF/LBO modeling, month-end close, valuation, e.g. `discounted-cashflow-model`, `carta-waterfall-scenarios`, `financial-statement-analyzer`
- **Legal practice** (~160) — litigation, corporate, IP, employment, privacy, and compliance workflows per practice area, e.g. `litigation-legal-matter-workspace`, `written-consent`, `vendor-agreement-review`
- **Healthcare & life sciences** (~60) — `icd10-cm`, `fhir-developer`, `clinical-trial-protocol`, `prior-auth`
- **Business operations, sales, and productivity** (the remainder) — CRM/outreach, market research, meeting prep, project management, and general-purpose utility skills like `code-vuln-audit`, `secure-code-review`, `repo-audit`

Category boundaries above are approximate (skills came from several upstream marketplace bundles with overlapping themes); treat the counts as rough sizing, not an exact taxonomy.

## Creating a New Skill

### Directory Structure

```
skills/
  {skill-name}/           # kebab-case directory name
    SKILL.md              # Required: skill definition
    scripts/              # Optional: executable scripts
      {script-name}.sh    # Bash scripts
      {script-name}.mjs   # Node scripts
    references/           # Optional: supporting docs loaded on demand
    lib/                  # Optional: shared code for scripts
```

### Naming Conventions

- **Skill directory**: `kebab-case` (e.g., `vercel-deploy`, `log-monitor`)
- **SKILL.md**: Always uppercase, always this exact filename
- **Scripts**: `kebab-case.sh` or `kebab-case.mjs` (e.g., `deploy.sh`, `collect-signals.mjs`)

### SKILL.md Format

```markdown
---
name: {skill-name}
description: {One sentence describing when to use this skill. Include trigger phrases like "Deploy my app", "Check logs", etc.}
---

# {Skill Title}

{Brief description of what the skill does.}

## How It Works

{Numbered list explaining the skill's workflow}

## Usage

```bash
bash /mnt/skills/user/{skill-name}/scripts/{script}.sh [args]
```

**Arguments:**
- `arg1` - Description (defaults to X)

**Examples:**
{Show 2-3 common usage patterns}

## Output

{Show example output users will see}

## Present Results to User

{Template for how Claude should format results when presenting to users}

## Troubleshooting

{Common issues and solutions, especially network/permissions errors}
```

### Best Practices for Context Efficiency

Skills are loaded on-demand — only the skill name and description are loaded at startup. The full `SKILL.md` loads into context only when the agent decides the skill is relevant. To minimize context usage:

- **Keep SKILL.md under 500 lines** — put detailed reference material in separate files
- **Write specific descriptions** — helps the agent know exactly when to activate the skill
- **Use progressive disclosure** — reference supporting files that get read only when needed
- **Prefer scripts over inline code** — script execution doesn't consume context (only output does)
- **File references work one level deep** — link directly from SKILL.md to supporting files

### Script Requirements

- Bash scripts: use `#!/bin/bash` and `set -e`
- Node scripts: use `#!/usr/bin/env node` and `.mjs`
- Write status messages to stderr
- Write machine-readable output (JSON) to stdout
- Include a cleanup trap for temp files when scripts create them
- Reference scripts by relative path, for example `node scripts/{script}.mjs`

### End-User Installation

Document skills.sh installation for public skills:

```bash
npx skills add vercel-labs/agent-skills --skill {skill-name}
```

For manual installs, document the native path when the skill needs one.

**Claude Code:**
```bash
cp -r skills/{skill-name} ~/.claude/skills/
```

**claude.ai:**
Add the skill to project knowledge or paste SKILL.md contents into the conversation.

If the skill requires network access, instruct users to add required domains at `claude.ai/settings/capabilities`.
