# La Divina Commedia — Interactive Reader

A single-page reader for the complete text of Dante's *Divine Comedy* (Inferno, Purgatorio, Paradiso) in the original Italian.

**Live:** https://ladivinacommedia.thisisfed.xyz/

## Features

- Full text of all three cantiche, navigable by canto via a fixed side index
- **Scrollspy** highlights the active canto as you scroll; click-to-scroll briefly suppresses the spy so the smooth-scroll animation isn't fought on the way down
- **Famous verses** highlight on click from the index — *selva oscura*, *amor che a nullo amato amar perdona*, *Galeotto fu 'l libro*, and others
- **Focus mode** softly vignettes the top and bottom of the viewport for distraction-free reading
- **Auto day/night theme** computed from estimated sunrise/sunset at the reader's location — no geolocation prompt, no third-party calls. Re-runs every minute so the theme flips live across dusk or midnight in long sessions
- **Reading position memory** at tercet-level precision via `localStorage` — survives layout reflow, resize, and tab close. Falls back to canto anchor if tercet count drifts
- Responsive down to mobile widths
- SEO with Open Graph, Twitter Card, and Schema.org `Book` structured data

## Technical notes worth flagging

**Sunrise/sunset without geolocation.** Longitude is inferred from the *standard* (non-DST) timezone offset — taking `Math.max(January, July)` of `getTimezoneOffset()` so BST readers aren't placed at 15°E. Latitude is bucketed by IANA zone region. Accurate enough for a theme threshold and never prompts the user.

**Julian Date rounding.** `Math.round` (not `floor`) for the day-number, since JD rolls over at noon UTC. Without it, morning readers would inherit the previous day's sunset and stay in night theme all morning.

**Tercet-indexed position storage.** `<span class="riga">` markers are stable structural anchors (one per stanza, in document order), so position restoration survives layout changes. Storage payload is versioned with a legacy-string fallback.

## Stack

Plain HTML, CSS, and vanilla JavaScript. No frameworks, no build step, no dependencies — drop it on any static host or open it in a browser.

Originally written with jQuery 3.4.1 and later refactored to vanilla JS. A few small bits worth noting from that refactor:

- **Smooth scroll** uses a custom `smoothScrollTo` helper (rAF + easeInOutQuad) instead of `window.scrollTo({behavior: 'smooth'})` so the 1100ms timing of the original is preserved exactly. The helper cancels any in-flight animation on the same element, mirroring jQuery's `.stop().animate()` chain that the scrollspy relied on.
- **Anchor targets** use `getElementById` rather than `querySelector` because many IDs in the document start with a digit (`#1uno`, `#2tre`), which CSS selector syntax doesn't allow unescaped. jQuery papered over this; vanilla doesn't.

## Running locally

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## License

MIT — text of the *Commedia* is public domain.
