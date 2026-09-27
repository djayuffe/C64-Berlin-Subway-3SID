# Architecture and timing model

## Program layout

`src/subway.s` is one ACME source file that assembles to a BASIC-loadable PRG.
The BASIC stub at `$0801` executes `SYS 2061`, entering `MegaMain` at `$080d`.
The program banks out BASIC with `$01 = $36`, retains KERNAL/I/O, selects VIC
bank 0, uses screen RAM at `$0400`, and writes colour RAM at `$d800`.

The main loop is deliberately simple:

```text
raster IRQ (50 Hz)
  -> music tick and pulse decay
  -> derive row/beat visual pulses
  -> set frameReady
main loop
  -> atomically consume row/beat edges
  -> update active effect
  -> advance bar-based scene state machine
  -> apply shared 3SID colour polish
```

The renderer never advances music timing itself. It only consumes sticky edge
flags produced by the IRQ, so a costly effect cannot lose a row or beat event.

## IRQ and musical clock

`InstallIRQ` installs `MegaMain_IRQ` on raster line 250. Every IRQ calls
`TV_PlayMusic`, which advances the current pattern row after seven frames.
`TV_MusRow` has 16 rows per bar and `TV_BeatEdge` is raised every four rows.
`ComputeVisualPulses` turns the row phase into `beatSin` and `zoomPulse`; all
effects can therefore respond to the same audio-time source.

The startup routine initializes `TV_MusRow` to `$ff`, then forces the first IRQ
to start section 2 at row 0. Bass, drums, arpeggio, and beat state begin on
that same edge. Pulse state decays in the IRQ at 50 Hz, which prevents visual
energy from depending on main-loop execution time.

## Three-SID score

The default layout is intentionally explicit:

| Chip | Address | Role |
| --- | --- | --- |
| SID1 | `$d400` | Bass, sub, and ghost voices; filtered. |
| SID2 | `$d420` | Arpeggio, fifth harmony, and octave shimmer; filtered. |
| SID3 | `$d440` | Dry kick, snare, closed hat, and open hat voices. |

`TV_MusInit` clears all three chips, initializes ADSR/pulse/filter state, and
selects the Berlin A/B style. `TV_MusRowTrigger` reads the active pattern row;
`TV_MusArp` fires only phases 0, 2, and 4; and the drum routines create the
separate percussion hits. `sndPulse` accumulates triggered energy, then
`ThreeSIDEffectPolish` maps the individual bass/melody/drum pulse components to
subtle colour response shared by every effect.

## Scene scheduler

`partId` indexes `InitTbl` and `UpdateTbl`; there are exactly 28 entries in
each. `PartBarsTbl` is the duration authority: each entry is three or four
16-row bars. The old `PartFramesTbl_*` values remain only as legacy tooling
metadata and do not control scene duration.

At a row-0 edge, `partBarsLeft` is decremented. When the final bar has elapsed,
the main loop waits for row 12, starts the fade, and enters the transition
state. `TransCard` advances a monotonic 32-step palette fade. On the next row-0
edge it selects the next part (wrapping after 27), clears the screen/colour RAM,
calls the next initializer, and restarts the bar counter. This makes visual
transitions deterministic and music-aligned.

## Effect safety rules

All effects draw text-mode characters and colours. The active drawing region is
rows 0–23; row 24 is intentionally reserved for legacy scroller compatibility.
Individual effects use their own phase bytes and tables, while shared zero-page
pointers are separated from music pointers. Effects do not install IRQ handlers
or take over the raster scheduler. This keeps timing, audio, and transitions
under one owner.

## Build and runtime verification

`make` delegates to `src/Makefile`, which assembles `src/subway.s` with ACME as
`build/subway_3sid_v59.prg`. The CI workflow rebuilds from source, verifies the
two-byte PRG load address is `$0801`, checks that the file is nontrivially sized,
and emits a SHA-256 digest. VICE captures in `assets/effects/` are generated
from that PRG with warp enabled and a cycle limit; they are runtime evidence,
not hand-drawn artwork.
