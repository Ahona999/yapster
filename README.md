# Yapster

A sign-in page for a social network that does not exist, with one defect: you cannot sign in. The button leaves before you reach it.

Everything else on the page works perfectly. That is what makes it annoying.

**Live:** https://ahona999.github.io/yapster/

## Running it

The page is a single self-contained `index.html` — no build step, no dependencies. Open it directly:

```bash
start index.html      # Windows
open index.html       # macOS
```

Or serve it over HTTP, which is closer to how it behaves when deployed:

```bash
node serve.js         # http://localhost:5173
node serve.js 8080    # pick your own port
```

`serve.js` is a ~40 line static file server using only Node's standard library.

## How the button escapes

Three separate escape routes, because there are three ways to press a button.

| Input | Handler | What happens |
|---|---|---|
| Mouse | `pointermove` on `window` | Watches for the cursor entering a 96px halo around the button and moves it *before* you arrive. The click never had a chance to happen. |
| Touch / stylus | `pointerdown` on the button | No hover to warn it, so it bails out on the press itself with `preventDefault()`. |
| Keyboard | `click` on the button | Tab to it, press Enter, and the handler fires a dodge and drops focus. |

When it first flees, the button freezes its own width and height, switches to `position: fixed`, and its former slot keeps that height — so the card's layout doesn't collapse the moment it runs off.

Landing spots aren't purely random. Random alone produces the occasional useless hop right back under your cursor, so `flee()` samples 24 candidate positions, scores each by distance from the cursor, and takes the roomiest — bailing early once a candidate clears 65% of the viewport's smaller dimension.

An attempt counter tracks near-misses, and the taunts escalate from "Nope." to "Yapster admires your persistence." at ten.

## Notes

- **Themes** — light and dark are both defined at the token level, covering all three viewer states: explicit `data-theme`, OS `prefers-color-scheme`, and the unstamped default.
- **Reduced motion** — `prefers-reduced-motion` removes the wobble and the pulse, but not the dodging. That's the whole page.
- **No data goes anywhere** — the form has no action and no network calls. `autocomplete="off"` is set on both fields so your browser doesn't offer to save a password to a fake website.

## Files

```
index.html                    the entire page — markup, styles, behaviour
serve.js                      static file server for local development
.github/workflows/deploy.yml  publishes to GitHub Pages on push to main
```
