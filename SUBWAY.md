# Berlin Subway 3SID — journey concept

A C64 text-mode megademo that follows a continuous ride from Berlin airport to
the Golden Heart Hotel. The experience is scored end to end by a three-SID
Berlin A/B techno arrangement.

## Runtime model

`src/subway.s` owns the complete engine: VIC setup, one 50 Hz raster IRQ,
three-SID music, effect dispatch, and bar-locked transitions. It is a
standalone source file, not a build-time dependency on another project.

## 28 effects

The dispatch tables select 28 visual parts, including vortex, multiplexed edge
field, raster-boot tunnel, and neon wire cube. Each part is scheduled for
exactly three or four 16-row musical bars. See [docs/EFFECTS.md](docs/EFFECTS.md)
for the routine-level inventory and verified VICE captures.

## Five evolving real-techno sections (the journey)
- The music flows through 5 sections on each song loop (curSong cycles 2..6):
  departure build -> the journey peak -> through-the-city (melodic) ->
  underground dub breakdown -> arrival rave peak, then loops.
- All on the improved techno engine at ~150 BPM: four-on-the-floor kick, offbeat
  open hats, claps on 2 & 4, 16th hats in the peaks.
- BETTER BASS: rolling resonant-saw — deep SUB drop on beat 1, punchy root/octave
  16th roll, fifth lead-back; V1 ADSR $08/$78 for body; per-bar resonant filter
  "wah".  Sparse dark minor stabs on the offbeats with a release tail (V2 SR $f8).
- Verified dynamic arc: RMS swings from ~1600 (the dub breakdown) to ~2640 (the
  rave peak) — distinct sections, none dead-silent.

## Text — only the trip
- Title:  BERLIN / A TRIP / FROM THE AIRPORT / TO THE GOLDEN HEART HOTEL.
- Every effect card is a station in order: flughafen ber, terminal one two,
  wassmannsdorf, schoenefeld ... alexanderplatz ... kottbusser tor ... and finally
  GOLDEN HEART HOTEL / "you have arrived" (shown as the loop closes).  Subtitles
  are the line/leg ("airport express", "change here", "u2 u5 u8", ...).
- Scroller narrates only the ride — touchdown at flughafen ber, the airport
  express into the tunnel, the city rolling by underground, up the stairs into the
  night — with greet-stops at the key stations, ending at the Golden Heart Hotel.

Build:  `acme -f cbm -o build/subway_3sid_v59.prg src/subway.s`

Run:    `x64sc -autostartprgmode 1 -autostart build/subway_3sid_v59.prg`
