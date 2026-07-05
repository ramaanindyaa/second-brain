# CLAUDE.md — client-wiki

## Tujuan Wiki

Menjaga konteks per-klien freelance WordPress (brand, copy, keputusan desain,
histori komunikasi) supaya tidak hilang antar sesi kerja — terutama untuk klien
yang proyeknya berjalan lama/berulang (mis. WKM, PriceCal, dst).

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend, bukan
menggantikan.

## PENTING: Sensitivitas Data

Folder ini bisa berisi data klien yang confidential (brief, harga, kontrak,
kredensial akses). `raw/` di domain ini sudah di-gitignore secara default (lihat
`.gitignore` di root vault). **Jangan** commit folder ini ke remote git publik.
Kalau vault ini nanti mau di-push ke GitHub, pastikan repo private atau data
sudah direview dulu.

## Struktur Direktori

- `raw/` — brief client, riset kompetitor, mockup/referensi desain, email/chat
  penting. Immutable.
- `wiki/entities/clients/` — satu halaman per klien
- `wiki/concepts/` — pola desain/copy yang reusable antar klien (bukan spesifik
  satu klien)
- `wiki/sources/` — ringkasan per-sumber
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Field tambahan untuk `entities/clients/`: `client_status` (active/paused/completed),
`stack` (tema WP/plugin yang dipakai)

## Alur INGEST

1. Baca brief/sumber baru
2. Update halaman klien terkait (**jangan buat halaman klien baru** kalau itu
   proyek/revisi dari klien existing)
3. Kalau ada pola desain/copy yang generalizable, ekstrak ke `wiki/concepts/`
   (reusable knowledge, bukan cuma catatan satu klien)
4. Update `index.md`, log ke `log.md`

## Alur QUERY

Baca `index.md` → buka halaman klien spesifik → sintesis dengan konteks brand
klien tersebut.

## Alur LINT

Cek tambahan: klien dengan status "active" tapi tidak ada aktivitas/update lama
(kemungkinan perlu follow-up); keputusan desain yang kontradiktif antar sesi
untuk klien yang sama.

## Gaya Penulisan

Ringkas dan praktis. Pisahkan jelas: fakta dari brief klien vs.
rekomendasi/opini kamu sendiri.
