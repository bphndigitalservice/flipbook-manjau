# Flipbook Manjau Embed Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a minimal static site that full-screen embeds `https://laporan.bphn.go.id/bookcase/vspfe`, with a timeout fallback and Vercel-ready config.

**Architecture:** Three root files only — `index.html` (fullscreen iframe + best-effort overlay fallback), `vercel.json` (static root), `README.md` (local open + GitHub → Vercel). No framework, no build, no dependencies.

**Tech Stack:** HTML5, CSS, vanilla JavaScript, Vercel static hosting.

**Spec:** `docs/superpowers/specs/2026-10-26-flipbook-embed-design.md`

## Global Constraints

- Embed URL exactly: `https://laporan.bphn.go.id/bookcase/vspfe`
- Layout: full-screen embed only — no header/nav
- Document title: `Flipbook Manjau`
- Fallback: overlay after ~3 seconds with message + “Buka di tab baru” (`target="_blank"`, `rel="noopener noreferrer"`)
- Stack: plain HTML/CSS/JS — no framework, no build, no npm
- Manual verification only (no test runner in scope)
- Repo may need `git init` before the first commit

## File map

| File | Responsibility |
|------|----------------|
| `index.html` | Fullscreen iframe, CSS reset/viewport fill, timeout overlay fallback |
| `vercel.json` | Tell Vercel this is a static root site |
| `README.md` | Local open, iframe-block caveat, GitHub → Vercel steps |

---

### Task 1: Fullscreen embed page + fallback

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: none
- Produces: root page at `/` (or `index.html` when opened as a file) that loads the BPHN URL in an iframe and may show `#fallback` after 3000ms

- [ ] **Step 1: Create `index.html` with fullscreen iframe and fallback overlay**

Create `index.html` with this exact content (load-timeout heuristic: overlay only if `load` never fires within 3s, or on `error`):

```html
<!DOCTYPE html>
<html lang="id">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Flipbook Manjau</title>
    <style>
      *,
      *::before,
      *::after {
        box-sizing: border-box;
      }

      html,
      body {
        margin: 0;
        height: 100%;
        overflow: hidden;
        background: #111;
        font-family: system-ui, sans-serif;
      }

      #book {
        display: block;
        width: 100%;
        height: 100%;
        border: 0;
      }

      #fallback {
        display: none;
        position: fixed;
        inset: 0;
        z-index: 10;
        align-items: center;
        justify-content: center;
        padding: 1.5rem;
        background: rgba(17, 17, 17, 0.92);
        color: #f5f5f5;
        text-align: center;
      }

      #fallback.is-visible {
        display: flex;
      }

      #fallback p {
        margin: 0 0 1rem;
        max-width: 28rem;
        line-height: 1.45;
      }

      #fallback a {
        display: inline-block;
        padding: 0.75rem 1.25rem;
        border-radius: 0.375rem;
        background: #f5f5f5;
        color: #111;
        text-decoration: none;
        font-weight: 600;
      }

      #fallback a:focus-visible {
        outline: 2px solid #fff;
        outline-offset: 3px;
      }
    </style>
  </head>
  <body>
    <iframe
      id="book"
      title="Laporan BPHN — bookcase VSPFE"
      src="https://laporan.bphn.go.id/bookcase/vspfe"
      allowfullscreen
    ></iframe>

    <div id="fallback" role="dialog" aria-live="polite" aria-label="Embed tidak tersedia">
      <div>
        <p>
          Flipbook tidak bisa ditampilkan di halaman ini (kemungkinan situs BPHN
          memblokir embed). Buka langsung di tab baru:
        </p>
        <a
          href="https://laporan.bphn.go.id/bookcase/vspfe"
          target="_blank"
          rel="noopener noreferrer"
          >Buka di tab baru</a
        >
      </div>
    </div>

    <script>
      (function () {
        var FALLBACK_MS = 3000;
        var book = document.getElementById("book");
        var fallback = document.getElementById("fallback");
        var settled = false;
        var loaded = false;

        function showFallback() {
          if (settled) return;
          settled = true;
          fallback.classList.add("is-visible");
        }

        book.addEventListener("error", showFallback);
        book.addEventListener("load", function () {
          loaded = true;
        });

        window.setTimeout(function () {
          if (!loaded) showFallback();
        }, FALLBACK_MS);
      })();
    </script>
  </body>
</html>
```

