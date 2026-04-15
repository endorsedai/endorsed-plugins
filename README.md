# Endorsed AI — Claude Plugins

Internal marketplace of Claude plugins used at Endorsed AI for cold email copy generation and client onboarding.

## Plugins

| Name | Version | Description |
| --- | --- | --- |
| `client-brief-generator` | 0.1.4 | Generates a standardized Email Draft Doc (`client-brief.md`) from whatever client materials are available (website, decks, case studies, all optional). Gaps are flagged in the brief for later refresh. |
| `cold-email-generator` | 0.3.1 | Generates and refreshes cold email copy for instantly.ai campaigns. Reads from `client-brief.md` when present. |

The two plugins pair: **`client-brief-generator` produces the menu, `cold-email-generator` assembles the meal.**

---

## Installation

### For Claude.ai Team plan (Cowork admin) — recommended

1. Clone this repo locally
2. Zip each plugin directory from *inside* (so `plugin.json` ends up at the zip root):
   ```bash
   cd plugins/client-brief-generator
   zip -r ../../client-brief-generator-v0.1.0.zip . -x "*.DS_Store"
   cd ../cold-email-generator
   zip -r ../../cold-email-generator-v0.3.0.zip . -x "*.DS_Store"
   ```
3. In Claude.ai → **Admin → Plugins → Upload new plugin** — upload both zips
4. Set each to **Required** (force-installed for all team members)

### For Claude Code users (individual install)

```
/plugin marketplace add endorsedai/endorsed-plugins
/plugin install client-brief-generator@endorsed-plugins
/plugin install cold-email-generator@endorsed-plugins
```

### For Claude Code org-wide (managed settings)

Configure in the Claude.ai admin console under **Admin Settings → Claude Code → Managed settings**:

```json
{
  "extraKnownMarketplaces": {
    "endorsed-plugins": {
      "source": { "source": "github", "repo": "endorsedai/endorsed-plugins" }
    }
  },
  "enabledPlugins": {
    "client-brief-generator@endorsed-plugins": true,
    "cold-email-generator@endorsed-plugins": true
  }
}
```

---

## Prerequisite: Apify MCP connector (for `client-brief-generator`)

The `client-brief-generator` skill scrapes client websites via Apify. You must set this up **once** in the Cowork admin before using the skill. Endorsed AI is already an Apify customer (used by `endorsedai-ads-intelligence`), so this reuses the existing account — no new vendor, no new billing.

### Setup steps

1. In the Apify console → **Settings → API & Integrations** → copy your personal API token (or generate a shared org token)
2. In Claude.ai → **Admin → Connectors → Add custom connector**
3. Configure:
   - **Name**: Apify
   - **Remote MCP URL**: `https://mcp.apify.com`
   - **Auth**: OAuth (browser redirect — recommended) **OR** `Authorization: Bearer <APIFY_TOKEN>` header
4. Save. Test from a chat: *"search Apify actors for 'website crawler' and list the top 3"*

### Cost estimate for your usage

Apify is true pay-as-you-go via your existing compute units:

| Task | Pages | Approx. cost |
|---|---|---|
| One new client brief (full crawl) | ~15 pages | **$0.003-0.08** (adaptive crawler) |
| All 45 clients backfill | ~675 pages | **$0.15-3.40** one-time |
| Monthly refresh of 5 clients | ~75 pages | **$0.02-0.40/month** |

Well under the $5/mo free tier if you're on the Apify free plan. Negligible addition to your paid plan.

### Actors this skill uses

- **`apify/rag-web-browser`** — single-page LLM-ready markdown fetcher. Used for both full briefs (6-8 parallel calls, one per key page) and section refreshes (one call).

The skill explicitly avoids `apify/website-content-crawler` because full-site crawls routinely exceed MCP's 60-second synchronous call window, which forces async mode and triggers permission issues. The multi-call pattern with `rag-web-browser` keeps every call under the timeout and parallelizes for speed.

### Fallback: no Apify

If Apify is unavailable, account managers can paste the client's website content manually as `website-content.md` into the Project. The skill will use that instead of scraping.

---

## Account Manager workflow — onboarding a new client

**Time**: ~20-30 minutes per client.

1. **Create a Claude Project**: "Client: `<Name>` — Outreach"
2. **Upload whatever materials you have** (all optional — run with what's available):
   - Client's sales deck (PDF) — strengthens value prop + case studies + credibility
   - Client's pitch deck (PDF) — strengthens credibility + company snapshot
   - Case study PDFs — strengthens Case Studies / ROI section
   - Prior winning emails — strengthens Full Email Examples
3. **Run `/brief-new`** in the Project chat:
   ```
   /brief-new MEDDICC meddicc.com
   ```
   The skill scrapes the website, reads whatever docs are uploaded, and produces a brief. Missing materials get flagged with `[GAP — upload X via /brief-refresh Y]` placeholders, not blocked.
4. **Review the generated `client-brief.md`**
   - Does every case study that exists name a specific client?
   - Does every ROI claim that exists have a specific number?
   - Does the tone match how the client actually talks?
   - Note the `[GAP — ...]` markers — these tell you exactly which docs would strengthen the brief
5. **Save `client-brief.md`** to the Project (or pin it in Project instructions)
6. **When more materials arrive later**, run `/brief-refresh <Client> <Section>` with the new file uploaded — only that section regenerates
7. Done — the Project is ready for cold-email-generator campaigns from day one, even with a partial brief.

### Updating an existing client brief

When a client updates their positioning, deck, or case studies:

1. Upload the new material to the Project
2. Run `/brief-refresh`:
   ```
   /brief-refresh MEDDICC Case Studies / ROI
   ```
3. Skill outputs the single updated section
4. Paste it in, replacing the old section in `client-brief.md`

---

## Folder convention inside each Project

```
📁 Claude Project: "Client: <Name> — Outreach"
├── 00-inputs/
│   ├── sales-deck.pdf
│   ├── pitch-deck.pdf
│   └── website-content.md       ← auto-generated by Firecrawl
├── 01-generated/
│   └── client-brief.md          ← the canonical brief
└── 02-campaigns/
    └── [per-campaign email drafts]
```

---

## Development

### Repo structure

```
endorsed-plugins/
├── .claude-plugin/
│   └── marketplace.json              # marketplace index
├── plugins/
│   ├── client-brief-generator/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── commands/
│   │   │   ├── brief-new.md
│   │   │   └── brief-refresh.md
│   │   └── skills/client-brief-generator/SKILL.md
│   └── cold-email-generator/
│       ├── .claude-plugin/plugin.json
│       ├── commands/
│       │   ├── copy-new.md
│       │   ├── copy-rewrite.md
│       │   └── copy-spintax.md
│       └── skills/cold-email-generator/SKILL.md
└── README.md
```

### Editing a plugin

1. Edit the SKILL.md or command file
2. Bump the `version` in both `marketplace.json` AND the plugin's `plugin.json`
3. Commit + push (auto-deploy from GitHub for Claude Code users)
4. For Cowork users: re-zip the updated plugin directory and re-upload in the admin console

### Versioning

- **Patch (0.3.0 → 0.3.1)**: typo/clarity fixes, no behavior change
- **Minor (0.3.0 → 0.4.0)**: new capability, backwards-compatible
- **Major (0.3.0 → 1.0.0)**: breaking schema changes (e.g., renaming sections in client-brief.md)

Keep both plugins on compatible versions. `cold-email-generator` v0.3.x expects `client-brief-generator` v0.1.x output.
