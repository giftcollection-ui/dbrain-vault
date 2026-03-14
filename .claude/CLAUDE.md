---
type: note
title: Agent Second Brain
last_accessed: 2026-03-04
relevance: 1.0
tier: active
---
# Agent Second Brain

Voice-first personal assistant for capturing thoughts and managing tasks via Telegram.

## EVERY SESSION BOOTSTRAP

**Before doing anything else, read these files in order:**

1. `vault/MEMORY.md` — curated long-term memory (preferences, decisions, context)
2. `vault/.memory-config.json` — memory decay configuration
3. `vault/daily/YYYY-MM-DD.md` — today's entries
4. `vault/daily/YYYY-MM-DD.md` — yesterday's entries (for continuity)
5. `vault/goals/3-weekly.md` — this week's ONE Big Thing
6. `vault/.session/handoff.md` — previous session context (if exists)

**Don't ask permission, just do it.** This ensures context continuity across sessions.

---

## SESSION END PROTOCOL

**Before ending a significant session, write to today's daily:**

```markdown
## HH:MM [text]
Session summary: [what was discussed/decided/created]
- Key decision: [if any]
- Created: [[link]] [if any files created]
- Next action: [if any]
```

**Also update `vault/MEMORY.md` if:**
- New key decision was made
- User preference discovered
- Important fact learned
- Active context changed significantly

**Update `vault/.session/handoff.md`:**
- Last Session: what was done
- Key Decisions: if any
- In Progress: unfinished work
- Next Steps: what to do next
- Observations: friction signals, patterns, ideas (type: `[friction]`, `[pattern]`, `[idea]`)

---

## Mission

Help user stay aligned with goals, capture valuable insights, and maintain clarity.

## Directory Structure

| Folder | Purpose |
|--------|---------|
| `daily/` | Raw daily entries (YYYY-MM-DD.md) |
| `goals/` | Goal cascade (3y → yearly → monthly → weekly) |
| `thoughts/ideas/` | Ideas and architectural decisions |
| `thoughts/learnings/` | Lessons and patterns (SSOT for Claude Code skills) |
| `thoughts/tasks/` | Task tracking and roadmaps |
| `thoughts/projects/` | Project overviews and roadmaps |
| `thoughts/reflections/` | Session and weekly reflections |
| `thoughts/research/` | Perplexity/NLM research results |
| `thoughts/security/` | Security findings and audits |
| `thoughts/economics/` | Pricing experiments and cost analysis |
| `MOC/` | Maps of Content indexes (auto-generated) |
| `attachments/` | Photos by date |
| `business/` | Business data (CRM, network, events) |
| `projects/` | Side projects (clients, leads) |

## Business Context

**Entry point:** `business/_index.md`

```
business/
├── _index.md       ← Start here (stats, overview)
├── crm/            ← Client records (companies + deals in one file)
├── network/        ← Company structure, partners
└── events/         ← Events, conferences
```

Search: `business/crm/{kebab-case}.md` (e.g. `acme-corp.md`, `client-b.md`)

## Projects Context

**Entry point:** `projects/_index.md`

```
projects/
├── _index.md       ← Start here
├── clients/        ← Project clients
├── leads/          ← Leads
└── projects/       ← Active projects
```

## Current Focus

See [[goals/3-weekly]] for this week's ONE Big Thing.
See [[goals/2-monthly]] for monthly priorities.

## Goals Hierarchy

```
goals/0-vision-3y.md    → 3-year vision by life areas
goals/1-yearly-YYYY.md  → Annual goals + quarterly breakdown
goals/2-monthly.md      → Current month's top 3 priorities
goals/3-weekly.md       → This week's focus + ONE Big Thing
```

## Entry Format

```markdown
## HH:MM [type]
Content
```

Types: `[voice]`, `[text]`, `[forward from: Name]`, `[photo]`

## Processing Workflow

Run daily processing via `/process` command or automatically at 21:00.

### 3-Phase Pipeline:
1. **CAPTURE** — Read daily entries → classify → JSON
2. **EXECUTE** — Create Todoist tasks, save thoughts, update CRM → JSON
3. **REFLECT** — Generate HTML report, update MEMORY, record observations

Each phase = fresh Claude context for better quality.

## Card Template (agent-memory)

**Skill:** `.claude/skills/agent-memory/SKILL.md`

All new vault cards follow the agent-memory template:

