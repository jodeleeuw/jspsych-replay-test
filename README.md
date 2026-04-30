# jspsych-replay-test

Demo experiment for testing the [session recording / replay feature](https://github.com/jspsych/jsPsych/pull/3661) added to jsPsych v8.

## What is this?

This repository contains a self-contained demo experiment (`index.html`) that exercises a wide variety of jsPsych plugins and input types. Its purpose is to validate that the `record_session: true` feature captures all the events needed to reconstruct a session visually.

## Running the demo

Open `index.html` directly in a browser, **or** serve it with a local HTTP server (required for some browsers):

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Plugins included

| Trial | Plugin | Input type captured |
|-------|--------|---------------------|
| Instructions | `plugin-instructions` | `mouse.click` (Next / Previous buttons) |
| Fixation cross | `plugin-html-keyboard-response` | `key.down` / `key.up` (Space) |
| Stroop task (×3) | `plugin-html-keyboard-response` | `key.down` / `key.up` (R / G / B) |
| Mood rating | `plugin-html-button-response` | `mouse.click` on buttons |
| Confidence slider | `plugin-html-slider-response` | `mouse.down` / `mouse.move` / `mouse.up` on range input |
| Canvas stimulus | `plugin-canvas-keyboard-response` | Canvas `dom` snapshot + `key.down` / `key.up` |
| Sketchpad | `plugin-sketchpad` | `mouse.down` / `mouse.move` / `mouse.up` (freehand drawing) |
| Free sort | `plugin-free-sort` | `mouse.down` / `mouse.move` / `mouse.up` (drag & drop) |
| Text survey | `plugin-survey-text` | `key.down` / `key.up` (text typing) |

## Session recording

The experiment initialises jsPsych with:

```js
const jsPsych = initJsPsych({ record_session: true });
```

After all trials complete, `jsPsych.getSessionRecording()` is called in the `on_finish` callback and the full JSON recording is displayed on screen. The recording object (schema version 1) includes:

- Per-trial initial DOM snapshots and mutation log
- Mouse, keyboard, touch, clipboard, media, focus, and scroll events
- Viewport dimensions and changes
- Seeded `Math.random` call log

The recording is also stored at `window._sessionRecording` for inspection in the browser console.

## Source

All jsPsych files are loaded via [jsDelivr](https://www.jsdelivr.com/) pointing to the `preview/pr-3661` branch of the jsPsych repository, which contains the pre-built dist files for the recording feature:

```
https://cdn.jsdelivr.net/gh/jspsych/jsPsych@3aa66f74be8247ecd742e51d25aee59cf6323be9/packages/...
```
