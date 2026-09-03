# H.M.S. Zephyrus

A voxel ship in a bottle, animated in the browser. A three-masted ship rides a living sea beside a lighthouse island while gulls circle, a dolphin jumps, and something purple surfaces now and then. It doubles as a desk clock and, where the page is allowed to reach the network, mirrors the real weather where you are.

Live at **https://mementoria.github.io/ShipInABottle/**

Everything is a single file, [`index.html`](index.html), about 1,450 lines with Three.js loaded from cdnjs. There is no build step. Open the file, or serve the folder with any static server.

## Background

On September 1, 2026, Anthropic released Claude Fable 5.1 alongside Claude Mythos 5.1. The two are the same underlying model with different safeguards: Fable is generally available, Mythos is limited to vetted programs in cybersecurity and the life sciences. Anthropic priced Fable 5.1 at $10 per million input tokens and $50 per million output tokens, and cut cache reads by 75% to $0.25, which it said makes typical workloads about a quarter cheaper than Fable 5.

Launch week filled up with one-shot demos. One of them started as a comparison. On the afternoon of September 1, wick ([@holytrinity](https://x.com/holytrinity)) posted a ship in a bottle that GLM 5.3, running locally on eight RTX Blackwell 6000s, had produced from a single prompt. The prompt asked for an elaborate voxel-art scene of an intricate ship sailing an ocean inside a bottle, with waves and physics, colorful voxels, props and landscape, all inside the glass, built with WebGL in one HTML file that opens in Chrome. About an hour later, wick ran that same prompt through Claude Fable 5.1 and posted the result with the caption "Same prompt. Unbelievable." That second clip is the one with the pirate ship, the striped lighthouse, the weather toggle, and the touring camera you can grab.

- Original GLM 5.3 post with the prompt: [x.com/holytrinity/status/2094849933606064379](https://x.com/holytrinity/status/2094849933606064379)
- The Fable 5.1 result: [x.com/holytrinity/status/2094866061212459130](https://x.com/holytrinity/status/2094866061212459130)

Two days later the clip appeared in the Two Minute Papers episode [Claude Fable AI Is Much Stranger Than The Headlines Suggest](https://www.youtube.com/watch?v=B3LXEW4Pc-w), captioned "Made with Fable 5.1". Marc paused it there, took a screenshot, and started this project.

## From screenshot to app

The whole thing was built in a single Claude Code session with Claude Fable 5.1, working from that one screenshot and a few rounds of feedback. The screenshot was the only reference. Neither the original prompt nor wick's code was used; the prompt above was tracked down afterwards, for this README.

1. **One image, one request.** The screenshot showed a capsule-shaped bottle lying on a wooden voxel stand with a plaque reading H.M.S. Zephyrus, a pirate ship on a chunky voxel sea, a red-and-white lighthouse, clouds, gulls, a purple sea serpent, and two HUD cards: a weather toggle reading Calm, and a camera label reading "touring – drag to take control". The request was simply to make that app. The first build was already the shape of the final one: a small voxel builder that turns lists of blocks into instanced meshes, a sea made of columns shaped to the bottle's footprint and recolored by wave height, a ship that pitches and rolls with the wave under it, a fresnel glass shader drawn back-face first then front, a calm/storm toggle, a touring camera with drag to take over, and a plaque drawn to a canvas texture in Cinzel. It was checked in the in-app browser, the sea came out too washed out, and one color pass fixed that.

2. **Bigger ship, real weather, and two bugs.** The feedback round asked for more weather options, a larger and more detailed ship, a lighthouse that wasn't up in the clouds, and two fixes. The water stopped short of the rounded end and stuck out past the neck: the sea grid only covered the middle of the bottle, and the neck end had been approximated as a sphere instead of following the shoulder curve of the glass. The sea now spans the full length and follows the same profile the glass is lathed from, right into the neck. The other bug was subtler. Toggling weather made everything lurch faster for a few seconds, and it hadn't done that right after loading. The waves computed their phase as elapsed time multiplied by a speed factor, so easing the speed from one value to another swept the phase through a jump proportional to how long the page had been open. The fix integrates phase per frame instead, so a tempo change only changes the tempo. The ship doubled in length and picked up a copper bottom and boot-top stripe, gun ports, a quarterdeck and forecastle, gallery windows, deck cannons, barrels, a wheel, an anchor, a figurehead, crow's nests, bulging square sails with jibs and a spanker, and rigging lines. Weather became six presets blended over a couple of seconds. The camera stopped orbiting all the way round, because half of each lap was spent staring down the cork, and now sweeps across the front instead.

3. **Day and night, rain, and a clock.** Night had been one of the six weathers. It became its own layer with Auto, Day, and Night, so a snowy night and a foggy dawn both work, and Rain took the freed slot. A desk clock went in the bottom corner so the page could sit on a second monitor. This round also raised the question of live weather, which turned up a hard constraint: a page published as a claude.ai artifact runs under a content security policy that blocks every network request, so live data would need a normal web host.

4. **GitHub Pages and live conditions.** The project moved to a public repo under the Mementoria org with Pages serving the file as-is. Live weather uses Open-Meteo, which needs no API key. The page offers a one-time browser location prompt or a typed city, remembers the choice in local storage, and refreshes every fifteen minutes. Current conditions map onto the presets, wind scales the swell, cloud cover sets how many clouds drift through, and the forecast's sunrise and sunset drive the Auto light in the location's own time zone. The first end-to-end test was Reykjavik, overcast at 50°F.

5. **Pages becomes canonical.** With the site live, the artifact copy was retired from the README, and the clip's origin was traced by pulling the Two Minute Papers description with yt-dlp and checking each of its source posts.

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

The Live pill appears when the page can fetch. It uses [Open-Meteo](https://open-meteo.com/), which needs no API key. On first use, choose **Use my location** for a one-time browser prompt, or type a city. Your choice is kept in this browser's local storage and sent to Open-Meteo with each forecast request, refreshed every fifteen minutes. When you use the browser prompt, the coordinates are also sent once to [BigDataCloud](https://www.bigdatacloud.com/) to turn them into a place name, since Open-Meteo has no reverse lookup.

The current conditions map onto the scene: thunderstorms become Storm, snow codes become Snow, rain and drizzle become Rain, fog codes become Fog, and clear skies become Calm or Breeze depending on wind. Wind speed then scales the swell and the flags, and cloud cover sets how many clouds drift through. Sunrise and sunset from the same forecast drive the Auto light. Pick any other weather pill to override, and Live to hand control back.

Click the temperature to switch between Celsius and Fahrenheit. Click the place name to change location.

If the page is opened somewhere that blocks network requests, the Live pill stays hidden and the six manual weathers still work.

## Scene notes

- The sea is a grid of voxel columns shaped to the bottle's profile, from the rounded end into the neck, with wave height and foam driving the colour.
- The ship bobs, pitches, and rolls with the wave under it. Sails bulge with the wind, flags flap, and the cabin windows light up at night.
- The glass is a small fresnel shader rendered back face first, then front, over the interior.
- Wave phase is integrated per frame rather than computed from elapsed time, so changing the tempo never jumps the animation.

## Credits

- Original demo and prompt: wick, [@holytrinity](https://x.com/holytrinity)
- The clip that started it: [Two Minute Papers](https://www.youtube.com/watch?v=B3LXEW4Pc-w), Károly Zsolnai-Fehér
- Built by Marc with Claude Fable 5.1 in Claude Code
- Launch details: [Anthropic's announcement](https://www.anthropic.com/claude-fable-and-mythos-5-1)
- Weather data: [Open-Meteo](https://open-meteo.com/)

## Notice & Disclaimer

This project is an independent educational recreation and technical study inspired by the screenshot of wick's ([@holytrinity](https://x.com/holytrinity)) demo. It is shared strictly for educational and demonstrative purposes. No ownership or proprietary rights are claimed over the original concept or visual design.
