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
    │   └── cursor_paper.pdf         # paper PDF
    └── videos/
        └── abigot-demo-video.mp4    # shared placeholder used in all 3 video slots
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
10. **Citation** (numbered section 6) — BibTeX block with Copy button.
11. **Footer**.

## Styling

- **Reference:** [DreamDojo project page](https://dreamdojo-world.github.io/)
  for layout (numbered sections, colored heading underline).
- **Theme:** light page, indigo accent (`#4f46e5`).
- **Section heading underline + primary pill + Copy button:** navy → violet
  gradient (`linear-gradient(90deg, #1e3a8a, #7c3aed)`).
- **Tokens** in `static/css/style.css`: `--accent`, `--accent-hover`,
  `--accent-gradient`. Swap these to recolor the entire site in one place.

## Videos (placeholder phase)

All three `<video>` slots reference the **same** file
`static/videos/abigot-demo-video.mp4` (~57 MB) until per-section real clips
are produced. Each section keeps its own distinct `<h3>`, caption, and anchor:

| Slug                          | Anchor                 | Final content                                                            |
| ----------------------------- | ---------------------- | ------------------------------------------------------------------------ |
| `x2_deployed_policy`          | `#video-x2`            | AgiBot X2 Ultra running the deployed Sonic-family policy on hardware.    |
| `motion_retargeting`          | `#video-retargeting`   | Retargeting onto X2 kinematics; arm-flip / wrist-clamp fixes.            |
| `mujoco_real_sim_sidebyside`  | `#video-sim2real`      | Side-by-side replay in MuJoCo of matched real vs sim from one checkpoint.|

`<video preload="metadata">` keeps initial page weight tiny — bytes only
download on play, and the browser caches after first play so all three
players reuse the same blob.

### Optional: shrink the placeholder before pushing

```bash
ffmpeg -i abigot-demo-video.mp4 -vf "scale=-2:720" \
       -c:v libx264 -crf 28 -c:a aac -b:a 96k \
       abigot-demo-video-720p.mp4
```

This typically drops a 57 MB clip to ~10–20 MB.

### Replacing with real per-section clips

1. Drop the new file in `static/videos/<slug>.mp4` (and optional poster
   `static/images/<slug>_poster.jpg`).
2. In `index.html`, change that section's `<source src="…">` to point at
   the new file. Keep the `<h3>` and caption — the heading is what gives
   the section meaning, not the bytes.

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

### Large video file

`static/videos/abigot-demo-video.mp4` is ~55 MB; GitHub warns above 50 MB but
accepts the push. To avoid warnings, compress before rsync (see
[Optional: shrink the placeholder](#optional-shrink-the-placeholder-before-pushing))
or use Git LFS on the Pages repo only.

## Local preview

```bash
cd paper_project_page
python3 -m http.server 8000
# open http://localhost:8000/
```

## Future updates (not in scope for first ship)

- Replace the three placeholder videos with their real per-section clips.
- Add a CI workflow (`.github/workflows/pages.yml`) in the Pages repo if
  preferred over deploy-from-branch.
- Update the BibTeX `@misc{...}` once the paper has an arXiv ID.
- Confirm legal clearance on any hardware footage before public push.
