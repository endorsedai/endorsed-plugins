---
description: Rewrite existing cold email copy with fresh phrasing and spintax
argument-hint: "[paste existing email copy]"
---

Use the `cold-email-generator` skill in refresh mode to rewrite the following cold email.

Existing copy:

$ARGUMENTS

Requirements:
- Preserve exactly: specific numbers (e.g. "30%", "$1m"), named client companies, and personalization variables (`{{firstName}}`, `{{companyName}}`).
- Reword phrasing, sentence structure, action verbs, and transitions.
- Add `{{RANDOM | ... }}` spintax to greetings, action verbs, connecting phrases, closings, and CTAs (4-8 blocks total).
- Keep the same email type / structure — don't switch formats.
- Output: refreshed subject line, blank line, refreshed email body. No commentary.
