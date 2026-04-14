---
description: Generate a new Client Email Draft Doc (client-brief.md) from a client's website, sales deck, and pitch deck
argument-hint: "[client name] [client domain]"
---

Use the `client-brief-generator` skill in Full Brief mode.

Client details:

$ARGUMENTS

Requirements:
- Confirm the sales deck and pitch deck PDFs are uploaded to this Project before starting. If either is missing, stop and ask.
- Use the Apify MCP connector to scrape the client's website via `call-actor` with actor `apify/website-content-crawler` (maxCrawlPages 15, depth 2, crawlerType adaptive) — this covers homepage, about, product, pricing, case studies, customers, latest blog posts in one call.
- Save the scraped website content as `website-content.md` in the Project (for future section-refresh use).
- Produce the full `client-brief.md` with all 9 stable sections in the exact order specified in the skill.
- Enforce the 4 Hard Rules: succinct value prop, named clients in every case study, numerical ROI everywhere, credibility via background/investors/logos/certifications.
- Output the complete `client-brief.md` content, no meta-commentary.
- The account manager will save it to `01-generated/` and review before use.
