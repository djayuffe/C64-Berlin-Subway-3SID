# Subway 3SID v5.9.0 Final Music / Timing / Effects Lock

Final implementation:

- Berlin A/B are the only active music sections.
- Stable 7-frame row timing.
- First IRQ starts section 2, row 0, bass, drums, arp and beat together.
- SID1 `$d400`: bass/sub/ghost.
- SID2 `$d420`: arp/harmony/shimmer.
- SID3 `$d440`: kick/snare-hat drum chip.
- Pulse decay is IRQ-driven at 50 Hz.
- Row and beat edges are atomically captured for the renderer.
- Arp triggers only at phases 0, 2 and 4.
- Scene duration is no longer renderer-frame based.
- Every scene lasts an exact 3 or 4 musical bars.
- Fade begins at row 12 and the next scene begins at row 0.

Build:

```bash
cd mega
./build_release.sh
```
