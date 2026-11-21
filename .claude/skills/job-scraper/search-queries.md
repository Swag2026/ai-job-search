# Search Queries for Job Scraper

<!-- SETUP: Customize these queries based on your skills, target roles, and location -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. None ship with AI Job Search: copy the ones for your market from [community-portals](https://github.com/ai-job-search-dev/community-portals) (e.g. `linkedin-search`, `freehire-search` under `portals/global/`), or build one with `/add-portal`. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** write every query category in every language listed in your CLAUDE.md Languages table (typically 1-2, sometimes more). A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule. Translate each category's keywords rather than machine-translating word-for-word (e.g. "Frontend Developer" -> "Desarrollador Frontend", not a literal word-for-word translation) if you work in more than one language.

## Search Sites

Primary (your market's job boards - scaffold one with `/add-portal`):
- **bayt.com** - largest general job board in the Gulf/UAE market
- **linkedin.com/jobs** - LinkedIn job listings (filter: UAE / Dubai); also covered by the community `linkedin-search` CLI once installed
- **gulftalent.com** - niche/industry board for Gulf region roles (optional)
- **naukrigulf.com** - another major board for the UAE/Gulf market (optional)

<!-- No portal CLI installed yet for these boards - /scrape currently falls back to WebSearch.
Install one via /add-portal or by copying a matching folder from community-portals if one exists. -->

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Write **each category in every language from your Languages table** (see Language scope above). Combine each query with your location terms (e.g. your city, region, or metro area) where the site supports it.

Each priority has two kinds of block. **Plain terms** blocks are what the portal CLIs consume: one term per line, one CLI call per term, no `OR`, no `site:` prefix, and no location (the CLI's location flag, or the city in `--query` where that portal's SKILL.md says so, carries it). Quote a multi-word phrase only if the portal's `Query syntax:` line says quoting is honoured. **WebSearch-only** blocks are Google syntax (`site:`, quotes, `OR`) for the fallback path and are never passed to a CLI.

**Organize by function, not job title.** The same underlying work carries different titles across companies and markets (a "Data Scientist" role at one employer may be posted as "Insights Analyst" or "Data Consultant" at another). Name each priority category after the function it covers, and list several plausible job titles as query variants within that category rather than betting an entire priority tier on one exact title string.

### Priority 1: Inventory & Warehouse Systems

These match your strongest and most desired career direction.

#### Plain terms (portal CLIs)

```
Inventory Control Specialist
Warehouse Systems Specialist
ERP Operations Specialist
Odoo Inventory
```

#### WebSearch-only (`site:` fallback)

```
site:bayt.com "Inventory Control Specialist" Dubai
site:bayt.com "Warehouse Systems Specialist" Dubai
site:bayt.com "Odoo" Dubai
site:linkedin.com/jobs "Inventory Control Specialist" UAE
```

### Priority 2: ERP / Odoo Development

These match your domain expertise building ERP-integrated systems.

#### Plain terms (portal CLIs)

```
Odoo Developer
ERP Developer
FastAPI Developer
```

#### WebSearch-only (`site:` fallback)

```
site:bayt.com Odoo Developer Dubai OR "Abu Dhabi"
site:gulftalent.com Odoo ERP UAE
site:linkedin.com/jobs Odoo Developer Dubai UAE
```

### Priority 3: Full Stack Developer (ops/ERP-adjacent)

Adjacent roles you could pivot into, given your in-house full-stack build-out (FastAPI, React, PostgreSQL).

#### Plain terms (portal CLIs)

```
Full Stack Developer
Python Developer
```

#### WebSearch-only (`site:` fallback)

```
site:bayt.com "Full Stack Developer" FastAPI Dubai
site:bayt.com "Python Developer" Dubai
```

### Priority 4: Broader Technical / Operations Consulting

Wider net for general technical and ops-systems roles.

#### Plain terms (portal CLIs)

```
Python developer
operations systems consultant
```

#### WebSearch-only (`site:` fallback)

```
site:bayt.com Python developer Dubai
site:linkedin.com/jobs "Python developer" Dubai
site:bayt.com "operations systems" consultant Dubai
```

## Location Filter

When evaluating results, verify the job location matches the relocation target. Define acceptable areas:
- Dubai and surrounding areas (primary target)
- Abu Dhabi, UAE (acceptable)
- Other UAE emirates (acceptable)
- Other Gulf countries (borderline - discuss with user before applying)
- Outside the Gulf region (too far, unless role is fully remote)

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table. When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, matching `/scrape` Step 1b.3: use a supported recency filter or filter the returned publication dates client-side. A future application deadline does not bypass this discovery window; `/rank` handles deadline expiry and urgency separately after discovery. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
