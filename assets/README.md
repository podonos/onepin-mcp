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

Regenerate after editing `logo.svg`:

```bash
qlmanage -t -s 1600 -o . logo.svg
python3 -c "from PIL import Image; Image.open('logo.svg.png').convert('RGB').resize((400,400), Image.LANCZOS).save('logo.png', optimize=True)"
rm logo.svg.png
```

The Onepin name and logo are trademarks of Podonos and are not licensed under this
repository's MIT License.
