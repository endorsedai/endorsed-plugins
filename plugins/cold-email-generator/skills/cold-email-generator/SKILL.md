---
name: cold-email-generator
description: Generate cold email copy with spintax formatting for instantly.ai campaigns, or refresh existing copy to combat fatigue. Use whenever the user says "write a cold email", "draft cold email copy", "generate outreach for [company]", mentions instantly.ai campaigns, or asks for email copy with personalization variables. Also trigger when they want to "refresh copy", "add more spintax", "reword this email", "combat copy fatigue", "generate variations", or provide existing email copy to modify. Trigger for both new copy generation and existing copy enhancement.
---

# Cold Email Generator

## Overview

This skill produces cold email copy for instantly.ai campaigns. There are two modes:

1. **Generate new copy**: build fresh email copy from scratch using one of the 4 proven formats
2. **Refresh existing copy**: reword and add spintax variations to combat copy fatigue

Both modes use `{{RANDOM | option1 | option2 | option3}}` spintax and personalization variables like `{{firstName}}`, `{{companyName}}`.

---

## Required Inputs (pre-flight checklist)

Before generating copy, resolve these 5 inputs in this order. **Only ask for what's still missing after steps 1 and 2.**

### Step 1: Parse the user's message first

Read the user's input carefully. Extract any of these that were already provided:
- Client name (or infer from the Project name)
- Target persona / recipient role
- Email type (1, 2, 3, or 4)
- Sender first name
- Sender title
- Sequence depth (just 1, or 1+2)

Do NOT re-ask for anything the user already said. If they typed "write a cold email to a CRO at Linear", the persona is `CRO`. Don't ask again.

### Step 2: Check `client-brief.md`

Pull whatever's still missing from the brief's `## Company Snapshot`:
- Sender first name (from "Primary sender" field)
- Sender title (same field)
- Client name (from title of the brief)
- Mailing address (if Sequence 2 is requested)

### Step 3: Ask ONLY for what's still missing

Required (refuse to generate without):

1. **Sender first name** - only ask if missing from both user's message and brief
2. **Sender title** - only ask if missing from both user's message and brief
3. **Target recipient persona** - ask if missing (campaign-specific, rarely in brief)
4. **Email type** - default Type 1, confirm in one line: *"Using Type 1 (Personalisation-led) by default. Want Type 2 (Case Study-led), Type 3 (Bullet Point), or Type 4 (Condensed) instead?"*
5. **Sequence depth** - ask: *"Generate Sequence 1 only, or Sequence 1 + Sequence 2 follow-up? (Default: just 1)"*

If Sequence 2 is requested AND mailing address is not in the brief, ask for it in the same message: *"Sequence 2 needs the company mailing address for the signature block. What's the address?"*

### Pre-output validation

Before returning the final copy, scan for any bracket patterns and refuse to output if found:
- `[Name]` / `[First name]` / `[Sender]` / `[Full name]`
- `[Title]` / `[role]` / `[persona]`
- `[Address]` / `[Mailing address]`
- Any `[...]` that isn't an explicit spintax variable or personalization token (`{{firstName}}`, `{{companyName}}`, `{{RANDOM | ... }}`)

If any are found, ask the user for the missing value and rewrite before output.

### Inference shortcut

If the user provides structured input like `/copy-new Fertifa CRO Type 2 seq1+2 Jane "Head of Partnerships"`, parse all inputs and skip asking entirely.

---

## Client Context

This skill is designed to work inside a Claude Project that contains client-specific knowledge. The canonical knowledge file is **`client-brief.md`**, produced by the `client-brief-generator` skill.

**Always check for `client-brief.md` first.** If it exists, pull directly from its stable sections:

| Section in client-brief.md | Use for |
|---|---|
| `## Value Proposition` | Intro/value prop line. Pick the variant matching the campaign angle. |
| `## Case Studies / ROI` | Case study line. Pick the variant matching the prospect's likely pain. |
| `## Credibility Lines` | Credibility line. Pick the variant matching the email length. |
| `## Personalisation Openers` | Opening line. Pick the variant matching the prospect's role/signal. |
| `## Subject Lines` | Subject line options |
| `## Soft Closes` | Closing CTA |
| `## ICP & Personas` | Calibrate tone + angle to the target persona |

**If `client-brief.md` is missing**, ask the user to run `/brief-new` first via the `client-brief-generator` skill. Only proceed without it if the user explicitly confirms they want a one-off email using raw deck/website content.

