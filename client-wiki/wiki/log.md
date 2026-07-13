# Log — Client Wiki (Freelance WordPress)

Catatan kronologis semua aktivitas (ingest/query/lint). Format tiap entri:
`## [YYYY-MM-DD] <ingest|query|lint> | <judul>`

## [2026-07-05] init | Wiki diinisialisasi
Struktur 3-layer (raw/, wiki/, CLAUDE.md) dibuat mengikuti pola LLM Wiki
Karpathy. Belum ada sumber di-ingest.

## [2026-07-05] ingest (superseded) | Nexana Studio — Brand Identity Guide
Sempat di-ingest ke sini (entity di `wiki/entities/studio/nexana-studio.md`),
tapi user minta dipindah ke domain sendiri karena Nexana Studio adalah brand
milik sendiri, bukan klien eksternal. Lihat
`## [2026-07-05] moved-out` di bawah.

## [2026-07-05] moved-out | Nexana Studio dipindah ke nexana-studio-wiki
Raw source, entity page, dan source summary Nexana Studio dipindah keluar
dari client-wiki ke domain baru `../nexana-studio-wiki/`. index.md di sini
dikembalikan ke kondisi kosong. Kalau ada klien eksternal WordPress asli
(WKM, PriceCal, dst) yang mau di-ingest, itu tetap masuk ke sini di
`entities/clients/`.
