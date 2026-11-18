# Architecture

## Bird's-eye view

A single static page. Treblo's style tags are embedded in it as JSON. People browse and filter the tags, collect two lists ("use" and "avoid"), and copy each list as comma-separated text. The page has no backend, no build step, no network calls apart from Google Fonts, and no saved state.

## Quality goals

1. **Opens anywhere.** It works as a local file and on GitHub Pages without changes.
2. **Fast on a phone.** All 4,160 tags are searched in memory, and the list renders at most 600 rows at a time.
3. **Tags are exact.** The page only ever copies strings from the embedded vocabulary.

## Code map

| File | Role |
|---|---|
| `index.html` | Styles, markup, the tag data (`<script type="application/json" id="data">`) and the app script, in that order |
| `favicon.svg`, `og.png` | The tab icon and the image shown when the link is shared |

The data blob holds `cats`, `fams`, `subs`, `regs` and `tiers` (lookup tables), `tags` (rows of `[name, rank, tier, cat, fam, sub, reg]`, where rank is `null` for tags that are hidden from browsing) and `rel` (per tag, related tag indexes, strongest first). The Suggested pane uses `rel`.

## Invariants

- **A tag is in at most one list, "use" or "avoid".** If a tag were in both, the two copied lists would contradict each other.
- **Only tags with a rank are shown, in Browse and in Suggested.** The 165 unranked tags are outside the 4,160 Treblo lists, and many have broken text (`forr\xf3`). Showing them would let people copy tags Treblo doesn't know.
- **The data line stays a single line in `index.html`.** Edits to the page work around it, and it is roughly 340 KB.
- **`og:url` and `og:image` are absolute Pages URLs.** If the repo is renamed or moved, link previews break until they're updated.
