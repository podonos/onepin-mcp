# Publishing the registry entry

The [official MCP Registry](https://registry.modelcontextprotocol.io) is a metadata
index: it stores a `server.json` describing where our server lives, and clients read it
to offer Onepin as an installable connector. It hosts no code and runs nothing.

Ours is a **remote** server, so there is no package to publish anywhere — none of the
npm/PyPI steps in the official quickstart apply. What gets published is one URL and the
name we are allowed to publish it under.

| | |
|---|---|
| Registry name | `ai.onepin/onepin` |
| Endpoint | `https://mcp.onepin.ai` (streamable-http) |
| Metadata | [`server.json`](../server.json) at the repo root |
| Listing mark | `https://onepin.ai/onepin-icon-on-{dark,light}.png` — served by `podonos/donut-landing`, **not** [`assets/`](../assets/README.md) |
| Namespace proof | apex `onepin.ai` TXT record — **in the backend repo's Terraform** |
| First published | 2026-10-08 |

## The name is a claim about a domain

`name` is reverse-DNS and the registry only accepts one whose namespace you can prove you
own. `ai.onepin` is `onepin.ai` reversed, so publishing under it means proving control of
`onepin.ai` via a TXT record on its **apex**.

That record is **not** managed here. It lives in the backend repository's prod Terraform
(`local.apex_txt_records`), together with the SPF and Google site-verification values that
share the same Route 53 record set. Two consequences worth knowing before you touch DNS:

- **Never add or edit the apex TXT set in the console.** Route 53 stores all TXT values for
  one name as a single record set, so a console write either loses a sibling value or is
  itself erased by the next `terraform apply`. If the lost value is the SPF line, mail
  deliverability degrades with nothing failing loudly.
- **Nothing visible breaks if the proof is deleted.** The listing stays up and connectors
  keep working — the registry verified the namespace at publish time and does not re-check.
  The failure appears at the next publish, which is refused. That delay is the whole
  argument for keeping the record in Terraform: the cost is paid by whoever is shipping,
  long after whoever removed it has forgotten.

The procedure for rotating the key or re-establishing the proof is in the backend repo's
`docs/mcp-registry.md`.

## Updating the listing

Published version metadata is immutable; an edit to `server.json` is a **new version**.

`server.json`'s `version` is deliberately **not** the Onepin app version. The entry
describes a URL, not a build — tying it to the backend's release number would mean
republishing identical metadata on every deploy. Bump it only when `server.json` itself
changes.

```
edit server.json  +  bump "version"  →  merge to main  →  CI publishes
```

`.github/workflows/publish-mcp-registry.yml` runs on a push to `main` that touches
`server.json`. It reads the registry's current latest version first and exits as a clean
no-op when it already matches, so a re-run — or a commit that changes the file without
bumping the version — is harmless rather than a failure. `workflow_dispatch` is there for
re-running after fixing a transient failure.

Changing the **name** is not an update: it publishes a second entry, and the old one has
to be deprecated with `mcp-publisher status --status deprecated`. The workflow pins the
name for that reason and will fail rather than publish a rename.

### Two prerequisites, both outside this repo's code

- A GitHub Environment named **`mcp-registry`** with required reviewers. The key it holds
  authorizes publishing **any** server under `ai.onepin`, not just this one, so the review
  is the thing standing between repo write access and our namespace.
- An environment secret **`MCP_PRIVATE_KEY`** holding the Ed25519 private key (64 hex
  chars) from 1Password.

CI authenticates with DNS, not `login github-oidc`: OIDC grants `io.github.*` namespaces
and ours is domain-based.

## Publishing by hand

Only needed if CI is unavailable. The key is in 1Password; do not write it to a file in a
working tree.

```bash
brew install mcp-publisher

mcp-publisher login dns --domain onepin.ai --private-key "<from 1Password>"
mcp-publisher validate server.json
mcp-publisher publish server.json

curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=ai.onepin/onepin"
```

The registry API is slow and intermittently times out (measured 9–24 s, with outright
failures). Retry before concluding anything is wrong — this is why the workflow's reads
pass `--retry`.

## Why the entry lives in this repository

The entry is distribution metadata with the same lifetime as the client manifests beside
it — it changes a few times a year, decoupled from the backend's release cadence. Keeping
it here also means the one automatic trigger that makes sense (`push` on `server.json`)
is available: in the backend repo, every production release creates a `vX.Y.Z` tag, so the
tag trigger the registry's own Actions guide suggests would attempt a publish per release
and fail on the duplicate version.

What that split costs is that the URL in `server.json` is now stated in a repository that
does not define it. `.github/workflows/check-endpoint.yml` is the answer: it asks the
running service what resource identifier it advertises (RFC 9728) and requires every
manifest here to agree, on a daily schedule — because the event it guards against
happens in another repository and would never trigger a push-only check.
