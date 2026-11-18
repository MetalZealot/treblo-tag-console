# Sonauto Tag Console

A single-page tag console for building Sonauto style prompts from the Treblo v3 style tags. Everything lives in `index.html`; there is no build step.

## Use it

- Open `index.html` in a browser, or
- Turn on GitHub Pages (Settings → Pages → Deploy from branch → `main`, `/ (root)`) and open the Pages URL.

## Known limit

The **Auto-tag** button calls Claude directly from the page. That only works when the page runs inside claude.ai, which supplies the sign-in. Hosted anywhere else (GitHub Pages, a local file), Auto-tag will fail; the rest of the console works.
