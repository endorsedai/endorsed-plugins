---
name: client-brief-generator
description: Generates a standardized Client Email Draft Doc (client-brief.md) from a client's website, sales deck, and pitch deck. Use whenever onboarding a new client, or when the user says "generate client brief for [name]", "create email draft doc for [client]", "build brief for [client]", "set up [client] in the system", or uploads website/deck/pitch materials and asks for a brief. Also trigger for section refreshes when the user says "regenerate case studies for [client]", "refresh the value prop section", "update the personalisation openers", or provides updated materials and names a specific section. Produces a canonical markdown brief with 9 stable sections that the cold-email-generator skill consumes.
---

# Client Brief Generator

## Overview

This skill produces the canonical per-client knowledge artifact: **`client-brief.md`**. The cold-email-generator skill consumes this file when writing copy for the client.

There are two modes:

1. **Full Brief** — initial generation from website + sales deck + pitch deck. Run once per new client.
2. **Section Refresh** — regenerate a single section when source materials change. Run whenever the client updates a deck or positioning.

**The output is the same every time for the same inputs.** This skill enforces a fixed schema so briefs are interchangeable across all 45+ Endorsed AI clients.

---

## Prerequisites

This skill requires the **Apify MCP connector** to be configured in Claude.ai Cowork admin:

- Remote MCP URL: `https://mcp.apify.com`
- Auth: OAuth (browser redirect) OR `Authorization: Bearer <APIFY_TOKEN>` header
- MCP tools exposed: `search-actors`, `call-actor`, `fetch-actor-details`, `add-actor`
- Actor used by this skill: **`apify/rag-web-browser`**
  - Fast single-page fetches (~15-22s each) that fit in MCP's 60s synchronous call window
  - Returns LLM-ready markdown directly
  - Used for BOTH full brief (multiple targeted calls) AND section refresh (single call)
  - Enabled by default on Apify MCP, no separate activation needed

**Why not `apify/website-content-crawler`?** It's designed to crawl entire sites and routinely exceeds MCP's 60s timeout, forcing async mode which has separate permissions issues. `rag-web-browser` solves both by being fast-per-call.

Use `scrapingTool: "raw-http"` for static marketing sites (cheaper, ~5s). Use `scrapingTool: "browser-playwright"` for JS-heavy SaaS sites (~15-22s). When in doubt, start with `raw-http` — if markdown comes back empty or obviously broken, retry with `browser-playwright`.

If Apify MCP is not wired up, ask the user to either:
- Set it up in the Cowork admin, OR
- Paste the website content as markdown directly into the Claude Project as `website-content.md`

Never fabricate website content. If neither Apify nor a pasted file is available, stop and ask.

---

## Input Materials

The skill expects these inputs in the active Claude Project:

| File | Purpose | Required? |
|---|---|---|
| Sales deck (PDF) | Value prop, positioning, ROI claims | Yes |
| Pitch deck (PDF) | Credibility, team, traction | Yes |
| Client domain or URL | Website content for tone + positioning | Yes (via Firecrawl) |
| Case studies (PDF/MD) | Named ROI outcomes | Optional — extract from decks if absent |
| Prior winning emails (MD) | Voice calibration | Optional |

If sales deck OR pitch deck is missing, ask the user before proceeding. Do not infer from website alone — decks contain the claims that make copy persuasive.

---

## Mode Decision Tree

**Full Brief** when:
- User is onboarding a new client
- No `client-brief.md` exists in the project
- User explicitly asks for a full brief / full draft doc

**Section Refresh** when:
- A `client-brief.md` already exists in the project
- User names a specific section (e.g., "regenerate case studies", "refresh value props")
- User provides an updated deck or material and asks to update the brief

In Section Refresh mode, output **only the replacement section text** (including its `## ` heading). The user/account manager pastes it in, replacing the old section.

---

## The 4 Hard Rules (Endorsed AI tips)

