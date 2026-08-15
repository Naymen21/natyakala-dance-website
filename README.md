# Natyakala Dance School

Concept website for **Natyakala Dance School** — an Indian classical dance school in Minneapolis (Bharatanatyam, Mohiniyattam, Kathakali) directed by Sona Nair.

**Live:** https://natyakala-dance-website.vercel.app

## View it

Open `index.html` directly in a browser, or serve the folder:

```bash
python -m http.server 8000
# then open http://localhost:8000
```

## Deploying

This repo is the **single source of truth** for the live site. Vercel is connected
to it and deploys `main` to production automatically — push and it goes live in
seconds. There is no build step: the tree is one static `index.html`.

```bash
git push origin main    # that's the whole deploy process
```

Check a deploy landed:

```bash
gh api repos/Naymen21/natyakala-dance-website/commits/main/status --jq .state
```

⚠️ **Commit email matters.** Vercel refuses to build a commit whose author email
doesn't resolve to a GitHub account, failing with *"Deployment was blocked"* — the
site silently stays on the previous version. Commit as:

```
Naymen21 <100360630+Naymen21@users.noreply.github.com>
```

This is set globally, so it should just work; if a deploy is ever blocked, check
`git log -1 --format='%ae'` first.

## What's here

| Path | What |
|------|------|
| `index.html` | The entire site — self-contained (all CSS, JS, and images embedded). |
| `assets/` | Original source images (dancer cutouts + the hero composite) kept for future edits. |
| `assets/logo/` | The Nataraja mark in three cuts — whole, ring only, dancer only — cropped to the artwork and downscaled to 1024px. Re-encode from these if the logo ever needs resizing. |

The site is a single file on purpose: it's a shareable concept demo. Every image is embedded as a data URI so it works with no server and no external requests.

## Features

- Nataraja intro/loading animation — the flame ring turns around a still dancer — then a stage-curtain reveal
- Custom lagging cursor (desktop)
- Layered parallax hero with the three-dancer composite
- Light / dark theme toggle (persists via `localStorage`)
- Curriculum cards with cutout dancers overflowing the frames
- Full photo gallery (103 photos) with masonry layout + lightbox
- "Stages we've graced" marquee (Flower TV, Minnesota Malayalee Association, KHMN, Indian Association of Minnesota)
- Trial-class request form with client-side anti-spam (honeypot, time gate, per-device cooldown)
- Google Maps location section
- Responsive down to phones; respects `prefers-reduced-motion`

## Before going live (production TODO)

This is a **demo**. For the real site:

1. **Serve images as files**, not embedded data URIs. The full album (~226 unique photos) is too large to embed; host them in an image folder / CDN and load responsively. Demo currently embeds 103 downscaled copies.
2. **Wire the trial form to a real backend** (e.g. a small API route + email/SMS via Resend/Twilio) with server-side rate limiting and an admin toggle so Sona can pause notifications. Client-side anti-spam alone is bypassable.
3. **Replace the three adult dancer cutouts** — they came from a reference image and should be Natyakala's own photos.
4. **Add a real director photo** (current portrait slot is a placeholder; the "Profile1" file is a text bio, not a headshot).
5. **Embed a live Google Maps iframe** (demo shows a styled placeholder panel).
6. Add the hero **video montage** in place of the video slot.

## Notes

- Rebuilding as a proper **Next.js** project is the recommended path for the above (form backend, image hosting, real map/video).
- Album source photos are **not** committed here (too large for git).
