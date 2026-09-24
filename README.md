# C64 - Berlin Trip Subway 3SID

A Commodore 64 ACME-assembler demo variant of Berlin Trip Subway, arranged
for three SID chips: bass/sub, arpeggio/harmony, and drums.

## Build

Requires [ACME](https://sourceforge.net/projects/acme-crossass/) on your PATH.

```sh
make
```

This writes `build/subway_3sid_v59.prg`. To run it in VICE:

```sh
x64sc -autostartprgmode 1 -autostart build/subway_3sid_v59.prg
```

See `V5_9_0_NOTES.md` for the final timing, music, and effect design notes.