**Fallback sources** (when `client-brief.md` is absent AND user confirms to proceed):
- Project instructions (client name, ICP, tone, key angles)
- Website scrape or summary
- PDFs/decks (pitch decks, case studies, one-pagers)
- Winning copy examples (optional)

**Do NOT fabricate proof points or metrics.** If they're not in the brief or project knowledge, ask the user before proceeding.

---

## Mode Decision Tree

**Generate new copy** when the user gives campaign details but no existing email body.

**Refresh existing copy** when the user pastes an existing email and wants it improved, spintaxed, or reworded.

---

## The 4 Email Types

Choose the email type based on what assets the client has and what would work best for the campaign. When unsure, default to Type 1.

---

### Type 1: Personalisation → Intro/Value Prop → Case Study/ROI → Credibility → Soft Close

**Best for:** Most campaigns. Use when the client has a strong case study and credibility.

**Structure:**

**Line 1 - Personalisation**
Reference the prospect's specific role or context at their company. This shows you know who they are and why you're reaching out to them specifically.

```
{{RANDOM | Hi | Hey | Hello}} {{firstName}}, as you're {{RANDOM | leading | heading up | running}} [their role/function] at {{companyName}} I wanted to {{RANDOM | touch base | reach out | get in touch}}.
```

**Line 2 - Intro + Value Proposition**
One sentence on who you are and what you do. Be succinct and specific. Use the client's own language, not generic marketing speak. Name the company and describe the outcome they deliver.

```
I'm [Name], [title] of [Company]. [One clear sentence on what you do and who you help.]
```

**Line 3 - Case Study / ROI**
Named client example with specific numbers. This is the most important line. It makes the claim real. Always use actual figures from project knowledge.

```
[Case study sentence with client name and specific numerical result.]
```

**Line 4 - Credibility**
Why should they trust you? Background, investors, scale, reviews, team credentials. Keep it to one sentence.

```
[One credibility sentence. Team background, notable clients, investors, or scale metric.]
```

**Line 5 - Soft Close**
One simple question. Don't oversell, just invite a conversation.

```
{{RANDOM | Open to a quick chat? | Worth a conversation? | Would you be open to more info or a chat?}}
```

**Signature**
```
{{RANDOM | Thanks | Best | Cheers}},
[First name]
```

**Full example (Endorsed AI):**
```
Hey {{firstName}}, as you're leading GTM at {{companyName}} I wanted to touch base.

I'm Angus, founder of Endorsed AI. We design and build fully automated revenue generation systems for more than 100 B2B technology companies using the top AI and outreach tools.

For YC-backed Solidroad, our outbound campaigns currently drive 30% of total revenue (over $1m in total sales) and have directly supported a $6.5m Seed Round (led by First Round Capital) through creating a predictable revenue generation engine.

We're now 3 years strong, our team has held leadership roles across Gartner and startup incubators and we operate outbound systems for 100+ tech companies.

Open to a chat?

Thanks,
Angus
```

---

### Type 2: Case Study Led (Multiple Case Studies)

**Best for:** Clients with several strong, varied case studies across different company stages or verticals. Works well when social proof across multiple names is more compelling than depth on one story.

**Structure:**

**Line 1 - Personalisation**
Same as Type 1.

**Line 2 - Intro + Value Proposition**
Brief intro. Keep it tight. The case studies are the star.

**Line 3 - Multiple Case Studies (bullet format)**
Lead with "A few recent client results:" then 3-4 bullet points, each with a named company and a specific metric. Vary the type of result (revenue, meetings, pipeline, etc.) to show range.

```
A few recent client results:

- [Client A]: [specific outcome with number]
- [Client B]: [specific outcome with number]
- [Client C]: [specific outcome with number]
- [Client D]: [specific outcome with number]
```

**Line 4 - Scale/Credibility**
One sentence showing overall scale or social proof.

**Line 5 - Soft Close**

**Full example (Endorsed AI, Case Study Led):**
```
Hello {{firstName}},

I'm Angus, founder of Endorsed AI. We build and run AI outbound revenue systems for over 100 B2B technology companies.

A few recent client results across various stages and markets:

- Solidroad: outbound now drives approximately 30% of total revenue (over $1 million closed) and supported a $6.5 million Seed round
- Flare: $1.8m+ closed from outbound and over 100 sales calls per month
- Trumpet: over 120 booked meetings and an end-to-end GTM system that automatically engages high-intent prospects
- Cosine: 200+ ICP meetings booked in four months, creating millions in pipeline

More than 80 of our clients are VC or PE-backed, with many using our revenue generation systems to fast forward to the next investment round.

Worth a conversation?

Cheers,
Angus
```

