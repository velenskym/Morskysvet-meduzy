# Jellyfish kiosk — ZOO Sea World Prague

A touchscreen application running beside the jellyfish tank at ZOO Mořský svět (Sea World Prague), the Czech Republic's only public marine aquarium.

**Live:** https://velenskym.github.io/Morskysvet-meduzy/
**Try it in a simulated kiosk screen:** https://velenskym.github.io/morskysvet-euac/

Plain HTML, CSS and JavaScript. No framework, no build step, no server. Hosted free on GitHub Pages, displayed by Chromium in kiosk mode on a small Linux box next to the glass.

---

## The exhibit

*Chrysaora plocamia* — the South American sea nettle. The application opens on the question the tank itself raises: jellyfish have been here longer than the dinosaurs, they survived all five mass extinctions, and while many species decline today, jellyfish are doing well. What does that say about the state of our oceans?

## Pages

| File | What it is |
|---|---|
| `index.html` | Home — hero and three cards |
| `druh.html` | The species in the tank: who it is, where it lives, how it is kept |
| `jednoduchost.html` | "Perfect simplicity" — body plan, movement, stinging cells (a nematocyst fires in ~700 ns at an acceleration of ~5 000 000 g), and a custom SVG of the life cycle from planula to ephyra to adult |
| `zahavci.html` | Jellyfish of the world, and an interactive phylogenetic tree of the Cnidaria — expandable groups with species counts, and a ★ on every species actually kept in our exhibition |

Both languages live in the same files: every text node carries `data-cs` and `data-en` attributes and one function swaps them. The visitor's choice is remembered in `localStorage`.

## Built for one specific screen

The application is authored at a fixed 1920×1080 layout and scaled with CSS to the tablet's 1366×768 panel. That is deliberate — there is exactly one screen to support, so there is no responsive CSS anywhere in the project and the result is pixel-predictable on the real device.

The practical consequence: **opening `index.html` on a laptop or a phone will not look right.** To see it as a visitor does, use the [kiosk simulator](https://velenskym.github.io/morskysvet-euac/), which frames the app at its native resolution and scales the whole frame to fit your screen.

## Running it locally

```bash
git clone https://github.com/velenskym/Morskysvet-meduzy.git
cd Morskysvet-meduzy
python3 -m http.server 8000
```

Then open http://localhost:8000 in a browser window sized to 1366×768 (in Chrome: DevTools → device toolbar → set a custom 1366×768 device).

## Reusing this for your own institution

You are welcome to. The code is MIT-licensed; the photographs, video and texts are not — see [LICENSE](LICENSE).

- **The machine underneath it** — Lubuntu with Openbox, autologin, Chromium kiosk flags, a watchdog and automatic power-off at closing time — is documented in [KIOSK-SETUP.md](https://github.com/velenskym/morskysvet-euac/blob/main/KIOSK-SETUP.md), including the mistakes that cost us the most time.
- **The design system** (colours, type scale, the 21 px minimum body size, the 3-minute inactivity reset, drag-scroll, local video instead of YouTube) is shared by all four of our kiosks and is described in the same document.
- The three sister applications: [octopus](https://github.com/velenskym/Morskysvet-chobotnice), [Great Barrier Reef](https://github.com/velenskym/Morskysvet-GBR), [Raja Ampat](https://github.com/velenskym/Morskysvet-kiosk).

## Contact

Mikuláš Velenský — Curator, ZOO Sea World Prague
velenskym@gmail.com · [morskysvet.cz](https://www.morskysvet.cz) · [github.com/velenskym](https://github.com/velenskym)

Shared with the EUAC community. If you build something from this, I would like to hear about it.
