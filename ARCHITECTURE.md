# Architecture

## Bird's-eye view

A single static page. Treblo's style tags are embedded in it as JSON. People browse and filter the tags, collect two lists ("use" and "avoid"), arrange each list in the order they want, and copy each list as comma-separated text. The page has no backend, no build step, and no network calls apart from Google Fonts. The two lists are saved in the browser's `localStorage`, so they survive a refresh; nothing leaves the device.

## Quality goals

1. **Opens anywhere.** It works as a local file and on GitHub Pages without changes.
2. **Fast on a phone.** All 4,160 tags are searched in memory, the list renders at most 600 rows at a time, and picking a tag repaints only that row. A tap should respond within 100 ms.
3. **Tags are exact.** The page only ever copies strings from the embedded vocabulary.
4. **Accessible.** Text meets WCAG AA contrast (colours are tokens on `:root`), every control has a name and a visible focus ring, and keyboard focus survives a pick.

## Code map

| File | Role |
|---|---|
| `index.html` | Styles, markup, the tag data (`<script type="application/json" id="data">`) and the app script, in that order |
| `favicon.svg`, `og.png` | The tab icon and the image shown when the link is shared |
| `screenshots/` | The README images (desktop at 1280×800, phone at 390×844) |

The data blob holds `cats`, `fams`, `subs`, `regs` and `tiers` (lookup tables), `tags` (rows of `[name, rank, tier, cat, fam, sub, reg]`, where rank is `null` for tags that are hidden from browsing) and `rel` (per tag, related tag indexes, strongest first). The Suggested pane uses `rel`.

## Invariants

- **A tag is in at most one list, "use" or "avoid".** If a tag were in both, the two copied lists would contradict each other.
- **A list's order is the user's order.** Tags stay in the order they were picked until the user moves them (drag, the move arrows, the arrow keys) or taps "Sort by popularity". The chips, the copied text and the saved list all use that one order. If any of them re-sorted on its own, a list someone arranged would be copied or reloaded in a different order without them noticing.
- **Only tags with a rank are shown, in Browse and in Suggested.** The 165 unranked tags are outside the 4,160 Treblo lists, and many have broken text (`forr\xf3`). Showing them would let people copy tags Treblo doesn't know.
- **Saved lists hold tag names, not row indexes, and only ranked names are restored.** Indexes shift if the data is refreshed, which would silently swap people's tags.
- **There is one filter panel.** On phones the same element moves into a `<dialog>` and back, and Back closes it. A second copy would let the two drift out of sync.
- **On phones, Copy tags has the same box with the tray open or closed** (bottom-right, half the width). The open/close animation grows the tray upward from its bottom edge, so if that button moved, it would jump mid-animation. The tray floats over the list, and the list keeps room only for the closed bar. If opening the tray resized the list instead, every open would re-lay out the whole list and the tap would lag. The tray's spacing and corner come from three variables on `.tray` (`--tray-pad`, `--tray-text`, `--tray-r`), so a one-off value there breaks the alignment.
- **The data line stays a single line in `index.html`.** Edits to the page work around it, and it is roughly 340 KB.
- **`og:url` and `og:image` are absolute Pages URLs.** If the repo is renamed or moved, link previews break until they're updated.
