---
description: Generate new cold email copy for an instantly.ai campaign
argument-hint: "[client] [persona] [type 1-4] [sender name] [sender title]"
---

Use the `cold-email-generator` skill to write a new cold email for an instantly.ai campaign.

Campaign / client details:

$ARGUMENTS

Requirements:
- Run the Required Inputs pre-flight checklist FIRST. Before generating, confirm:
  1. **Sender first name** (from `client-brief.md` Company Snapshot, or ask)
  2. **Sender title** (same source, or ask)
  3. **Target recipient persona/role** (always ask, campaign-specific)
  4. **Email type** (1, 2, 3, or 4; default Type 1, confirm in one line so AM can override)
- If the user provided structured input like `Fertifa CRO Type 2 Jane COO`, parse it and skip asking.
- If any of the 4 required inputs are missing, STOP and ask the user in plain text. Never fill with `[Name]`, `[Title]`, `[role]` placeholders.
- Use spintax on greetings, action verbs, and closings (aim for 4-8 `{{RANDOM}}` blocks).
- Include personalization variables: `{{firstName}}`, `{{companyName}}`.
- Output: subject line, blank line, email body. No commentary, no labels.
- Pull client-specific facts (value prop, proof points, credibility) from project knowledge. Do not invent metrics.
- Pre-output validation: scan final copy for any `[...]` brackets that aren't `{{...}}` variables. If any found, rewrite with the missing values before output.
