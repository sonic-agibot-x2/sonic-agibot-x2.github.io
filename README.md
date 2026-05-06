# Sonic-on-AgiBot-X2 — Project Landing Page

Static, single-page site for the paper *Porting NVIDIA Sonic to the AgiBot X2
Ultra: A Sim-to-Sim-to-Real Bridge for a Non-G1 Humanoid*.

This folder is **staged inside the GR00T-WholeBodyControl monorepo** for
convenience but is meant to be deployed as the **root** of a separate
GitHub Pages user/organization repository:

> https://github.com/sonic-agibot-x2/sonic-agibot-x2.github.io  →
> served at https://sonic-agibot-x2.github.io/

**Deploying a new version:** edit files under `paper_project_page/` in this
monorepo, then run the **rsync + git commit + push** sequence in
[Deploying to sonic-agibot-x2.github.io](#deploying-to-sonic-agibot-x2githubio)
below. The GitHub Pages repo should contain the **same files as this folder**
at its **repository root** (`index.html`, `static/`, `.nojekyll`) — not a
subfolder — so the site URL paths keep working.

## Layout

```
paper_project_page/
├── index.html                       # single-page site
├── .nojekyll                        # opt out of Jekyll on GitHub Pages
├── README.md                        # this file
└── static/
    ├── css/
    │   └── style.css                # all layout + DreamDojo-style indigo theme
    ├── images/
    │   ├── f1_pipeline.png          # original (paper) pipeline figure (kept for reference)
    │   └── f1_pipeline_web.svg      # bright web-themed pipeline diagram (hand-authored SVG, ~8 KB)
    ├── pdfs/
    │   └── sonic-agibot-x2-port.pdf # paper PDF (renamed from cursor_paper.pdf)
    └── videos/
        ├── x2-normal-walk-mocap-sim-real.mp4          # Video 1 (top): mocap | mujoco | real, natural walk
        ├── x2-ablation-tests-run1-vs-run2.mp4         # Video 2 (mid): foot-collision URDF ablation
        ├── x2-compare-real-sim-motions-with-plots.mp4 # Video 3 (end): real vs sim + joint plots
        ├── x2-sonic-balancing-on-one-leg.mp4          # Bonus 1 (More demos): single-leg balance pose
        └── x2-sonic-manipulation-gestures.mp4         # Bonus 2 (More demos): upper-body / arm gestures (fingers held at default)
```

## Page structure (top → bottom)

1. **Hero** — title, authors, pill row (Paper · Code · Videos · Citation).
2. **Video 1** — *Real robot deployment* (`#video-x2`, full-width hero clip).
3. **Abstract** (numbered section 1).
4. **Pipeline figure** (numbered section 2; `f1_pipeline_web.svg`).
5. **Key Takeaways 1–3** (numbered section 3): sim-to-sim bridge,
   observation/encoding bugs, dual-publisher whir → 0.20 s.
6. **Video 2** — *Motion retargeting* (`#video-retargeting`).
7. **Sim-to-Real Anchor Archive** (numbered section 4) — written context
   for Video 3.
8. **Video 3** — *Real vs sim in MuJoCo* (`#video-sim2real`), paired with
   the anchor-archive prose.
9. **Failure-Mode Taxonomy** (numbered section 5) — closing.
10. **More demos** (numbered section 6; `#more-demos`) — 2-up grid with the
    bonus single-leg-balance and upper-body-manipulation clips. The
    **Videos** pill in the hero anchors here.
11. **Citation** (numbered section 7) — BibTeX block with Copy button.
12. **Footer**.

## Styling

- **Reference:** [DreamDojo project page](https://dreamdojo-world.github.io/)
  for layout (numbered sections, colored heading underline).
- **Theme:** light page, indigo accent (`#4f46e5`).
- **Section heading underline + primary pill + Copy button:** navy → violet
  gradient (`linear-gradient(90deg, #1e3a8a, #7c3aed)`).
- **Tokens** in `static/css/style.css`: `--accent`, `--accent-hover`,
  `--accent-gradient`. Swap these to recolor the entire site in one place.

## Videos

Five real clips drive the page (no placeholders). The first three are
inline and interleaved with the prose; the last two live in the
**More demos** grid near the bottom and are also where the **Videos**
pill anchors. Each video lives in its own `<section class="video-block">`
(or `<figure class="video-block video-block--grid">` inside the grid)
with a distinct `<h3>`, caption, and anchor:

| File                                            | Anchor              | What it shows                                                                                                                                |
| ----------------------------------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `x2-normal-walk-mocap-sim-real.mp4`             | `#video-x2`         | Three-panel natural walk: retargeted mocap (left) → MuJoCo checkpoint (mid) → real X2 hardware (right). 22 MB.                                |
| `x2-ablation-tests-run1-vs-run2.mp4`            | `#video-ablation`   | 2×3 grid: top row = run 1 (2k/6k/16k checkpoints, all collapse <6 s); bottom row = run 2 with foot-collision URDF fix (all complete). 9.2 MB. |
| `x2-compare-real-sim-motions-with-plots.mp4`    | `#video-sim2real`   | Left: real (solid) and sim (shaded) walking side-by-side in MuJoCo. Right: lower-body joint trajectories overlaid. 14 MB.                     |
| `x2-sonic-balancing-on-one-leg.mp4`             | `#video-balance`    | Bonus (More demos): single-leg static balance under the deployed controller — out-of-distribution stance test. Portrait clip, 1.8 MB.         |
| `x2-sonic-manipulation-gestures.mp4`            | `#video-manip`      | Bonus (More demos): coordinated arm, shoulder, and torso gestures while the lower body holds stance. Dexterous hands not commanded in this clip. Portrait clip, 4.8 MB. |

Pages-relevant attributes used on every `<video>`:

- `controls playsinline muted loop preload="metadata" autoplay` —
  autoplay-on-scroll works because of `muted`; manual pause is preserved
  across scroll events (see the `IntersectionObserver` script in
  `index.html`). A one-time `touchstart` / `click` fallback in the same
  script kicks in-view videos for mobile browsers that gate autoplay
  behind a user gesture.
- `preload="metadata"` keeps initial page weight low; bytes only download
  when each clip enters the viewport.

### Replacing or adding clips later

1. Drop the new file in `static/videos/<name>.mp4` (and optional poster
   `static/images/<name>_poster.jpg`).
2. In `index.html`, change that section's `<source src="…">` to point at
   the new file. Keep the `<h3>` and caption — the heading is what gives
   the section meaning, not the bytes.

### Optional: re-encode to shrink size

```bash
ffmpeg -i input.mp4 -vf "scale=-2:720" \
       -c:v libx264 -crf 28 -c:a aac -b:a 96k \
       output-720p.mp4
```

The current set is ~52 MB total, all per-file well under the 50 MB
GitHub-recommended ceiling. The two More-demos clips were originally
HEVC Main 10 (10-bit yuv420p10le) phone captures that browsers cannot
decode in `<video>`; they have been re-encoded to H.264 8-bit yuv420p
at 720p with `-an -movflags +faststart` for browser playback.

## Deploying to sonic-agibot-x2.github.io

The [Pages repository](https://github.com/sonic-agibot-x2/sonic-agibot-x2.github.io)
must mirror **this directory’s contents** at the **repo root** (so
`https://sonic-agibot-x2.github.io/static/...` resolves). Do not commit only
`index.html` — copy the whole tree including `static/` and `.nojekyll`.

Set these to match your machine (examples use this repo and a sibling clone):

| Variable | Example |
| -------- | ------- |
| `MONO` | Path to this monorepo, e.g. `~/projects/GR00T-WholeBodyControl` |
| `PAGES` | Path to a local clone of `sonic-agibot-x2.github.io` |

### One-time: clone the Pages repo

```bash
git clone git@github.com:sonic-agibot-x2/sonic-agibot-x2.github.io.git \
  ~/projects/sonic-agibot-x2.github.io
```

(Use HTTPS if SSH is not set up:
`https://github.com/sonic-agibot-x2/sonic-agibot-x2.github.io.git`)

### Every deploy: rsync, commit, push

From your machine, after changing anything under `paper_project_page/`:

```bash
MONO=~/projects/GR00T-WholeBodyControl
PAGES=~/projects/sonic-agibot-x2.github.io

rsync -av --delete --exclude='.git' \
  "$MONO/paper_project_page/" \
  "$PAGES/"

cd "$PAGES"
git add -A
git status   # sanity check
git commit -m "Update project landing page"
git push origin main
```

- **`rsync --delete`** removes files in the Pages repo that you removed from
  `paper_project_page/` (keeps the mirror accurate).
- **`--exclude='.git'`** avoids touching the Pages repo’s `.git` directory.

### GitHub Pages settings

In the **sonic-agibot-x2.github.io** repo: **Settings → Pages →** source
**Deploy from a branch**, branch **`main`**, folder **`/ (root)`**.  
Live site: **https://sonic-agibot-x2.github.io/** (may take a minute after push).

### Browser codec gotcha (HEVC / 10-bit)

If a clip plays in your local video player but renders as a **black
frame inside a working `<video>` element** (controls move, duration is
known, no pixels), the file is almost certainly HEVC / H.265 (often
Main 10 / 10-bit). Browsers do not decode that in `<video>`. Confirm
with `ffprobe`:

```bash
ffprobe -v error -select_streams v:0 \
  -show_entries stream=codec_name,profile,pix_fmt \
  -of default=nw=1 path/to/clip.mp4
```

If you see `codec_name=hevc` or `pix_fmt=yuv420p10le`, re-encode to
H.264 8-bit:

```bash
ffmpeg -i input.mp4 -vf "scale=-2:720" \
       -c:v libx264 -preset slow -crf 23 -pix_fmt yuv420p \
       -an -movflags +faststart \
       output.mp4
```

`yuv420p` (8-bit) and `libx264 High`/`Main` profile are the
universally browser-safe combo.

### Large video file

All current clips are under the 50 MB GitHub-recommended ceiling. If
a future clip exceeds it, re-encode (see
[Optional: re-encode to shrink size](#optional-re-encode-to-shrink-size))
or move large MP4s to Git LFS on the Pages repo.

## Local preview

```bash
cd paper_project_page
python3 -m http.server 8000
# open http://localhost:8000/
```

## Future updates (not in scope for first ship)

- Add a CI workflow (`.github/workflows/pages.yml`) in the Pages repo if
  preferred over deploy-from-branch.
- Update the BibTeX `@misc{...}` once the paper has an arXiv ID.
- Confirm legal clearance on any hardware footage before public push.
