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

### Any other MCP client

Point the client at `https://mcp.onepin.ai` using the Streamable HTTP transport. The
server advertises its authorization server via
[RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728)
(`/.well-known/oauth-protected-resource`), so clients that support OAuth discovery and
dynamic client registration need no further configuration.

## Repository contents

```
mcp.json                      Cursor — MCP server definition
.cursor-plugin/plugin.json    Cursor — plugin manifest
gemini-extension.json         Gemini CLI — extension manifest
assets/                       Marketplace logo
```

The registry metadata (`server.json` for `ai.onepin/onepin`) is published from the
Onepin backend repository, which is the single source of truth for it. It is
deliberately not duplicated here.

## Support

- Documentation — https://docs.onepin.ai
- Issues with this configuration — this repository's issue tracker
- Account, billing, and service questions — https://onepin.ai
