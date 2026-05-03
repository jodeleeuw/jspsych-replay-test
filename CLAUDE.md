# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

A single-file static demo (`index.html`) that exercises a wide range of jsPsych plugins to validate the **`record_session`** feature added in [jsPsych PR #3661](https://github.com/jspsych/jsPsych/pull/3661). The point of each trial is to drive a *different* class of input event (mouse click, key down/up, mouse drag, slider input, canvas, freehand draw, drag-and-drop, text typing) so the resulting `jsPsych.getSessionRecording()` JSON can be inspected for completeness.

There is no build system, no test suite, no `package.json`, and no application backend. Treat this repo as a fixture, not a library.

## Running locally

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

Opening `index.html` via `file://` mostly works but some browsers restrict features that require an HTTP origin — prefer the local server.

## Deployment

Push to `main` → `.github/workflows/deploy-pages.yml` uploads the repo root as a Pages artifact and deploys it. There's no build step; the workflow uploads files as-is. Live demo: https://jodeleeuw.github.io/jspsych-replay-test/

## Architecture & important conventions

**jsPsych is loaded from jsDelivr pinned to a specific commit on the `preview/pr-3661` branch** of jspsych/jsPsych — *not* from npm, *not* from `main`, and *not* from the PR's actual head branch (`claude/add-hifi-data-recording-...`). The PR head branch contains source only; built `dist/` and `css/` files live on a separate CI-pushed branch named `preview/pr-3661`, populated by upstream's `preview-publish.yml` workflow. The pinned SHA appears in every `<script>` and `<link>` tag in `index.html`.

**To bump the pin** (the standard fix when scripts start 404'ing): get the current HEAD of upstream's `preview/pr-3661` branch (`gh api repos/jspsych/jsPsych/branches/preview/pr-3661 --jq .commit.sha`), then `replace_all` that SHA across every CDN URL in `index.html`. The README has an example URL that may also reference an old SHA; update it for accuracy but the demo doesn't depend on it. Recent commits like `5aba490` are exactly these SHA bumps. Always update all URLs together, never just one. Do **not** use the SHA from `gh api repos/jspsych/jsPsych/pulls/3661 --jq .head.sha` — that's the source branch and has no dist files.

**The recording UI lives inside the `on_finish` callback**, not in a separate page. After the timeline completes, `showRecording()` overwrites `document.body` with a summary table + full JSON pretty-print + download button, and stashes the raw object on `window._sessionRecording` for console inspection. `recording.end_reason` is intentionally `null` here because we read the recording *during* `on_finish`, before jsPsych's internal `stop()` runs — this is documented in the UI and is not a bug.

**Adding a new trial**: pick a plugin that exercises an input modality not already covered (see the table in `README.md`). Load its dist file via the same pinned SHA, append the trial config to the `timeline` array near the bottom of `index.html`, and update the README table. Don't add plugins that duplicate an already-covered event type — the demo's value is breadth of input coverage.
