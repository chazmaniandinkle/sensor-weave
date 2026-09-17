# Sensor Weave

**[Open it →](https://chazmaniandinkle.github.io/sensor-weave/)**

An optical mouse can't track on a phone screen. The glass is too flat, the backlight washes out the sensor's own LED, and a still image gives it nothing to lock onto.

Sensor Weave puts a high-contrast tiled pattern on the screen and moves it. The mouse sees texture sliding underneath it and reports motion, so the computer it's plugged into stays awake and un-idle without anyone touching anything.

No account, no install required, no network after first load. One page, one canvas.

## Use it

1. Open the page on the phone.
2. Screen brightness to max, and **turn auto-brightness off** — no web API can do this, and a phone that dims mid-run will drop the sensor.
3. Press **Start**. The page goes fullscreen, takes a screen wake lock, and hides its own controls.
4. Lay the mouse face-down on the glass.
5. Tap the pattern to bring the controls back.

If the sensor won't lock on: shrink **Tile** first, then try **Invert**. Cheap LED sensors are picky about polarity against glass, and one of the two usually just works.

## Controls

| Control | What it does |
|---|---|
| **Path** | `Orbit` — circle. `Figure-8` — lemniscate. `Drift` — straight line at a fixed heading, wrapping modulo the tile. Orbit and Figure-8 loop back on themselves, so the pattern never parks against a screen edge. |
| **Speed** | Pattern velocity in CSS px/s. Slower is steadier; too fast and a cheap sensor loses the correlation between frames. |
| **Radius** | Size of the Orbit / Figure-8 path. |
| **Heading** | Direction of travel in Drift mode. |
| **Tile** | Size of one pattern cell. This is the one that matters most — it sets the spatial frequency the sensor is trying to resolve. |
| **Invert** | Swaps black and white. |

Every setting persists in `localStorage`.

## The pattern

One tile is drawn on an offscreen canvas and repeated with `createPattern`. It carries several spatial frequencies at once, so a sensor that can't resolve the fine hatching still has the concentric squares to work with, and vice versa:

- four quadrant trapezoids of diagonal hatching, with the direction alternating quadrant to quadrant, so no single orientation dominates
- a stack of concentric squares alternating 0° and 45°, each a different size
- a centre circle and crosshair
- diamonds straddling the tile corners, which give the seams their own features

Animation is translation only — the tile itself never changes between frames, so there's no per-frame redraw cost beyond a single `fillRect`.

## Notes

- **Wake lock** comes from the Screen Wake Lock API, re-acquired on `visibilitychange` and `focus`. The status line reads `awake` when it's held, `dimming` when the browser supports it but it isn't held, and `no wake lock` where the API is missing (notably older iOS).
- **Fullscreen** is requested on Start, which also kicks in the safe-area insets so nothing sits under a notch.
- **Installable** as a PWA — manifest plus a cache-first service worker, so it runs with the phone fully offline.
- **No** brightness API exists on the web. That step stays manual.

## Layout

```
index.html               the whole app — markup, styles, logic
manifest.webmanifest     PWA manifest
sw.js                    cache-first service worker (offline shell)
icons/                   192 / 512 / maskable-512, generated from the pattern itself
```

Served straight from GitHub Pages off `main`. No build step.

## License

MIT
