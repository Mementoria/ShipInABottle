# H.M.S. Zephyrus

A voxel ship in a bottle, animated in the browser. A three-masted ship rides a living sea beside a lighthouse island while gulls circle, a dolphin jumps, and something purple surfaces now and then. It doubles as a desk clock and, where the page is allowed to reach the network, mirrors the real weather where you are.

Live at **https://mementoria.github.io/ShipInABottle/**

Everything is a single file, [`index.html`](index.html), with Three.js loaded from cdnjs. There is no build step. Open the file, or serve the folder with any static server.

## Controls

| Control | Action |
| --- | --- |
| Drag | Take the camera. The tour resumes after twelve seconds idle. |
| Scroll or pinch | Zoom |
| Weather pills | Calm, Breeze, Fog, Rain, Storm, Snow, or Live |
| Light pills | Auto follows the clock through sunrise and sunset. Day and Night force it. |
| `W` | Next weather |
| `1` to `6` | Pick a weather |
| `L` | Live weather |
| `N` | Cycle Auto, Day, Night |
| `T` | Resume the tour |
| `Space` | Pause |

Weather and light are separate layers, so a snowy night or a foggy dawn both work. Transitions blend over a few seconds.

## Live weather

The Live pill appears when the page can fetch. It uses [Open-Meteo](https://open-meteo.com/), which needs no API key. On first use, choose **Use my location** for a one-time browser prompt, or type a city. Your choice is kept in this browser's local storage and sent only to Open-Meteo with each forecast request, refreshed every fifteen minutes.

The current conditions map onto the scene: thunderstorms become Storm, snow codes become Snow, rain and drizzle become Rain, fog codes become Fog, and clear skies become Calm or Breeze depending on wind. Wind speed then scales the swell and the flags, and cloud cover sets how many clouds drift through. Sunrise and sunset from the same forecast drive the Auto light. Pick any other weather pill to override, and Live to hand control back.

Click the temperature to switch between Celsius and Fahrenheit. Click the place name to change location.

If the page is opened somewhere that blocks network requests, the Live pill stays hidden and the six manual weathers still work.

## Scene notes

- The sea is a grid of voxel columns shaped to the bottle's profile, from the rounded end into the neck, with wave height and foam driving the colour.
- The ship bobs, pitches, and rolls with the wave under it. Sails bulge with the wind, flags flap, and the cabin windows light up at night.
- The glass is a small fresnel shader rendered back face first, then front, over the interior.
- Wave phase is integrated per frame rather than computed from elapsed time, so changing the tempo never jumps the animation.
