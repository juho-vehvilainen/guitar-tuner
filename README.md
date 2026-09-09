# Guitar Tuner

A guitar tuner that runs in the browser, built for iPhone Safari and hosted on
GitHub Pages. Chromatic and string modes, six tuning presets, semitone
transpose, adjustable reference pitch.

**Live:** https://juho-vehvilainen.github.io/guitar-tuner/

Single-file static site. No build step, no dependencies. Open `index.html`
over HTTPS or localhost and it works. Microphone access requires a secure
context, so opening the file directly from disk will not work.

## Using it on iPhone

1. Open the URL in Safari and tap **Tap to start**. Allow the microphone.
2. Share → **Add to Home Screen** for a full-screen app with its own icon.
3. Safari asks for the microphone on each visit by default. To stop that,
   open the page, tap **AA** in the address bar → Website Settings →
   Microphone → Allow.

The screen stays awake while the tuner is listening. The microphone stops
when the screen locks or you switch apps. Tap start again to resume.

## Features

- **Tunings:** Standard, Half step down, Full step down, Drop D, Drop C, DADGAD.
- **Transpose:** shift any preset from -12 to +12 semitones in Settings.
- **Reference pitch:** A4 from 415 to 466 Hz, default 440.
- **String mode** (default) snaps to the nearest string of the selected tuning.
  Tap a string chip to lock to it, tap again to unlock.
- **Chromatic mode** shows the nearest note of any pitch.
- **Note names:** auto (flats for flat tunings and downward transpose, sharps
  otherwise), or force sharps or flats.
- Settings persist in `localStorage`.

## How detection works

Audio comes from `getUserMedia` with echo cancellation, noise suppression and
auto gain turned off, into an `AnalyserNode` with a 4096-sample buffer. Every
40 ms the time-domain buffer is read and run through the McLeod Pitch Method:

1. Compute the normalised square difference function for lags covering
   50 to 1500 Hz.
2. Find the key maxima, one per positive region after the first negative
   crossing.
3. Take the first maximum that is at least 0.9 times the highest one. This
   picks the fundamental even when a harmonic is louder, which is common on
   the low E string.
4. Parabolic interpolation around that lag for sub-sample precision.

Readings are gated on RMS (silence) and clarity (unpitched noise), and the
median of the last five readings drives the needle. In tune is plus or minus
3 cents.

A Node test against synthetic tones with bright, pure, and weak-fundamental
harmonic profiles across all six strings at 44.1 and 48 kHz lands within
1.5 cents in every case, at about 5 ms per detection on a laptop.

## Files

- `index.html` – the whole app, inline CSS and JS
- `manifest.webmanifest`, `icons/` – Add to Home Screen support
- `.github/workflows/deploy.yml` – deploys to GitHub Pages on push to `main`
- `PLAN.md` – the design decisions

## Deploy

Push to `main`. GitHub Actions uploads the repository root as the Pages
artifact. In the repository settings, Pages source must be set to
**GitHub Actions** once.
