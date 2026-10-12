# Two surfaces, two URLs

Onepin serves two MCP endpoints on the same hostname. They are different servers to a
client: each has its own RFC 9728 resource identifier, and a client that authenticates
against one cannot use the other's.

| URL | Surface | Where it is used |
|---|---|---|
| `https://mcp.onepin.ai` | **Full** — including audio generation | The registry entry (`server.json`), the Cursor and Gemini CLI manifests here, and the URL a user pastes by hand |
| `https://mcp.onepin.ai/studio` | **Reduced** — everything up to the point audio is generated: browse, author, clone, pick voices | Connector-directory submissions, Claude's in particular |

## Why a submission may not use the full URL

The registry entry publishes the full surface deliberately: listing the studio profile
there would advertise an Onepin that cannot generate audio.

A connector directory is the opposite case. Claude's directory is a separate submission
with its own review, and the studio profile exists for it. So **the URL on that form is
the `/studio` one**, not the root — picking the root because it is the URL in this repo's
manifests is the mistake this page exists to prevent.

Confirm before submitting anywhere, since each surface answers for itself:

```bash
curl -s https://mcp.onepin.ai/.well-known/oauth-protected-resource        | jq -r .resource
curl -s https://mcp.onepin.ai/.well-known/oauth-protected-resource/studio | jq -r .resource
```

Each returns its own URL. A surface whose metadata does not echo the URL you are about to
submit is the wrong URL.

The split itself — which tools each profile carries, and why the bare path resolves to the
full surface — is defined in the Onepin backend (`donut.api.mcp.profiles`) and documented
in its `docs/mcp-server.md`.