```yaml
---
type: crm|lead|contact|project|personal|note
description: >-
  One line — what a searcher will see in results
tags: [tag1, tag2]        # 2-5 tags, lowercase
status: active|draft|pending|done|inactive
industry: FMCG            # for CRM/leads
region: US                 # ISO codes
created: YYYY-MM-DD
updated: YYYY-MM-DD
# Auto fields (don't edit manually):
last_accessed: YYYY-MM-DD
relevance: 0.85
tier: active
---
```

**Rules:**
- `description` — REQUIRED. Write as a search snippet, NOT "contact" or "crm"
- `tags` — REQUIRED. 2-5 tags, lowercase, hyphen-separated
- `status` ≠ `tier`: status = business status, tier = memory (automatic)
- One fact = one place (DRY). References via [[wikilinks]]
- Decay engine: `uv run .claude/skills/agent-memory/scripts/memory-engine.py decay .`

## Skills & References

| Skill | Purpose |
|-------|---------|
| `dbrain-processor` | Main daily processing (3-phase pipeline) |
| `graph-builder` | Vault link analysis and building |
| `vault-health` | Health scoring, MOC generation, link repair |
| `agent-memory` | Card template, decay engine, tiered search |
| `todoist-ai` | Todoist task management via MCP |

- **Processing:** `.claude/skills/dbrain-processor/SKILL.md`
- **Graph Builder:** `.claude/skills/graph-builder/SKILL.md`
- **Vault Health:** `.claude/skills/vault-health/SKILL.md`
- **Agent Memory:** `.claude/skills/agent-memory/SKILL.md`
- **Todoist:** `.claude/skills/todoist-ai/SKILL.md`
- **Rules:** `.claude/rules/` (daily, thoughts, goals, obsidian-markdown, weekly-reflection)
- **Docs:** `.claude/docs/`

## Graph Builder

**Purpose:** Analysis and maintenance of vault link structure.

**Architecture:**
1. `scripts/analyze.py` — deterministic vault traversal
2. `scripts/add_links.py` — batch link addition
3. Agent — semantic links for orphan files

**Usage:**
```bash
# Analyze vault
uv run vault/.claude/skills/graph-builder/scripts/analyze.py

# Result
vault/.graph/vault-graph.json  # JSON graph with stats
vault/.graph/report.md         # Human-readable report
```

**Domains:**
| Domain | Path | Hub |
|--------|------|-----|
| Personal | thoughts/, goals/, daily/ | MEMORY.md |
| Business | business/crm/, business/network/ | business/_index.md |
| Projects | projects/clients/, projects/leads/ | projects/_index.md |

## Available Agents

| Agent | Purpose |
|-------|---------|
| `weekly-digest` | Weekly review with goal progress |
| `goal-aligner` | Check task-goal alignment |
| `note-organizer` | Organize vault, fix links |
| `inbox-processor` | GTD-style inbox processing |

## Path-Specific Rules

See `.claude/rules/` for format requirements:
- `daily-format.md` — daily files format
- `thoughts-format.md` — thought notes format
- `goals-format.md` — goals format
- `telegram-report.md` — HTML report format
- `obsidian-markdown.md` — Obsidian syntax rules
- `weekly-reflection.md` — weekly reflection template

## Report Format

Reports use Telegram HTML:
- `<b>bold</b>` for headers
- `<i>italic</i>` for metadata
- Only allowed tags: b, i, code, pre, a

## Quick Commands

| Command | Action |
|---------|--------|
| `/process` | Run daily processing |
| `/do` | Execute arbitrary request |
| `/weekly` | Generate weekly digest |
| `/align` | Check goal alignment |
| `/organize` | Organize vault |
| `/graph` | Analyze vault links |

## Research Protocol (NotebookLM)

When user says "research [topic]", "/do research: [topic]", or asks to deep-dive into a topic:

1. `nlm list notebooks` — check if a relevant notebook exists
2. If not: `nlm create notebook "[Topic]"`
3. Add sources (up to 5): `nlm add source --url [url] --notebook "[Title]"`
   - Accept: URLs, YouTube links, text content
4. Query: `nlm query "[Title]" "summarize key findings and actionable insights"`
5. Save findings to vault: `thoughts/ideas/[kebab-case-topic].md` with proper frontmatter
6. Report findings in Telegram HTML format

### NotebookLM Strategy

| Content Type | Where | Why |
|-------------|-------|-----|
| Daily capture, tasks, CRM | Obsidian vault | Structured, linked, decay-managed |
| Research (external sources, docs, articles) | NotebookLM | Multi-source synthesis, podcast gen |
| Project overview (GiftMixer) | Both | Vault = live context, NLM = external sources |
| Competitor analysis | Both | NLM for sources, vault for actionable insights |
| Learning paths | NotebookLM → vault | NLM aggregates, vault stores distilled knowledge |

