# MSE timer-throttling / Energy Saver test page

A single-page test harness for observing how throttling a page's JavaScript
timers (`setTimeout` / `setInterval`) to **1 Hz** affects **MediaSource (MSE)**
video buffering.

It exists to investigate the Chromium `ThrottleFullscreenVideoActiveTab`
feature, which throttles the active tab's throttleable task queues (JS timers)
to 1 Hz while the tab shows an **effectively-fullscreen video** and the browser
is in **Energy Saver** mode. Network/`fetch` completion, MSE `updateend`, and
other media-element events (`timeupdate`, `progress`, `waiting`) are **not**
throttled.

## Live demo

Once hosted on GitHub Pages: `https://<your-user>.github.io/<repo>/`

## What it shows

- **Timer rate** — requested vs. actual `setInterval`/`setTimeout` interval, so
  you can see the 1 Hz throttle kick in.
- **Buffer ahead of the playhead**, **rebuffering events**, **total stalled
  time**, `currentTime`, and **rAF FPS** (rAF is not throttled by the feature).

## Engines

Pick an **Engine** in the controls:

- **synthetic MSE demo (offline)** — a built-in MSE loop that appends fragments
  of the bundled clip. Choose the *buffering driver*:
  - `setInterval` / `setTimeout` — throttled by the feature; the buffer drains
    to 0 under 1 Hz (with small segments), causing rebuffers.
  - `requestAnimationFrame` — not throttled (but not viable for real background
    buffering).
  - `event-driven (updateend + timeupdate)` — models a real player; stays smooth
    under the throttle.
  - `Fragments/append` raises the effective segment size.
- **hls.js / dash.js / Shaka Player / rx-player** — the **real** libraries,
  loaded from a CDN, playing a real HLS/DASH stream. The buffer/rebuffer metrics
  are computed from `video.buffered` + media events, so they work the same for
  every engine. (Requires internet for the CDN + streams.)

## How to trigger the throttle

- **Chromium**: launch with `--enable-features=ThrottleFullscreenVideoActiveTab`,
  turn on Energy Saver (`chrome://settings/performance`), click **Go Fullscreen**.
- **Microsoft Edge**: enable Energy Saver and go fullscreen.
- **Any browser (quick proxy)**: background the tab — background tabs already
  throttle timers to 1 Hz, so you see the same buffering dynamics.

With normal-latency streams (multi-second segments, deep buffers) real players
typically **don't** rebuffer even when throttled, because one scheduling tick per
second still fetches more than one second of media. The at-risk case is
**low-latency live** (sub-second segments, a thin buffer at the live edge) — try
pointing the Media URL at an LL-HLS / LL-DASH stream.

## Run locally

MSE needs the media served over HTTP. From this folder:

```bash
# Option A: Python
python -m http.server 8000

# Option B: Node (bundled helper, adds Range support)
node server.js
```

Then open `http://localhost:8000/` (Node helper prints the URL).

## Deploy to GitHub Pages

```bash
git init -b main
git add .
git commit -m "MSE energy-saver throttling test page"
# create the repo and push (GitHub CLI):
gh repo create mse-throttling-testpage --public --source=. --remote=origin --push
```

Then enable Pages: **repo Settings → Pages → Source: "Deploy from a branch" →
`main` / `/ (root)` → Save**. The page (`index.html`) is served at the repo's
Pages URL. `.nojekyll` is included so GitHub serves the files as-is.

## Bundled media

`bear-640x360-v_frag.mp4` and `test.mp4` are fragmented-MP4 test assets from the
Chromium / Web Platform Tests suites, included so the offline synthetic mode
works out of the box. They are covered by their upstream licenses (Chromium is
BSD-licensed; WPT assets are under the WPT license) — keep that in mind if you
redistribute.

## Notes

- Real players often fire one `waiting` event at startup, so the rebuffer count
  may begin at 1.
- This is a diagnostic/test tool, not an official Chromium artifact.
