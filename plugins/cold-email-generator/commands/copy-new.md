---
description: Generate new cold email copy for an instantly.ai campaign. Outputs each sequence as a separate markdown artifact for easy copy-paste.
argument-hint: "[client] [persona] [type 1-4] [seq1 or seq1+2] [sender name] [sender title]"
---

Use the `cold-email-generator` skill to write cold email copy for an instantly.ai campaign.

Campaign / client details:

$ARGUMENTS

Requirements:
- **Parse `$ARGUMENTS` first**. Extract any of these the user already provided: client, persona, email type, sequence depth, sender name, sender title. Do NOT re-ask for anything already given.
- **Check `client-brief.md`** for sender name, sender title, client name, mailing address (if Sequence 2 needed).
- **Ask ONLY for what's still missing**:
  1. Sender first name (if not in message or brief)
  2. Sender title (if not in message or brief)
  3. Target recipient persona (if not in message)
  4. Email type (default Type 1, confirm in one line)
  5. Sequence depth (default 1; "Sequence 1 only, or Sequence 1 + Sequence 2 follow-up?")
  6. Mailing address (only if Sequence 2 requested and not in brief)
- **Never ship placeholder brackets**: `[Name]`, `[Title]`, `[role]`, `[First name]`, `[Address]`, `[Sender full name]`, etc. Scan the final copy for any `[...]` that isn't a `{{...}}` variable. If any found, ask for the missing value and rewrite.
- **Output as separate markdown artifacts**:
  - One artifact per sequence (artifact per email, not one combined artifact)
  - Sequence 1: subject line + blank line + body
  - Sequence 2: body only (no subject, continues thread) + signature block + compliance line
  - No commentary, no "here's your email" prose, just the artifacts
- Use spintax on greetings, action verbs, and closings (aim for 4-8 `{{RANDOM}}` blocks in Sequence 1).
- Include personalization variables: `{{firstName}}`, `{{companyName}}`.
- Pull client-specific facts (value prop, proof points, credibility) from project knowledge. Never invent metrics.
