# Assets

| File | Used by | Spec |
|---|---|---|
| `logo.png` | Cursor Marketplace, Cline MCP Marketplace | 400×400 PNG — one file satisfies Cursor's logo field and Cline's exact-size rule |
| `logo.svg` | source | The 400×400 composition the PNG is rendered from |

Both are the Onepin pin mark from `https://onepin.ai/icon.svg`, in the treatment the
brand already uses for an icon that has to sit on an unknown background
(`https://onepin.ai/apple-icon.png`): pure white mark on a `#1A1A1A` tile, ~11% padding
per side. A PNG cannot carry that source SVG's `prefers-color-scheme` fill switch, so the
colour is fixed here rather than left to the viewer's theme.

## MCP clients and directories do not read these files

`server.json`'s `icons` and the backend's `serverInfo.icons`
(`src/donut/api/mcp/server.py`) both point at **`https://onepin.ai/onepin-mark-on-light.png`**
and **`…-on-dark.png`** — the live site's assets (`podonos/donut-landing` → `public/`), not
anything in this directory. Two of them, because those are transparent single-colour marks
and neither colour is readable on both backgrounds; `theme` tells the consumer which to
use. Note this is the opposite trade from the files above: they carry an opaque tile so one
file works anywhere, which is right for a marketplace thumbnail and wrong for a listing
card, where the tile shows as a black box on a light surface. The spec asks consumers to require an
icon URL on the server's own domain, and `raw.githubusercontent.com` is not it, so there is
nowhere here to point them.

The consequence is a drift seam worth knowing before you touch the mark: **editing
`logo.svg` and regenerating `logo.png` changes the Cursor and Cline listings and nothing
else.** An MCP client or a directory keeps showing whatever the website serves. Changing
the mark everywhere means regenerating the two files in `podonos/donut-landing` under
`public/` as well — its `public/README.md` is the other half of this note.

Regenerate after editing `logo.svg`:

```bash
qlmanage -t -s 1600 -o . logo.svg
python3 -c "from PIL import Image; Image.open('logo.svg.png').convert('RGB').resize((400,400), Image.LANCZOS).save('logo.png', optimize=True)"
rm logo.svg.png
```

The Onepin name and logo are trademarks of Podonos and are not licensed under this
repository's MIT License.
