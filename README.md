# C64 - Berlin Trip Subway 3SID

A Commodore 64 ACME-assembler variant of Berlin Trip Subway, arranged for a
three-SID layout: bass/sub on SID1, arpeggio/harmony on SID2, and drums on
SID3.

![VICE runtime capture](assets/live-vice.png)

The timing model captures row and beat edges atomically for the renderer,
uses a 50 Hz IRQ pulse decay, and schedules each scene in musical bars rather
than display frames.

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

The PRG starts through its embedded BASIC loader (`SYS 2061`). See
`V5_9_0_NOTES.md` for the final timing, music, and effect design notes.

## Live VICE capture

![Running C64 Berlin Subway 3SID](assets/live-vice.png)