These are non-negotiable. Every brief must satisfy all four:

1. **Succinct value prop in the intro** — one clear sentence on what the business does. No jargon dumps. No buzzwords. Use the client's own language from their website/deck, not generic marketing speak.
2. **Name-drop clients explicitly** — generic "our customers" fails. Every case study must name the specific client (e.g., "Solidroad", "Valeo Foods", "Indico Data").
3. **Include numerical ROI data** — specific percentages, dollar amounts, multiples. "30% of total revenue", "$6.5m Seed Round", "50% increase in conversion rates", "250x ROI". Ranges acceptable only when the source provides ranges.
4. **Credibility via background/investors/credentials/reviews** — not self-praise. Show why they should trust this business: founder pedigree, investor names (Sam Altman, Passion Capital), client logos (HP Enterprise, Cisco, Snowflake), certifications (SOC 2, FCA-regulated), review platforms (G2 ratings).

If any section fails one of these rules, regenerate it before outputting the brief.

---

## The 9-Section Schema (stable headings — DO NOT rename)

The brief file must use these exact `## ` headings in this exact order. The cold-email-generator skill and section-refresh mode depend on stable headings.

```markdown
# Client Brief: <Client Name>

## Company Snapshot
## ICP & Personas
## Value Proposition
## Case Studies / ROI
## Credibility Lines
## Personalisation Openers
## Subject Lines
## Soft Closes
## Full Email Examples
```

---

## Section-by-Section Generation Guide

### 1. Company Snapshot

3-5 bullets. The fastest possible onboarding context.

```markdown
## Company Snapshot

- **One-liner**: [One sentence — what they do, for whom]
- **Category / vertical**: [e.g., "B2B SaaS — sales enablement"]
- **Stage / size**: [Series A, 50 employees, $10m ARR — whatever's public]
- **Backers / notable investors**: [if mentioned in pitch deck]
- **Primary sender** (for emails): [Founder name + title — e.g., "Julie, COO"]
```

### 2. ICP & Personas

Each persona gets a sub-heading and 2-3 bullets. Minimum 1 primary persona, up to 4 total. Pull from the decks' "target customer" slides and the website's use cases.

```markdown
## ICP & Personas

### Primary: [Title/role]
- Responsibilities: [what they own]
- Pain points: [what keeps them up at night, in their language]
- Why this company helps: [one sentence link to value prop]

### Secondary: [Title/role]
- [same structure]
```

### 3. Value Proposition

5-7 variants, each a single sentence. Group by angle. Use the client's own phrasing from website/deck wherever possible — the goal is voice-matched, not AI-generic.

```markdown
## Value Proposition

### Succinct
- [Variant 1 — shortest, sharpest]
- [Variant 2]

### Credibility-led
- [Variant — leads with the trust signal]
- [Variant]

### ROI-led
- [Variant — leads with the outcome metric]
- [Variant]

### Product-specific (if multi-product)
- **[Product A]**: [Variant]
- **[Product B]**: [Variant]
```

### 4. Case Studies / ROI

Organized by outcome metric (not by client). 2-4 bullets per metric. Each bullet MUST include: (a) named client, (b) specific number, (c) the outcome.

```markdown
## Case Studies / ROI

### Win Rate / Conversion
- [Client] helped [client name] go from X% to Y% [with specifics]

### Deal Size / ACV
- [Client] partnered with [client name] to drive Nx ACV uplift [with specifics]

### Sales Cycle / Velocity
- [...]

### Forecast Accuracy
- [...]

### Blended / Multi-metric
- [Long-form narrative case study with 2-3 stacked outcomes]
```

Only include metric categories the client actually has data for. Do not fabricate metrics to fill categories.

### 5. Credibility Lines

4-8 one-liners. Each is a standalone credibility statement that can drop into an email without context.

