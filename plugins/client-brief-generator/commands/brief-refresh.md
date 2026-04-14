---
description: Regenerate a single section of an existing client-brief.md after source materials change
argument-hint: "[client name] [section name, e.g., 'Case Studies / ROI']"
---

Use the `client-brief-generator` skill in Section Refresh mode.

Target:

$ARGUMENTS

Requirements:
- Read the existing `client-brief.md` in the Project to match voice and style.
- Identify the target section by exact heading match from the 9-section schema. If ambiguous, ask which section.
- Re-extract from whatever source material changed (new deck, updated website page, new case study PDF).
- If a website refresh is needed, use Apify `call-actor` with `apify/rag-web-browser` on the specific changed page URL only — do not re-crawl the whole site.
- Apply the 4 Hard Rules: succinct value prop, named clients, numerical ROI, credibility via background/investors/logos/certifications.
- Output ONLY the replacement section, starting with its `## ` heading, preceded by a single comment line: `<!-- Replace the "## [Section Name]" section with: -->`
- No other commentary.
