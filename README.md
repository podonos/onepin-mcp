# Onepin MCP

Client configuration for the **Onepin** MCP server — a hosted, remote Model Context
Protocol server that turns scripts into speech.

| | |
|---|---|
| Endpoint | `https://mcp.onepin.ai` (Streamable HTTP) |
| Authentication | OAuth 2.0 — browser sign-in, no API key to copy |
| Registry entry | [`ai.onepin/onepin`](https://registry.modelcontextprotocol.io/v0/servers?search=ai.onepin) |
| Documentation | https://docs.onepin.ai |

An Onepin account is required. The server is hosted by Podonos; this repository
contains only the client-side configuration, not the server implementation.

## What it does

Onepin workflows are graphs of nodes that normalize text, synthesize audio, translate,
and validate the result. Through MCP you can:

- Browse workflows and templates, and build a new workflow from a template or from intent
- Search voices and preview them before committing to one
- Run a workflow over a script and poll the run to completion
- Fetch the resulting audio, per line or as a download
- Check credit balance and usage

Runs are asynchronous: start one, then poll it. Every result is a plain audio file on a
presigned URL.

## Install

### Cursor

Install from the Cursor Marketplace, or add the server manually:

```json
{
  "mcpServers": {
    "onepin": {
      "url": "https://mcp.onepin.ai"
    }
  }
}
```

### Gemini CLI

```bash
gemini extensions install https://github.com/podonos/onepin-mcp
```

Then authenticate when prompted, or run `/mcp auth onepin`.

### Claude

Add `https://mcp.onepin.ai` as a custom connector.

Note that Onepin serves a **second, reduced surface** at `https://mcp.onepin.ai/studio`,
covering everything up to the point audio is generated. It has its own OAuth resource
identifier and is the URL used for directory submissions — see
[docs/surfaces.md](docs/surfaces.md). The full surface above is the one to add by hand.

### Any other MCP client

Point the client at `https://mcp.onepin.ai` using the Streamable HTTP transport. The
server advertises its authorization server via
[RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728)
(`/.well-known/oauth-protected-resource`), so clients that support OAuth discovery and
dynamic client registration need no further configuration.

## Repository contents

```
server.json                   Official MCP Registry entry (ai.onepin/onepin)
mcp.json                      Cursor — MCP server definition
.cursor-plugin/plugin.json    Cursor — plugin manifest
gemini-extension.json         Gemini CLI — extension manifest
assets/                       Marketplace logo
docs/mcp-registry.md          How the registry entry is published
```

`server.json` is the entry published to the official MCP Registry as
`ai.onepin/onepin`; CI publishes it when its `version` changes. The namespace proof
behind that name — a TXT record on the `onepin.ai` apex — lives in the Onepin backend
repository's Terraform, not here. See [docs/mcp-registry.md](docs/mcp-registry.md).

## License

MIT — see [LICENSE](LICENSE). This covers the configuration files in this repository
only; use of the Onepin service itself is governed by its own terms.

The Onepin name and logo are trademarks of Podonos and are not licensed under the MIT
License.

## Support

- Documentation — https://docs.onepin.ai
- Issues with this configuration — this repository's issue tracker
- Account, billing, and service questions — https://onepin.ai