```markdown
## Credibility Lines

- **Scale / social proof**: [e.g., "Used by 500+ B2B sales organisations"]
- **Founder / team**: [e.g., "Founded by Andy Whyte, best-selling author..."]
- **Investors / advisors**: [e.g., "Backed by Sam Altman"]
- **Named customers**: [e.g., "Trusted by HP Enterprise, Comcast, UiPath"]
- **Certifications / compliance**: [e.g., "SOC 2 Type II audit-ready"]
- **Reviews / awards**: [e.g., "Rated #1 Digital Sales Room on G2"]
```

### 6. Personalisation Openers

Group by signal type. Each group has 2-4 variants. Follow the Endorsed AI pattern: short, references the prospect's specific context.

```markdown
## Personalisation Openers

### By Role: [Primary Persona]
- Hi {{firstName}}, as you're leading [role] at {{companyName}} I wanted to touch base.
- Hey {{firstName}}, given you're [role-phrasing] at {{companyName}} I wanted to reach out.

### By Role: [Secondary Persona]
- [variants]

### Competing Methodology / Tool Signal
- Hi {{firstName}}, as your team is using [competitor] at {{companyName}} I wanted to touch base.
- [variants for each known competitor]

### LinkedIn Signal
- Hi {{firstName}}, I saw you're following [relevant page] on LinkedIn and thought it made sense to connect.

### Recent Event (funding, hiring, launch)
- Congrats on the recent [growth/raise] at {{companyName}}. [context question].
```

Only include signal groups the client has real targeting data for. Don't pad with generic "engineering leader" variants if they don't target that persona.

### 7. Subject Lines

4-6 variants. Keep low-friction. Use the patterns from the existing cold-email-generator skill.

```markdown
## Subject Lines

- {{firstName}} <> [SenderFirstName] | Introduction
- {{firstName}} <> [SenderFirstName] | Intro and Invite
- Introduction | {{firstName}} <> [SenderFirstName]
- {{RANDOM | Quick question, {{firstName}} | Question about {{companyName}}}}
- {{companyName}} + [topic]
```

### 8. Soft Closes

3-5 variants. Match the client's tone (formal vs. casual).

```markdown
## Soft Closes

- Open to a quick chat?
- Worth a conversation?
- Would you be open to more info or a chat?
- Would it make sense to connect?
- Does it make sense to explore this briefly?
```

### 9. Full Email Examples

One example per applicable format from the Endorsed AI "4 Types of First Emails" playbook. All assembled from the sections above — this section IS the proof that the brief is self-consistent.

```markdown
## Full Email Examples

### Type 1: Personalisation → Intro/VP → Case Study → Credibility → Soft Close
[Complete email, ready to paste into instantly.ai. Uses variables and spintax.]

### Type 2: Case Study Led (Multiple Case Studies)
[Complete email]

### Type 3: Bullet Point Format
[Complete email]

### Type 4: Condensed
[Complete email]
```

Only include formats that fit the client's asset strength. If the client has only one strong case study, skip Type 2. If the value prop is single-dimensional, skip Type 3.

---

## Full Brief Workflow

When generating a new brief:

1. **Confirm inputs**
   - Ask the user for the client's domain/URL
   - Confirm sales deck and pitch deck are uploaded to the Project
   - If either deck is missing, stop and ask

2. **Scrape the website via Apify — multi-call pattern**

   Make 6-8 **parallel** `call-actor` invocations against `apify/rag-web-browser`, one per key page. Parallelize them in a single response so total wall-time is ~20s, not 2 minutes.

   Target URL patterns (try each; skip ones that 404):
   - `https://<client-domain>/` (homepage — always)
   - `https://<client-domain>/about` or `/about-us`
   - `https://<client-domain>/product` or `/products` or `/platform`
   - `https://<client-domain>/pricing`
   - `https://<client-domain>/customers` or `/case-studies` or `/success-stories`
   - `https://<client-domain>/blog` (homepage of blog; extract 2-3 recent post URLs from it and fetch those too if valuable)

   Per-call input template:
   ```json
   {
     "query": "https://<client-domain>/<path>",
     "maxResults": 1,
     "scrapingTool": "raw-http",
     "removeCookieWarnings": true
   }
   ```

   If a call returns empty/broken markdown (common for JS-heavy SaaS sites), retry that specific URL once with `"scrapingTool": "browser-playwright"`. Don't flip everything to Playwright — it's 4-10x more expensive.

