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
| `index.html` | The whole site: all CSS, all JS, and the small fixed images (logo, favicon, hero composite, dance-form cutouts) embedded as data URIs. |
| `assets/` | Original source images (dancer cutouts + the hero composite) kept for future edits. |
| `assets/logo/` | The Nataraja mark in three cuts — whole, ring only, dancer only — cropped to the artwork and downscaled to 1024px. Re-encode from these if the logo ever needs resizing. |

One file on purpose — it is a shareable concept demo. The *fixed* art is embedded
so the chrome renders with no server; the *photo archive* is not, because it never
could be (see below).

## The photo gallery

The 266 archive photographs live in a **public Supabase Storage bucket**, not in
this file:

```
https://ezrzwjzyetpusijwditl.supabase.co/storage/v1/object/public/gallery/
  manifest.json     list of {n: filename, w, h}
  thumb/<name>.jpg  600px  — the masonry grid
  full/<name>.jpg   1800px — the lightbox
```

On load the page fetches `manifest.json`, then builds URLs from it. **The bucket is
public, so the page holds no Supabase key and makes no authenticated request** —
these are plain HTTPS GETs. Nothing to leak, nothing to rotate.

Why not embed them like everything else: 266 photos as base64 is ~350MB inline, and
even the earlier 103-photo subset made `index.html` 5.5MB that every visitor
downloaded in full before first paint. It is now 722KB and photos stream in lazily.

**To add or remove photos:** re-run the derive/upload scripts against a source
folder, or edit `manifest.json` in the bucket directly — dropping an entry there
removes it from the site with no re-upload and no code change.

If the manifest fetch fails the gallery shows a "could not be loaded" message
rather than a silently empty grid.

## Features

- Nataraja intro/loading animation — the flame ring turns around a still dancer — then a stage-curtain reveal
- Custom lagging cursor (desktop)
- Layered parallax hero with the three-dancer composite
- Light / dark theme toggle (persists via `localStorage`)
- Curriculum cards with cutout dancers overflowing the frames
- Full photo gallery (266 photos from Supabase) with masonry layout + lightbox
- "Stages we've graced" marquee (Flower TV, Minnesota Malayalee Association, KHMN, Indian Association of Minnesota)
- Trial-class request form with client-side anti-spam (honeypot, time gate, per-device cooldown)
- Responsive down to phones; respects `prefers-reduced-motion`

## Before going live (production TODO)

This is a **demo**. For the real site:

1. **Wire the trial form to a real backend** (e.g. a small API route + email/SMS via Resend/Twilio) with server-side rate limiting and an admin toggle so Sona can pause notifications. Client-side anti-spam alone is bypassable.
2. **Replace the three adult dancer cutouts** — they came from a reference image and should be Natyakala's own photos.
3. **Add a real director photo** (current portrait slot is a placeholder; the "Profile1" file is a text bio, not a headshot).
4. Add the hero **video montage** in place of the video slot.
5. **Decide how visitors find the studio.** The address/map section was cut; if the real
   site needs one, it has to come back with a live map rather than the placeholder panel.

## Notes

- Rebuilding as a proper **Next.js** project is the recommended path for the above (form backend, image hosting, real map/video).
- Album source photos are **not** committed here (269MB). They live in Supabase; the
  originals stay on Sona's drive.