### NotebookLM Notebooks Strategy

| Notebook | Purpose | Sources |
|----------|---------|---------|
| GiftMixer Platform | Product context for AI queries | Vault notes, GitHub docs, design docs |
| AI Models & Providers | Model landscape updates | FAL.ai docs, model blogs, pricing pages |
| Telegram Mini Apps | Platform knowledge | TG docs, developer blogs, case studies |
| Competitor Intelligence | Market positioning | Competitor sites, reviews, case studies |
| Agent Engineering | Agent/skill best practices | Research papers, blog posts, tutorials |

### NLM Integration Patterns

**Research → Vault pipeline:**
1. User asks about topic → check NLM for existing notebook
2. If exists: `nlm query` for synthesis
3. If not: create notebook, add 3-5 sources, query
4. Distill key findings → vault `thoughts/research/<topic>.md`
5. Cross-link with existing vault notes via [[wikilinks]]

**Podcast generation for review:**
- After major research → `nlm audio create` → user listens on commute

### Available NLM Commands

```bash
nlm list notebooks                              # List all notebooks
nlm create notebook "Title"                     # Create notebook
nlm add source --url URL --notebook "Title"     # Add URL source
nlm add source --youtube URL --notebook "Title" # Add YouTube source
nlm query "Title" "question"                    # Query notebook
nlm audio create --notebook "Title"             # Generate podcast
nlm mindmap create --notebook "Title"           # Generate mindmap
```

### Rules
- NotebookLM = READ-HEAVY, WRITE-LIGHT tool. Feed sources, query for synthesis.
- Canonical knowledge store = Obsidian vault. NLM is supplementary.
- Always save key findings back to vault (don't leave knowledge only in NLM).
- NLM CLI works on VPS (Linux), NOT on Windows (port 9223 blocked). Use browser on Windows.

## GiftMixer Project Skills (Cross-System Awareness)

This vault serves as SSOT for lessons and decisions consumed by GiftMixer project skills.
Skills with `VAULT CONTEXT` sections read these files on load:

| Vault File | Skills That Read It |
|-----------|-------------------|
| `thoughts/learnings/giftmixer-lessons.md` | prompt-architect, craft-engineer, quality-guardian, video-pipeline-engineer |
| `thoughts/ideas/giftmixer-decisions.md` | prompt-architect, craft-engineer, security-ops, video-pipeline-engineer |
| `thoughts/tasks/cinema-roadmap-p0-p3.md` | quality-guardian, video-pipeline-engineer |

**Feedback loop:** Claude Code writes lessons/decisions → vault → next session skills read updated vault.

## Obsidian Plugin Ecosystem

### Installed & Active

| Plugin | Purpose | Integration with Claude |
|--------|---------|----------------------|
| **obsidian-git** | Auto-sync every 10min | Enables VPS <-> Desktop <-> GitHub sync |
| **obsidian-local-rest-api** | REST API on port 27124 | Claude Code reads/writes vault via API |
| **smart-connections** | Semantic search (bge-micro-v2) | Find related notes automatically |
| **gemini-scribe** | In-Obsidian AI with Gemini | Alternative AI for vault-local work |

### Recommended Additions

| Plugin | What it does | Why useful |
|--------|-------------|-----------|
| **Dataview** | SQL-like queries over metadata | Query notes by type/tag/tier from within Obsidian |
| **Templater** | Advanced templates with JS | Auto-fill frontmatter, dynamic daily notes |
| **Periodic Notes** | Weekly/monthly notes | Structured weekly reviews + monthly summaries |
| **Calendar** | Calendar view of daily notes | Visual navigation of daily notes |
| **Kanban** | Kanban boards from markdown | Visual task management inside vault |
| **Tasks** | Task aggregation across vault | Collect all `- [ ]` items into one view |
| **DB Folder** | Database-like views of folders | Table view of thoughts/ with sorting/filtering |

## Customization

For personal overrides: create `CLAUDE.local.md`

## Learnings (from experience)

1. **Don't rewrite working code** without reason (KISS, DRY, YAGNI)
2. **Don't add checks** that weren't there — let the agent decide
3. **Don't propose solutions** without studying git log/diff first
4. **Don't break architecture** (process.sh → Claude → skill is correct)
5. **Problems are usually simple** (e.g., sed one-liner for HTML fix)

---

*System Version: 3.1 — Updated 2026-03-15 (vault-skill integration, NLM strategy, plugin ecosystem)*
