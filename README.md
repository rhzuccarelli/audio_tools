# Audio Tools

A single-file, browser-based audio toolkit. Everything runs locally in your
browser — no upload, no server, no dependencies.

Open [`index.html`](index.html) in any modern browser to use it.

The current version is shown next to the logo in the header (**Rev 4.1**).

## Choosing a module

The app opens on a **module-selection landing page** where you pick which tool
to work with:

- **Stem Arranger** — sequence loops into a full song and export one clean WAV
  per instrument (documented below).
- **Multi-Channel WAV** — build or edit interleaved multi-channel WAVs from
  independent stereo pairs (see [Multi-Channel WAV mixer](#multi-channel-wav-mixer)).
- **Multitrack Decks** — open several multi-channel WAVs side by side and move
  tracks between them (see [Multitrack Decks](#multitrack-decks)).

Each tool is self-contained: once you're inside one, there is no shortcut to the
others — return to the landing page (click the **Audio Tools** logo) to switch
tools. The current tool lives in the URL hash (`#/stem-arranger`,
`#/multichannel`, `#/multitrack-decks`), so tools are deep-linkable and the
browser back/forward buttons move between them.

### Adding a tool

Tools are data-driven from a single `TOOLS` registry near the top of the
`<script>` in [`index.html`](index.html). A new tool is one registry entry
(`id`, `name`, `tagline`, `icon`, `enter()`/`leave()`/`onSpace()` hooks) plus
its own markup container — the landing grid, hash router, and keyboard handling
are all derived from the registry, so none of them need to be touched. This
keeps the app extensible without depending on a single hardcoded mode switch.

## Version history

The header shows the current revision. **Big changes bump the first digit,
small changes bump the second.** Update the `Rev X.Y` label in the header (and
this list) with every change.

- **Rev 4.1** — The Multi-Channel WAV mixer can now export a **stereo bounce of a
  chosen region**: instead of the full interleaved multi-channel file, mix every
  audible pair down to one stereo WAV over an **In/Out region**. The region can be
  typed in **seconds** or **snapped to whole bars** (using the project tempo and a
  beats-per-bar setting), and the **In/Out** points can be dropped straight from
  the playhead. Mute/solo decide which pairs go into the bounce, so it captures
  exactly what the preview transport plays.
- **Rev 4.0** — New **Multitrack Decks** tool: open several multi-channel WAVs
  **side by side as decks** and **drag tracks from one into another**, so you can
  merge two files or assemble a brand-new one out of pieces of both. Drags
  **move** rather than copy — the source deck gives the track up — and single
  **L/R channels** can be dragged between decks too. Each deck has its own
  import, tempo, transport and export.
- **Rev 3.3** — The Multi-Channel WAV mixer can now source each track's **left
  and right channels independently**. Load two different mono files into one
  track (one per channel), and **route channels between tracks** by dragging a
  channel's L/R grip onto the L or R slot of any other track — e.g. copy the L
  of track 6 into the R of track 3. Each channel has its own load/clear control.
- **Rev 3.2** — Stem Arranger gained an **Export Multi-Track** option: instead
  of one WAV per instrument, render the whole arrangement to a single
  interleaved multi-channel WAV (each instrument on its own channel group, with
  the project tempo embedded), the same format the Multi-Channel tool produces.
- **Rev 3.1** — Stem Arranger can now save and reopen songs. **Save Project**
  writes a small JSON file with the arrangement and tempo (re-load the same
  stems folder to reopen it); **Save Bundle** writes a self-contained JSON file
  with the audio embedded (reopens with no stems folder needed); **Load Song**
  opens either kind.
- **Rev 3.0** — Tools are now isolated: the in-app tool switcher was removed, so
  switching tools goes back through the landing page.
- **Rev 2.0** — Added the module-selection landing page and the `TOOLS` registry
  (deep-linkable, hash-routed tools).

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
- **Export multi-track** — renders the whole arrangement to a single
  interleaved multi-channel WAV (`arrangement_multitrack.wav`) with each
  instrument on its own channel group and the project tempo embedded as an ACID
  chunk — the same combined format the Multi-Channel tool produces.
- **Save & reopen songs** — save the current song to a `.json` file so you don't
  rebuild it from scratch next time, then **Load Song** to bring it back. Two
  save formats:
  - **Save Project** — a small, human-readable text file holding the
    arrangement, trims, section order, and tempo. Re-load the same stems folder,
    then load this file, and everything snaps back into place.
  - **Save Bundle** — a larger, self-contained file with the decoded audio
    embedded, so the song reopens exactly as-is with no stems folder needed.

    **Load Song** accepts either format and detects which one it is
    automatically.

## Usage

1. Open `index.html` in your browser.
2. Click **Load Stems Folder** and pick your `Stems/` directory.
3. Click loops in the left panel to add them to the arrangement.
4. Reorder, swap variations, and trim blocks as needed.
5. Set the BPM and beats/bar if the auto-detected values need adjusting.
6. Press play (or Space) to preview.
7. Click **Export Stems** for one WAV per instrument, or **Export Multi-Track**
   for a single multi-channel WAV with every instrument on its own channel.
8. Click **Save Project** (or **Save Bundle**) to save the song to a `.json`
   file, and **Load Song** to reopen a saved song later.

## Multi-Channel WAV mixer

Switch to **Multi-Channel WAV** in the header to build or edit interleaved
multi-channel WAV files made of 1-6 independent stereo pairs (2-12 channels
total) — e.g. a 6-pair surround/stem-print file where pair 1 is drums, pair 2
is bass, and so on.

- **Set the pair count** (1-6) to size the project.
- **Upload** a mono or stereo file into any individual pair without touching
  the others (the strip's **Upload/Replace** button fills both channels from one
  file — a mono file feeds both, a stereo file splits into L and R).
- **Source each channel independently** — every track exposes a small **L** and
  **R** row. Click the **＋** on a row to load a mono file into just that
  channel, so a single track can hold two unrelated mono files (e.g. a mono kick
  on L and a mono snare on R). The **✕** clears one channel; clearing both
  empties the track. Channels of different lengths are padded with silence to
  match.
- **Route channels between tracks** — drag a channel's **L**/**R** grip and drop
  it onto the L or R slot of any track (including another slot on the same
  track) to copy that channel there. For example, drop track 6's L grip onto
  track 3's R slot to copy it across. The copy is independent — editing or
  clearing the source afterwards doesn't disturb the destination.
- **Import** an existing multi-channel WAV (2-12 channels, even) to split it
  back into its stereo pairs for editing — replace just the pairs you want
  (e.g. re-record pair 1) and re-export. After importing you can also rearrange
  its channels with the routing grips before exporting.
- **Export** writes one interleaved WAV with all pairs combined; empty or
  shorter pairs are padded with silence.
- **Stereo bounce** exports a *stereo mixdown* of a **chosen region** instead of
  the full interleaved file — every audible pair summed to one stereo pair, the
  same downmix the preview transport plays. Set the region with the **In** and
  **Out** fields (the **⤓** button drops each marker at the current playhead),
  and choose whether the region is measured in **Time** (seconds) or **Bars**:
  - **Time** — In/Out are seconds; the bounce is exactly that slice.
  - **Bars** — In/Out are bar numbers (needs a tempo). Pick the **beats/bar**,
    and the region snaps to whole bar lines so the bounce is always a whole
    number of bars. The project tempo is embedded in the bounced WAV.

  Mute and solo decide which pairs are included, so you can bounce just the
  drums (solo them) or everything-but-one (mute it). The readout under the
  fields shows the resolved span before you commit.
- Preview playback mixes every pair down to your stereo output — the
  interleaving itself only exists in the interleaved export (the stereo bounce
  is already a stereo file).

## Multitrack Decks

The Multi-Channel WAV mixer edits **one** file at a time — importing replaces
whatever was open. **Multitrack Decks** opens several at once, side by side, so
you can move tracks between them.

Each **deck** is one multi-channel WAV: import a file into it, or start empty and
build one up. Decks sit next to each other in a rack, and the workspace opens
with three — two to load files into, and a **Build** deck to assemble a new file
out of pieces of the other two. Add or remove decks freely (up to 4).

### Moving tracks between decks

- **Drag a track row onto another deck** to move it there. Drop it between two
  rows to choose where it lands — a line shows the insertion point.
- **Drags move, they don't copy.** The source deck gives the track up and
  renumbers its remaining channels, so the file you're taking tracks *out* of
  stays gap-free. (This is the opposite of the Multi-Channel mixer's channel
  grips, which copy.)
- **Drag a single L or R channel** by its grip onto any channel slot in any deck
  to move just that channel — e.g. take the L of a track in deck B and drop it
  onto the R slot of a track in the Build deck. The source slot empties but its
  track keeps its position, so nothing below it renumbers.
- Dragging a row **within** a deck reorders it, which changes the channel order
  of that deck's exported file.
- A deck holds at most **6 stereo pairs (12 channels)**; a drop onto a full deck
  is refused and the source keeps its track.

### Per deck

- **Import WAV** — load a multi-channel WAV (2–12 channels, even) into that deck
  only. Its ACID tempo is picked up automatically, and its filename becomes the
  deck's export name.
- **Transport** — play/pause, stop, nudge ±5s and a scrub bar, with live meters
  on each track row. Only one deck plays at a time: starting one stops the
  others. **M**/**S** mute and solo within that deck.
- **Export** — writes that deck's pairs as one interleaved WAV with the deck's
  tempo embedded. The **⇲** button on a row exports that single pair on its own
  (or drag it onto your desktop, in Chrome/Edge).
- A row's **＋** loads a mono file into one channel; **✕** clears it. You can
  also drop audio files straight onto a deck, a row, or a single channel slot.

### Limits worth knowing

- **12-channel files can't be re-opened in the browser.** Browsers decode fewer
  channels than a WAV can carry — Chromium reads 10 but refuses 12. A 6-pair
  deck still *exports* a valid 12-channel file that DAWs and hardware read fine,
  and the app tells you at export time that it won't load back in. Keep a deck
  at 5 pairs or fewer if you need to reopen its output here.
- **Everything ends up at 48 kHz.** The app decodes through a single 48 kHz
  audio context, so a 44.1 kHz file is resampled on load and exports are always
  24-bit/48 kHz — which is also why tracks can always move between decks.

## Notes

- Everything is processed client-side; your audio never leaves your machine.
- Best results in Chromium-based browsers (the folder picker uses
  `webkitdirectory`).
- Export length is bounded by browser memory — very long arrangements with many
  stems may need to be split up.