3. **Combine and save**
   - Concatenate all returned markdown into a single file with clear `## <URL>` separators between pages
   - Save to the Project as `website-content.md` — used for future section refreshes without re-scraping

3. **Extract structured data from decks**
   - Read sales deck → pull value prop, product descriptions, ROI claims, case study details
   - Read pitch deck → pull team, investors, traction numbers, market positioning
   - Cross-reference with website content for consistency

4. **Draft the brief**
   - Fill all 9 sections using the schema
   - Enforce the 4 Hard Rules on every section
   - Use the client's own language — quote phrases verbatim from their decks where possible

5. **Self-check before output**
   - Does every case study name a specific client?
   - Does every ROI claim have a specific number?
   - Is the value prop under 25 words?
   - Are credibility lines standalone (work without extra context)?
   - If any answer is no, rewrite that section

6. **Output**
   - Full `client-brief.md` content
   - No commentary, no meta-explanation
   - The account manager will save it to the Project and review

---

## Section Refresh Workflow

When updating a single section:

1. **Identify the target section** from the user's message
   - Exact heading match: "## Case Studies / ROI", "## Value Proposition", etc.
   - If ambiguous, ask which of the 9 sections

2. **Read the updated source material**
   - New sales deck? Re-extract value prop + case studies + credibility
   - New case study PDF? Slot into the right metric category
   - Website re-fetch? Single `call-actor` to `apify/rag-web-browser` with the specific changed URL — do NOT re-scrape the full site

3. **Regenerate ONLY that section**
   - Apply the 4 Hard Rules
   - Match the style of the existing brief (read `client-brief.md` to calibrate)

4. **Output**
   - The single section, with its `## ` heading, ready to replace the old section verbatim
   - Start output with a one-line note: `<!-- Replace the "## [Section Name]" section with: -->`
   - Then the section itself
   - No other commentary

---

## File Location & Project Structure

The expected Project folder structure:

```
📁 Claude Project: "Client: <Name> — Outreach"
├── 00-inputs/
│   ├── sales-deck.pdf
│   ├── pitch-deck.pdf
│   └── website-content.md       ← generated by Apify during full brief
├── 01-generated/
│   └── client-brief.md          ← this skill's output
└── 02-campaigns/
    └── [per-campaign outputs]
```

When running Full Brief, save `client-brief.md` to `01-generated/`. Account manager reviews and edits before it's considered final.

---

## Key Principles

**Deterministic schema.** The 9 section headings are contracts. Never rename them, never add new top-level sections without updating this SKILL.md and the cold-email-generator skill in lockstep.

**Client's voice, not yours.** Pull phrases verbatim from their website and decks. If they say "revenue operating system" on their homepage, use "revenue operating system" — don't paraphrase to "sales automation platform."

**Numbers beat adjectives.** Every time. If the source material has "significant growth", dig for the actual number in an adjacent slide. If you can't find it, mark it `[NUMBER NEEDED]` in the brief so the account manager can source it — do not invent.

**Batch scrapes in parallel.** When making multiple `rag-web-browser` calls for a single brief, issue them in one response as parallel tool calls. Sequential calls waste time — parallel keeps full-brief generation under 60s wall clock.

**Named clients only.** Never write "our customers" or "leading brands." If the client hasn't publicly named any customer yet, flag this to the account manager as a gap that will hurt copy performance.

**Account managers review everything.** This skill produces a draft. The AM is responsible for correctness before the brief is used in campaigns.