---

### Type 3: Bullet Point Format

**Best for:** Products with multiple clear, quantifiable benefits. Works well when the value is multi-dimensional and hard to capture in one case study line.

**Structure:**

**Line 1 - Personalisation/Greeting**
Can be a simple greeting without the role reference, or include it.

**Line 2 - Intro + Value Prop**
Introduce yourself and company, then set up the bullets with a transition like "Teams using [company] typically:" or "Here are some examples:".

**Line 3 - Benefit Bullets (3-5 bullets)**
Each bullet is a specific, quantified outcome or capability. Lead with the strongest ones.

**Line 4 - Credibility**
Named customers, awards, or scale metric.

**Line 5 - Soft Close**

**Full example (Trumpet, Bullet Point Led):**
```
Hey {{firstName}},

I'm Ollie the CRO at trumpet and have formerly led global sales teams at LinkedIn and Salesloft.

We support sales leaders to empower their reps to run complex deals from first call to close in a single buyer-facing Sales Room, so reps aren't chasing updates across emails, docs, and threads.

Teams using trumpet typically:
- Keep buyers in-sync with Mutual Action Plans
- Surface 5x additional hidden stakeholders in the deal cycle
- Cut sales cycles by up to 40% using real buyer engagement data
- Forecast with much more accuracy using our live intent data

We're used by teams at Gong, HubSpot, Cognism and Stripe, and were recently rated the #1 Digital Sales Room on G2 globally across all categories.

Open to a short chat to see whether this would fit how you run deals today?

{{RANDOM | Best | Thanks}},
Ollie
```

---

### Type 4: Condensed

**Best for:** A/B testing shorter copy, contacts who prefer brevity, or campaigns where Type 1 has fatigued. Keeps the core ROI and credibility but compresses everything into 3-4 tight sentences.

**Structure:**

Combine personalisation + intro into one sentence. Follow immediately with the case study. Add one credibility line. Soft close.

**Full example (Endorsed AI, Condensed):**
```
Hey {{firstName}}, I'm Angus, CEO of Endorsed AI. We build automated outbound systems for 100+ B2B tech companies.

For YC-backed Solidroad, outbound now drives 30% of total revenue ($1m+ in sales) and supported their $6.5m Seed round led by First Round Capital.

We're 3 years strong, with leadership experience across Gartner and startup incubators, and currently run outbound for over 25 Irish tech scale-ups.

Open to a chat?

Thanks,
Angus
```

**Full example (Cosine, Condensed):**
```
Hey {{firstName}}, I'm Yang, co-founder of Cosine an AI engineer backed by Sam Altman that automates software tasks, ships features, fixes bugs, and handles multiple tickets in parallel.

On OpenAI's SWE-Lancer benchmark, Cosine completed $155K of freelance engineering work for $600 in compute (250x ROI, 72% pass rate).

We're backed by Sam Altman and trusted by HP Enterprise, Comcast, and UiPath.

Open to a quick chat?

Best,
Yang
```

---

## Subject Lines

Subject lines should be simple and low-friction. Options:

**Introduction format (works well for most campaigns):**
```
{{firstName}} <> [SenderName] | Introduction
{{firstName}} <> [SenderName] | Intro and Invite
Introduction | {{firstName}} <> [SenderName]
```

**Curious/question format:**
```
{{RANDOM | Quick question, {{firstName}} | Question about {{companyName}} | Quick question}}
```

**Relevance format (use when personalisation hook is strong):**
```
{{companyName}} + [topic area]
Re: {{companyName}}
```

Keep subject lines simple. Don't oversell in the subject line. The goal is just to get the open.

---

## Personalisation Line Patterns

The opening line should always reference something specific about the prospect. Use the role/function from the campaign data. Examples by persona:

- **Founder/CEO:** "as you're leading {{companyName}}" / "as the founder of {{companyName}}"
- **CRO/VP Sales/Head of Sales:** "as you're leading the sales org at {{companyName}}" / "as you're heading up sales at {{companyName}}"
- **VP RevOps:** "as you're leading RevOps at {{companyName}}" / "given you're running revenue operations at {{companyName}}"
- **Head of Marketing:** "given you're leading marketing at {{companyName}}"
- **Engineering/CTO:** "as you're running the engineering team at {{companyName}}"
- **Finance/CFO:** "as you're responsible for finance at {{companyName}}"
- **Competitor/tool user:** "as your team is using [tool] at {{companyName}}"
- **Recent funding:** "Congrats on the recent growth at {{companyName}}. How are you currently structuring [area] post-raise?"

