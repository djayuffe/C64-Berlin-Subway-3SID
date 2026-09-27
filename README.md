# C64 Berlin Subway 3SID

A timing-locked Commodore 64 ACME megademo: a 28-effect visual journey scored
by a three-SID soundtrack. SID1 supplies bass/sub, SID2 supplies arpeggio,
harmony and shimmer, and SID3 supplies independent kick, snare, and hat voices.
The program uses a 50 Hz raster IRQ and a musical-bar scene scheduler so visuals
remain locked to the groove instead of depending on renderer speed.

![VICE runtime capture](assets/live-vice.png)

## Highlights

- 28 text-mode effects: fields, waves, tunnels, stars, hearts, cubes, grids,
  corridors, flares, and wireframe finales.
- PAL 50 Hz raster IRQ with atomic row/beat edge handoff from music to renderer.
- Exact three- or four-bar scenes; fade starts at row 12 and the next scene
  begins on row 0.
- Default three-SID register map: `$d400`, `$d420`, and `$d440`.
- Zero external runtime assets: the PRG contains code, tables, patterns, and
  the two packed wire-cube bitmaps.

See [the architecture guide](docs/ARCHITECTURE.md) for the IRQ, timing, memory,
and audio logic; [the effects gallery](docs/EFFECTS.md) for every dispatch-table
effect and its VICE capture; and [SUBWAY.md](SUBWAY.md) for the journey concept.

## Build

Requires [ACME](https://sourceforge.net/projects/acme-crossass/) on your PATH.

```sh
make
```

This writes `build/subway_3sid_v59.prg`. To run it in
[VICE](https://vice-emu.sourceforge.io/):

```sh
x64sc -autostartprgmode 1 -autostart build/subway_3sid_v59.prg
```

The PRG starts through its embedded BASIC loader (`SYS 2061`). Enable two extra
SID chips in VICE at `$d420` and `$d440`; physical hardware needs the same
three-SID address layout. The project uses only text mode and leaves row 24
clear for legacy scroller compatibility.

## Verification

`make` assembles the binary deterministically with ACME. GitHub Actions repeats
that build on Ubuntu, checks the PRG header/load address, and records a SHA-256
digest. The effect captures are generated from the built PRG in VICE, not drawn
or synthesized separately.

## License and copyright

Copyright © 2026 Ulf Bertilsson.

Licensed under the GNU General Public License, version 3 or later
([GPL-3.0-or-later](LICENSE)). See [NOTICE](NOTICE) for the project notice.