Note: some browsers still fire `load` on a framed error/about:blank page when the remote site refuses framing. If that happens in production, treat it as known limitation of best-effort detection (spec); the “Buka di tab baru” link remains available once overlay shows, and users can always open the README URL.

- [ ] **Step 2: Manual verify — fullscreen iframe**

Run (PowerShell, from repo root):

```powershell
Start-Process "index.html"
```

Expected:
- Browser tab title is `Flipbook Manjau`
- Iframe fills the entire viewport (no page chrome/header)
- No double scrollbars on the outer page

- [ ] **Step 3: Manual verify — fallback path**

Temporarily change the iframe `src` to an invalid URL (e.g. `https://127.0.0.1:9/`) so `load` never succeeds, save, reload.

Expected within ~3 seconds:
- Overlay visible with the Indonesian message
- “Buka di tab baru” opens `https://laporan.bphn.go.id/bookcase/vspfe` in a new tab

Restore `src` to `https://laporan.bphn.go.id/bookcase/vspfe` before committing.

- [ ] **Step 4: Init git (if missing) and commit**

```powershell
if (-not (Test-Path .git)) { git init }
git add index.html
git commit -m "feat: add fullscreen BPHN flipbook embed with fallback"
```

---

### Task 2: Vercel config + README

**Files:**
- Create: `vercel.json`
- Create: `README.md`

**Interfaces:**
- Consumes: `index.html` at repo root (Task 1)
- Produces: Vercel-ready static project docs and config

- [ ] **Step 1: Create `vercel.json`**

```json
{
  "version": 2,
  "cleanUrls": true,
  "trailingSlash": false
}
```

This keeps a plain static root deploy. No `builds`/`functions` keys.

- [ ] **Step 2: Create `README.md`**

```markdown
# Flipbook Manjau

Halaman statis fullscreen yang menampilkan laporan BPHN:

https://laporan.bphn.go.id/bookcase/vspfe

## Buka lokal

Buka `index.html` di browser, atau dari root repo:

```powershell
Start-Process index.html
```

## Catatan embed

Beberapa situs memblokir iframe (`X-Frame-Options` / CSP `frame-ancestors`).
Jika konten tidak muncul, halaman menampilkan pesan dan tombol **Buka di tab baru** setelah sekitar 3 detik.

## Deploy: GitHub → Vercel

1. Push repo ini ke GitHub.
2. Di [Vercel](https://vercel.com): **Add New Project** → import repo.
3. Framework Preset: **Other** (static). Root directory: `.`
4. Deploy. URL production akan menyajikan `index.html`.
```

- [ ] **Step 3: Manual verify — files present and JSON valid**

```powershell
Get-Content vercel.json | ConvertFrom-Json | Format-List
Test-Path README.md
Test-Path index.html
```

Expected: JSON parses without error; all three files exist.

- [ ] **Step 4: Commit**

```powershell
git add vercel.json README.md
git commit -m "chore: add Vercel static config and README"
```

---

## Self-review (author checklist)

| Spec requirement | Task |
|------------------|------|
| Fullscreen embed, no header | Task 1 |
| Exact BPHN URL | Task 1 |
| Title `Flipbook Manjau` | Task 1 |
| ~3s overlay + Buka di tab baru | Task 1 |
| `vercel.json` static root | Task 2 |
| README local + caveat + GitHub→Vercel | Task 2 |
| Manual testing | Steps 2–3 Task 1, Step 3 Task 2 |
| No framework/build | Global + both tasks |

No placeholders. Task 1 Step 1 has a single final `index.html` (load-timeout + error).