---

## Soft Close Patterns

Always use exactly ONE soft close. Choose based on tone:

- Open to a quick chat?
- Worth a conversation?
- Would you be open to more info or a chat?
- Open to a short chat to see whether this would fit?
- Would it make sense to connect?
- Worth a quick introduction?
- Does it make sense to explore this briefly?
- Would you be interested in seeing how this could support {{companyName}}?

---

## Spintax Guidelines

Use spintax purposefully to create natural variation, not to randomize everything.

**High-value places to add spintax:**
- Greeting: `{{RANDOM | Hi | Hey | Hello}}`
- Opening verb: `{{RANDOM | leading | heading up | running}}`
- Touch base phrase: `{{RANDOM | touch base | reach out | get in touch}}`
- Closing: `{{RANDOM | Thanks | Best | Cheers}}`
- Sender name (if they go by multiple): `{{RANDOM | Angus | Ang}}`

**Do NOT randomize:**
- Specific numbers or metrics
- Named clients or companies in case studies
- Personalization variables (`{{firstName}}`, `{{companyName}}`)
- The core value proposition meaning

Aim for 4-8 `{{RANDOM}}` blocks per email. More than that starts to feel forced. Read each variation aloud to check it flows naturally.

---

## Mode 2: Refresh Existing Copy

When the user provides existing copy to refresh:

1. **Identify** the current structure. Which of the 4 types does it resemble?
2. **Preserve** all specific numbers, client names, and personalization variables exactly
3. **Reword** phrase by phrase. Different verbs, sentence structures, transitions.
4. **Add spintax** to greetings, action verbs, connecting phrases, closings, and CTAs
5. **Output** the full refreshed version, no commentary

**What to preserve:**
- Specific numbers (e.g. "30%", "$1m", "200+")
- Named companies in proof points
- Personalization variables (`{{firstName}}`, `{{companyName}}`)
- The CTA intent
- The overall structure

**What to change:**
- Word choice and phrasing
- Sentence structure
- Greeting formality
- Action verbs
- Transition phrases

---

## Sequence 2: Follow-up Email

Sequence 2 is a short follow-up sent a few days after Sequence 1. It continues the same thread (no new subject line) and references the previous email. Keep it tight: one greeting + one follow-up prompt + sign-off + signature block + compliance line.

### Structure

1. Greeting (short spintax: Hey | Hi)
2. One-line follow-up prompt (5-variant spintax on "did you see the previous email")
3. Combined question (4-variant spintax on "worth a chat?")
4. Sign-off (3-variant spintax: Thanks | Cheers | Best)
5. Sender first name
6. Blank line, `--` separator, blank line
7. Full signature block:
   - Sender full name
   - Title (with spintax variants if provided, e.g., "CEO & Founder | Founder & CEO")
   - Client name (pipe-separated from title)
   - Mailing address (line below)
8. Compliance line: `Please respond with 'unsub' if you do not wish to receive any more emails`

### Template (Endorsed AI example, adapt content per client)

```
{{RANDOM | Hey | Hi}} {{firstName}},

{{RANDOM | Had a chance to review | Did you get a chance to look over | Were you able to review | Just checking if you had time to review | Wanted to see if you'd had a chance to look at}} the {{RANDOM | below | info below}} and {{RANDOM | worth a chat? | worth a quick chat? | open to a quick chat? | worth discussing?}}

{{RANDOM | Thanks | Cheers | Best}},
[Sender first name]

--
[Sender full name]
{{RANDOM | [Title variant 1] | [Title variant 2]}} | [Client name]
[Client mailing address]

Please respond with 'unsub' if you do not wish to receive any more emails
```

Always produce the sign-off name and signature block with actual values. Never ship `[Sender first name]`, `[Sender full name]`, `[Title variant]`, `[Client name]`, or `[Client mailing address]` placeholders in the final output.

### Title variant generation

When the user provides one title (e.g., "CEO & Founder"), generate a second natural variant for spintax (e.g., "Founder & CEO"). If the user provides two explicitly, use both. If the title has no natural reversal (e.g., "Head of Sales"), use it once without spintax on that position.

### No subject line in Sequence 2

Sequence 2 continues the Sequence 1 thread in instantly.ai. Do not output a subject line for Sequence 2.

---

## Output Format

Output email copy as **separate markdown artifacts in claude.ai**, one per sequence. This lets AMs copy each sequence directly into instantly.ai without parsing through prose.

