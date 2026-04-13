---
description: Add spintax variations to existing cold email copy
argument-hint: "[paste existing email copy]"
---

Use the `cold-email-generator` skill in refresh mode, focused on adding spintax to the copy below.

Existing copy:

$ARGUMENTS

Requirements:
- Preserve exactly: specific numbers, named clients, and personalization variables (`{{firstName}}`, `{{companyName}}`).
- Minimal rewording — keep the voice and structure intact.
- Add `{{RANDOM | ... }}` spintax to high-value spots: greetings, opening verbs, touch-base phrases, closings, and CTAs.
- Aim for 4-8 spintax blocks. Do not spintax numbers, named clients, or core value-prop meaning.
- Output: subject line (spintax if applicable), blank line, email body. No commentary.
