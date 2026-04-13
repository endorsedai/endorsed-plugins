---
description: Generate new cold email copy for an instantly.ai campaign
argument-hint: "[client name or campaign details]"
---

Use the `cold-email-generator` skill to write a new cold email for an instantly.ai campaign.

Campaign / client details:

$ARGUMENTS

Requirements:
- Pick the email type that best fits the client's available assets (default to Type 1 if unsure).
- Use spintax on greetings, action verbs, and closings (aim for 4-8 `{{RANDOM}}` blocks).
- Include personalization variables: `{{firstName}}`, `{{companyName}}`.
- Output: subject line, blank line, email body. No commentary, no labels.
- Pull client-specific facts (value prop, proof points, credibility) from project knowledge. Do not invent metrics.
