# Grid 909

A browser drum machine and melody pad for techno. One HTML file, no build step, no dependencies, no samples — every sound is synthesized with the Web Audio API.

Open `index.html` in a browser. That's the whole install.

## What it does

- **8 drum/synth layers** on a 16-step grid: kick, clap, hat, open hat, perc, bass, stab, blip.
- **A 2-bar melody pad** you draw on with a finger or mouse. Pitch is quantized to A minor pentatonic, so any line fits over the rhythm section.
- **Per-layer shaping**: length and tone on a radial dial, drive and delay send on two knobs, plus a sweep shape (sine, ramp up, ramp down, sample & hold) that moves the tone on its own over 2, 4 or 8 bars.
- **Swing**, up to just past a triplet feel.
- **Chord progression** (Am–F–C–G) that moves the bass and stab every 4 or 8 bars while the drawn line stays put.
- **Gate**: arm 1/16, triplet or an accelerating build and it chops the last bar of the 4-bar phrase, then disarms itself.

## Controls

| Action                                | Result                                                   |
| ------------------------------------- | -------------------------------------------------------- |
| Tap a grid cell, or drag across cells | Place or clear hits                                      |
| Drag on the melody pad                | Draw a line; hold a row flat for a longer note           |
| `Erase` then drag, or right-drag      | Erase single columns of the line                         |
| Tap a layer name                      | Mute / unmute immediately                                |
| Tap `»`                               | Mute / unmute at the next bar line (tap again to cancel) |
| Hold `»`                              | Open that layer's shaping dial                           |
| Spacebar                              | Play / stop                                              |

The phase strip above the grid shows the position in the chord cycle, and marks in amber the bar an armed gate will land on.

## How it works

Everything lives in `index.html` — markup, CSS, and one script block, in this order:

1. **Pattern state.** `pat` holds the drum grid, `line` holds the 32-column melody, `P` holds per-layer parameters and their per-voice ranges (`RANGE`).
2. **Canvas drawing.** The melody pad's static art is rendered once into an offscreen canvas; each step only blits that and repaints the playhead column.
3. **Audio graph.** Built once at startup: a permanent bus per layer (dry / waveshaper drive / delay send), one shared dotted-eighth delay, a sidechain bus that ducks the tonal layers under every kick, and a gate node on the master.
4. **Voices.** Small functions that create an oscillator or a filtered noise burst per hit, and disconnect themselves on `ended`.
5. **Scheduler.** A 25 ms interval looks 120 ms ahead and schedules notes against `AudioContext.currentTime`. Swing, sweeps, chord changes and the gate are all functions of the absolute step counter, so they cost nothing beyond the arithmetic and stay phase-locked to the bar forever.

Two design notes worth knowing before changing things:

- **The pad is pentatonic on purpose.** Dropping the 2nd and 6th degrees removes every minor second and the tritone from the grid, so no two notes you can draw will clash. That's why the chord progression can move underneath a fixed melody without ever sounding wrong.
- **Sidechain ducking is doing a lot of work.** The bass, stab, blip and line all pass through `duck`, whose gain dips on every kick. Remove it and the whole thing immediately sounds like a toy.

## Deploying

It's a static file. GitHub Pages, Netlify, Cloudflare Pages, or any web server will serve it as-is. No bundler, no npm install.

Browser support: anything with Web Audio and Pointer Events — current Chrome, Safari, Firefox, and mobile versions of the same. Audio starts only after the first tap on Play, as browsers require.

## Known limits

- Patterns are not saved. A reload clears the board.
- Bluetooth output adds 150–300 ms of latency that the page can't compensate for. Use wired output or the device speaker when performing.
- The drum grid is one bar and the melody pad two; both loop lengths are constants (`STEPS`, `PAD`) if you want to change them.

## License

MIT — see `LICENSE`.
