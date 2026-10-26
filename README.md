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
