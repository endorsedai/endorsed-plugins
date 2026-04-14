---
description: Generate a new Client Email Draft Doc (client-brief.md) from a client's website, sales deck, and pitch deck
argument-hint: "[client name] [client domain]"
---

Use the `client-brief-generator` skill in Full Brief mode.

Client details:

$ARGUMENTS

Requirements:
- **Do NOT block on missing decks.** Inventory what's available (website URL, sales deck, pitch deck, case studies, prior winners), announce it in one line, then proceed with whatever's present.
- Use the Apify MCP connector to scrape the client's website. Call `apify/rag-web-browser` via `call-actor` in PARALLEL (6-8 tool calls in one response) against these URL patterns: `/`, `/about`, `/product` or `/products`, `/pricing`, `/customers` or `/case-studies`, `/blog`. Use `scrapingTool: "raw-http"` by default; retry individual pages with `browser-playwright` only if raw-http returns empty markdown.
- For sections where source material is missing, append a `[GAP — upload <X> via /brief-refresh <Section>]` placeholder so the account manager knows exactly how to strengthen the brief later.
- Only stop and ask the user if there is literally no source material at all (no website scrape + no uploaded files).
- Save the scraped website content as `website-content.md` in the Project (for future section-refresh use).
- Produce the full `client-brief.md` with all 9 stable sections in the exact order specified in the skill.
- Enforce the 4 Hard Rules: succinct value prop, named clients in every case study, numerical ROI everywhere, credibility via background/investors/logos/certifications.
- Output the complete `client-brief.md` content, no meta-commentary.
- The account manager will save it to `01-generated/` and review before use.