### Per-sequence artifact format

For each sequence, create a dedicated artifact:

- **Artifact identifier**: `sequence-1-<client>` or `sequence-2-<client>` (lowercase, hyphenated)
- **Artifact title**: `Sequence 1 - <Client> - <Persona>` or `Sequence 2 - <Client>`
- **Content type**: `text/markdown`
- **Content structure**:
  - Sequence 1: subject line, blank line, email body (including sign-off)
  - Sequence 2: email body only (no subject line), including signature block and compliance line
  - No commentary, no labels like "Here's the email:", no explanations

### Ordering when generating multiple sequences

1. Sequence 1 first (its own artifact)
2. Sequence 2 second (its own artifact, only if the AM requested Sequence 1 + Sequence 2)
3. Never combine into one artifact. Separate artifacts make copy-paste into instantly.ai trivial.

### What NOT to include

- No meta-commentary ("Here's your email:", "Let me know if you want changes")
- No explanations of which type was used (that's implied by the artifact title)
- No prose before or after the artifacts

---

## Key Principles

**Keep it short.** Target 100-150 words. Anything longer loses readers.

**Specifics beat adjectives.** "Saved $500K" beats "saved a lot." "30% of total revenue" beats "significant revenue contribution." Numbers are the most persuasive element.

**Name-drop clients.** Named clients build credibility fast. One named company beats five unnamed ones.

**Sound human.** Write like the sender is a real person emailing a colleague, not a brand sending a blast.

**One CTA only.** Pick one soft close and stop. Multiple asks create paralysis.

**No em-dashes or en-dashes.** The characters that look like long dashes read as AI-generated. Use a plain hyphen `-`, comma, colon, or a new sentence instead. Zero exceptions. Apply this to every word of the output: subject line, opener, intro, case study, credibility, CTA, signature.

**No "unsubscribe."** If a compliance/opt-out line is needed, use: `{{RANDOM | Reply with '1' if you do not wish to receive any more emails | Please respond with '1' if you would prefer not to receive further emails | Respond with '1' to opt out of future emails}}`. Only add this if the client requests it.

---

## Passive Feedback Capture (Lean v1)

After generating output, observe the conversation for user edit signals. Do not interrupt, confirm, or mention logging mid-session.

### Signals to detect

- "remove / fix / change X"
- "make it more/less Y"
- "we don't say Z" / "not our voice"
- Regeneration requests with a correction
- User pastes back an edited version

### During session

- Store each detected signal in memory
- Track chronologically
- Do not log immediately

### At session end

Session ends when:
- User says "thanks", "done", "that's it", "good for now"
- 3+ minutes of inactivity after the last message
- User starts a different task (e.g., asks about a different client)

When session ends, make ONE GitHub MCP call to create an issue. Use the available GitHub `create_issue` tool (e.g., `endorsedai__create_issue`) against repo `endorsedai/endorsed-plugins`.

**Issue title:**
```
[session-log] <client-slug> <YYYY-MM-DD> <AM-first-name>
```

**Issue labels:**
```
type/session-log
skill/cold-email-generator
```

**Issue body:**
```markdown
## Summary
- Client: <client>
- AM: <AM>
- Skill: cold-email-generator
- Version: 0.4.2
- Final output materially changed: <YES/NO>

## Edit Log (chronological)
<numbered sequence: what was generated, what AM asked, what changed>

## Signals (quick scan)
- <short phrase per edit signal>

## Possible Patterns (low confidence)
- <inferred recurring themes, only if obvious; omit section if none>
```

### Definition of "materially changed"

YES when tone, structure, or facts changed. NO when only typos or single-word swaps.

### Rules

- Never log mid-session
- Never create more than one issue per session
- Keep Signals concise (short phrases, not sentences)
- Only include "Possible Patterns" if a theme is reasonably clear; omit the section otherwise
- If the user says "don't log this" at any point in the session, skip logging entirely
- Do not paste full email bodies into the log. Summarize what changed; avoid leaking client-specific content unnecessarily
- AM name: use the name from the MCP OAuth context if available, otherwise leave blank

### Disclosure (show once per Project)

On first generation in a new Project, add this one-line note AFTER the output (not before):

> *I log your edit patterns to improve these skills. Logs: github.com/endorsedai/endorsed-plugins/issues?label=type/session-log. Say "don't log this" anytime to skip.*

Do not repeat in subsequent generations in the same Project. Do not repeat across sessions in the same Project.
