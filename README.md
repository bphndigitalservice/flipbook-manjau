# Flipbook Manjau

Halaman statis fullscreen yang menampilkan flipbook:

https://fliphtml5.com/bookcase/vspfe/

## Buka lokal

Buka `index.html` di browser, atau dari root repo:

```powershell
Start-Process index.html
```

Atau serve folder ini lalu buka `http://localhost:8000/` (bukan `/index.html`).

## Deploy: GitHub → Vercel

1. Push repo ini ke GitHub.
2. Di [Vercel](https://vercel.com): **Add New Project** → import repo.
3. Framework Preset: **Other** (static). Root directory: `.`
4. Deploy. URL production menyajikan `/` dari `index.html`.
