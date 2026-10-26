# Flipbook Manjau — static embed design

**Date:** 2026-10-26  
**Status:** Approved — plan in `docs/superpowers/plans/2026-10-26-flipbook-embed.md`  
**Repo:** `flipbook-manjau` (greenfield)

## Goal

A minimal static site that full-screen embeds  
`https://laporan.bphn.go.id/bookcase/vspfe`, suitable for local open now and later GitHub → Vercel.

## Decisions

| Topic | Choice |
|-------|--------|
| Layout | Full-screen embed only (no header/nav) |
| Hosting path | Simple static files; push to GitHub, serve on Vercel later |
| Blocked iframe | Overlay after ~3s with message + “Buka di tab baru” |
| Stack | Plain HTML/CSS/JS — no framework, no build |

## Project structure

```
flipbook-manjau/
  index.html
  vercel.json
  README.md
  docs/superpowers/specs/2026-10-26-flipbook-embed-design.md
```

## Page behaviour (`index.html`)

- Viewport meta; `html, body` and iframe fill the viewport (`100%` width/height; no double scrollbars).
- Single iframe, `src` = the BPHN bookcase URL above.
- iframe: descriptive `title`, `allowfullscreen`, no border.
- Document title: `Flipbook Manjau`.
- After ~3 seconds, if embed still appears blocked/unavailable, show a simple overlay with short copy and a button that opens the same URL in a new tab (`target="_blank"`, `rel="noopener noreferrer"`).
- Detection of cross-origin iframe blocking is best-effort (timeout-based); not guaranteed 100% accurate.

## Deploy config (`vercel.json`)

- Static site from project root; no serverless functions or env vars.
- Minimal config so Vercel serves `index.html` at `/`.

## Docs (`README.md`)

- How to open locally.
- Note that the BPHN site may block framing (`X-Frame-Options` / CSP).
- Short GitHub → Vercel import steps.

## Error handling & testing

**Edges**

- Iframe blocked or empty → overlay + open-in-new-tab.
- JS disabled → iframe still present; overlay may not appear.
- Mobile → same fullscreen layout.

**Manual checks**

1. Open `index.html` locally — fullscreen iframe.
2. Confirm BPHN content or fallback + new-tab button.
3. After Vercel deploy — repeat on the production URL.

## Out of scope

- Custom branding/header, analytics, auth, CMS, build tooling.
- Guaranteeing embed when BPHN forbids framing (fallback is the mitigation).
