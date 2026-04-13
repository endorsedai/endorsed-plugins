# Endorsed AI — Claude Code Plugins

Internal marketplace of Claude Code plugins used at Endorsed AI.

## Installation

### Org-wide (managed settings)

Owners configure this in the Claude.ai admin console under **Admin Settings → Claude Code → Managed settings**:

```json
{
  "enabledPlugins": {
    "cold-email-generator@endorsed-plugins": true
  }
}
```

### Manual (individual install)

```
/plugin marketplace add endorsedai/endorsed-plugins
/plugin install cold-email-generator@endorsed-plugins
```

## Plugins

| Name | Description |
| --- | --- |
| `cold-email-generator` | Generates and refreshes cold email copy with spintax formatting for instantly.ai campaigns. |
