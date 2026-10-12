# Assets

| File | Used by | Requirement |
|---|---|---|
| `logo.png` | Cursor Marketplace, Cline MCP Marketplace | 400×400 PNG — one file satisfies Cursor's logo field and Cline's exact-size rule |

**Not yet committed.** `.cursor-plugin/plugin.json` therefore carries no `logo` key:
Cursor's submission checklist requires every manifest path to resolve, so a field
pointing at a missing file fails validation. When `logo.png` lands, add it back:

```json
"logo": "assets/logo.png"
```

Marketplaces resolve relative paths against this repository's raw content URLs.
