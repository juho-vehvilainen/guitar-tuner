# Guitar Tuner, plan (2026-09-09)

A browser-based chromatic and string guitar tuner that runs on iPhone Safari
from GitHub Pages. No native app, no backend, no build step.

## Why a website is enough

- iOS Safari supports Web Audio and microphone access via `getUserMedia`.
- GitHub Pages serves over HTTPS, which the browser requires for mic access.
- "Add to Home Screen" gives a full-screen app with a manifest and icon.

## Decisions

**Tunings.** Six presets stored as MIDI note numbers, low string first:
Standard, Half step down, Full step down, Drop D, Drop C, DADGAD.
Plus a global semitone offset from -12 to +12, so any preset can be shifted
without adding more presets. Half step down exists both as a preset and as
Standard with offset -1; the preset is there because it is the most common
alternate tuning and deserves one tap.

**Reference pitch.** A4 = 440 Hz by default, adjustable 415 to 466 Hz.

**Modes.** String mode (default) snaps to the nearest string in the selected
tuning and shows the offset in cents. Chromatic mode shows whatever note is
played. Strings auto-detect; tapping a string chip locks it, tapping again
unlocks.

**Detection.** McLeod Pitch Method (normalised square difference function)
written by hand, no library. Buffer 4096 samples, lag range covering 50 to
1500 Hz, peak threshold 0.9, parabolic interpolation, clarity gate 0.85.
Noise gate on RMS. Median of the last 5 readings drives the needle. In-tune
window is plus or minus 3 cents.

**Display.** Synthwave theme shared with the Vortex 84 site (added 2026-09-09): Orbitron and Share Tech Mono, sunset-grid canvas that reacts to the mic and glows green when in tune, CRT overlays. Big note name, semicircle gauge from -50 to +50 cents, cents and
Hz readouts, six string chips with the active one highlighted. Green when in
tune, amber within 15 cents, red beyond. Dark theme only.

**Phone.** Wake Lock keeps the screen on while listening. Audio starts from a
tap because iOS requires a user gesture. Settings persist in localStorage.

**Repo.** One `index.html` with inline CSS and JS, a manifest, icons, and the
same GitHub Actions Pages workflow as the Vortex 84 site.

## Deliberately left out

Bass and ukulele presets, tone playback for tuning by ear, polyphonic tuning.

## Later ideas

- Strobe-style display as an alternative to the needle.
- Per-string "in tune" ticks that show progress across all six strings.
- Custom tuning editor.
