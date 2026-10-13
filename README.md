# NEBULA 8D — Feel the Sound in 3D

A JavaScript-only, frontend music player and local audio effects lab built with the Next.js App Router, React, Three.js, React Three Fiber, Drei, and the Web Audio API. Production output is a static site (`out/`); no custom application backend, database, account system, cloud processing, AI API, remote audio request, or runtime asset service is included.

## Requirements

- Node.js 20.9 or newer (required by current Next.js releases)
- npm 10 or a compatible npm version
- A modern browser with HTML audio, Web Audio API, `OfflineAudioContext`, and WebGL for the full experience

## Install and run

```bash
npm install
npm run dev
```

Open the local URL printed by Next.js (normally `http://localhost:3000`). Import a local audio file, select it, and press Play. Browsers commonly require an explicit user gesture before audio contexts can start.

Useful scripts:

```bash
npm run lint
npm run build
npm run serve:static
```

`npm run build` runs Next.js static export and writes the deployable site to `out/`. `npm run serve:static` serves that already-built folder on `http://localhost:8080` using Python's static file server; it is not an application backend. Alternatively, deploy the contents of `out/` to any static host.

## Privacy and local data

- Audio is imported using the browser File API and played through an object URL.
- The app does not upload audio or call an AI endpoint.
- Lightweight effect, display, and custom preset preferences can be saved in `localStorage`.
- Temporary object URLs are not persisted. Re-import files after a reload.
- Imported track entries and metadata edits exist for the current browser session. Named playlist definitions and file fingerprints may persist locally, but audio files and temporary URLs do not. The underlying original files are never modified.
- Runtime UI fonts use system font stacks. There are no runtime stock image, font, audio, or texture downloads.

## Implemented features

- Multi-file local audio import, drag/drop, duplicate checks, duration probing, search, metadata editing, per-track mood tags and preferred visualization, remove/clear, repeat, shuffle, speed, seek, volume, mute, and keyboard shortcuts.
- Reusable `AudioContext` graph with input gain, bass/treble shelves, seven-band EQ, parallel generated reverb, stereo panning, optional dynamics compression, analyser and master gain.
- Real-time analyser-based bass/mid/treble/energy features, canvas spectrum and waveform, frequency bars, transient approximation, and an interactive Three.js / React Three Fiber visual environment.
- Twelve selectable visual worlds: NEBULA CORE, SONIC RINGS, PARTICLE STORM, DEEP SPACE, FREQUENCY TUNNEL, MINIMAL WAVE, CELESTIAL SERENITY, IMPACT ARENA, BASS REACTOR, DREAM BLOOM, FOCUS GRID, and SOLAR CELEBRATION.
- Local mood recommendations that distinguish user-assigned tags from acoustic observations. The app does not claim to infer lyrics, religion, or exact emotional meaning from frequency analysis.
- Low-motion and low-performance settings, local custom presets, a 2D canvas fallback when WebGL is unavailable, and offline WAV export using a separate `OfflineAudioContext` graph and PCM/WAV encoder.

## Audio export details

The WAV exporter decodes the selected `File` locally, renders the EQ, bass and treble shelves, reverb when enabled, stereo movement profile, compressor when enabled, and master level, then writes PCM 16-bit WAV. A final global headroom pass attenuates the rendered buffer if its peak would exceed 0.96 full scale, avoiding hard sample clipping. The source file is unchanged. Render size can be memory-intensive; files above 80 MB trigger a warning. Browser APIs do not expose accurate render progress for this pipeline, so the UI does not fabricate a progress percentage.

## Browser limitations

- Supported import codecs depend on the browser's decoder. A recognizable extension does not guarantee a particular browser can decode the file.
- The spatial effect is conventional stereo panning. It is accurately described as “8D-style” movement rather than true HRTF binaural rendering.
- The offline export generates its own local reverb impulse response. Reverb texture may differ slightly between exports. The export recreates supported effects, not the live graph's wall-clock modulation phase.
- `OfflineAudioContext` may fail or exhaust memory for very long/high-resolution files. The app reports export errors and leaves the original unchanged.
- The audio spectrum and waveform are live windows into the current signal, not an offline full-track waveform preview.
- WebGL context-loss recovery is browser-dependent. The 2D fallback covers browsers where a WebGL context cannot be created at startup.
- Static hosting is supported. The development server is only needed for development; there is no custom backend service in the production build.

## Manual test checklist

1. Import at least two browser-decodable local audio files; re-import one duplicate and confirm it is skipped.
2. Play/pause, seek by timeline and waveform, switch tracks, adjust playback speed and volume, then test previous/next, repeat and shuffle.
3. Toggle spatial panning, adjust pan/depth/speed, compare EQ band changes, and enable/disable bass, treble, reverb, and compression.
4. Watch the spectrum, waveform and energy meters while the audio is playing. Change every visualization preset without stopping playback.
5. Assign a spiritual tag manually and confirm Celestial Serenity is recommended; try bass-heavy audio and compare acoustic recommendations. Confirm manual world selection remains in effect unless Auto Match is selected.
6. Export a short track to WAV, open the exported file in a local audio player, and verify it is a new processed file. Try a large file and confirm the memory warning.
7. Test keyboard controls, reduced motion, the low-performance profile, narrow/mobile layout, and static output from `out/`.

## Project layout

```text
app/
  layout.js
  page.js
  globals.css
components/
  NebulaApp.js
  AudioVisualizer3D.js
  VisualizerFallback2D.js
  AudioMeters.js
hooks/
  useAudioAnalysis.js
lib/
  audio-engine.js
  audio-analysis.js
  audio-export.js
  mood-matcher.js
  storage.js
next.config.js
package.json
```
