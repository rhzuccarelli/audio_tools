# Audio Tools

A single-file, browser-based audio toolkit. Everything runs locally in your
browser — no upload, no server, no dependencies.

Open [`index.html`](index.html) in any modern browser to use it.

## Choosing a module

The app opens on a **module-selection landing page** where you pick which tool
to work with:

- **Stem Arranger** — sequence loops into a full song and export one clean WAV
  per instrument (documented below).
- **Multi-Channel WAV** — build or edit interleaved multi-channel WAVs from
  independent stereo pairs (see [Multi-Channel WAV mixer](#multi-channel-wav-mixer)).

The current tool lives in the URL hash (`#/stem-arranger`, `#/multichannel`),
so tools are deep-linkable and the browser back/forward buttons move between
them. Click the **Audio Tools** logo at any time to return to the picker.

### Adding a tool

Tools are data-driven from a single `TOOLS` registry near the top of the
`<script>` in [`index.html`](index.html). A new tool is one registry entry
(`id`, `name`, `tagline`, `icon`, `enter()`/`leave()`/`onSpace()` hooks) plus
its own markup container — the landing grid, header switcher, hash router, and
keyboard handling are all derived from the registry, so none of them need to be
touched. This keeps the app extensible without depending on a single hardcoded
mode switch.

## What it does

Drop in a folder of stems organised by section and loop, sequence them however
you like, preview the result in real time, then export a separate WAV for each
instrument spanning the entire arrangement.

## Expected folder structure

The app reads the last three path components of each audio file as
`section / loop / filename`:

```
Stems/
├── Intro/
│   ├── Loop 01/   ← KICK.wav, KIT.wav, SHAKER.wav…
│   └── Loop 02/
├── Verse/
├── Chorus/
└── Bridge/
```

- **Section** — the top-level musical part (Intro, Verse, Chorus, …). Recognised
  section names are auto-sorted into a sensible musical order.
- **Loop** — a variation within a section. A folder with only `section/file.wav`
  (no loop folder) is treated as the `Default` loop.
- **Filename** — the leading token before the first `_` is used as the
  instrument name (e.g. `KICK_8 Bar Chorus_Loop 01.wav` → `KICK`). A `N bar`
  hint in the filename is used to detect tempo.

Supported audio formats: `wav`, `mp3`, `m4a`, `aif`, `aiff`, `ogg`, `flac`.

## Features

- **Load a folder of stems** directly from disk (uses the directory file picker).
- **Section library** grouped by section, colour-coded, with per-loop stem and
  duration info.
- **Build an arrangement** by clicking loops to append them; drag blocks to
  reorder; swap loop variations per block.
- **BPM & time signature** — tempo is auto-detected from bar counts in filenames
  (median across loops) and can be overridden; beats-per-bar is configurable.
- **Per-block trimming** — crop start/end by beats or whole bars with a visual
  beat timeline.
- **Live preview** — play, pause, stop, seek, and see the current block
  highlighted, all synced via the Web Audio API.
- **Export stems** — renders each instrument to its own WAV
  (`INSTRUMENT_arrangement.wav`) covering the whole song using an
  `OfflineAudioContext`.

## Usage

1. Open `index.html` in your browser.
2. Click **Load Stems Folder** and pick your `Stems/` directory.
3. Click loops in the left panel to add them to the arrangement.
4. Reorder, swap variations, and trim blocks as needed.
5. Set the BPM and beats/bar if the auto-detected values need adjusting.
6. Press play (or Space) to preview.
7. Click **Export Stems** to download one WAV per instrument.

## Multi-Channel WAV mixer

Switch to **Multi-Channel WAV** in the header to build or edit interleaved
multi-channel WAV files made of 1-6 independent stereo pairs (2-12 channels
total) — e.g. a 6-pair surround/stem-print file where pair 1 is drums, pair 2
is bass, and so on.

- **Set the pair count** (1-6) to size the project.
- **Upload** a mono or stereo file into any individual pair without touching
  the others.
- **Import** an existing multi-channel WAV (2-12 channels, even) to split it
  back into its stereo pairs for editing — replace just the pairs you want
  (e.g. re-record pair 1) and re-export.
- **Export** writes one interleaved WAV with all pairs combined; empty or
  shorter pairs are padded with silence.
- Preview playback mixes every pair down to your stereo output — the
  interleaving itself only exists in the exported file.

## Notes

- Everything is processed client-side; your audio never leaves your machine.
- Best results in Chromium-based browsers (the folder picker uses
  `webkitdirectory`).
- Export length is bounded by browser memory — very long arrangements with many
  stems may need to be split up.
