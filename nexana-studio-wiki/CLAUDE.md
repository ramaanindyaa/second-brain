# CLAUDE.md — nexana-studio-wiki

## Tujuan Wiki

Domain khusus untuk brand identity, positioning, dan materi bisnis milik
**Nexana Studio sendiri** (studio freelance WordPress/digital partner untuk
UMKM retail) — terpisah dari [client-wiki](../client-wiki/CLAUDE.md) yang
melacak konteks per-klien eksternal (brief, riset kompetitor, keputusan
desain per proyek klien, mis. WKM, PriceCal, dst).

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend,
bukan menggantikan.

## Kenapa Domain Terpisah dari client-wiki

Materi ini awalnya sempat di-ingest ke `client-wiki/wiki/entities/studio/`,
lalu dipindah ke domain sendiri karena:
- Nexana Studio adalah entitas milik sendiri (penyedia jasa), bukan klien
  eksternal — mencampurnya di dalam client-wiki bikin kategori "klien" vs
  "diri sendiri" ambigu tiap kali lint/ingest berikutnya jalan
- client-wiki sensitif dan sebagian di-gitignore (data klien); brand
  identity milik sendiri tidak butuh proteksi yang sama
- Domain sendiri bisa berkembang independen: paket layanan, materi
  marketing, roadmap bisnis studio — bukan cuma satu halaman entity di
  tengah wiki klien

## Struktur Direktori

- `raw/` — dokumen brand identity, riset kompetitor jasa sejenis, draft copy
  marketing, dst. Immutable.
- `wiki/entities/` — entitas terkait studio (nama/brand, tools/stack yang
  dipakai, paket layanan)
- `wiki/concepts/` — positioning framework, messaging pillars, brand
  voice/tone guidelines
- `wiki/sources/` — ringkasan per-sumber
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Tandai eksplisit status tiap elemen brand: `decided` vs `draft/candidate` —
banyak keputusan brand (tagline, warna final, logo) sengaja belum final di
sumber pertama, jangan biarkan itu terlihat seperti keputusan settled.

## Alur INGEST

1. Baca sumber baru dari `raw/`
2. Update/buat halaman entity (mis. detail brand) atau concept (mis.
   positioning framework, paket layanan) terkait
3. Update `wiki/index.md`
4. Log ke `wiki/log.md`

## Alur QUERY

Baca `index.md` → drill ke entity/concept relevan → sintesis dengan sitasi.

## Alur LINT

Cek tambahan: elemen brand yang ditandai "belum final" (tagline, warna,
logo) — pastikan tetap ditandai jelas sampai user mengonfirmasi keputusan
final. Jangan biarkan ingest/lint berikutnya diam-diam memperlakukannya
sebagai fakta settled.

## Gaya Penulisan

Ringkas, faktual, tandai jelas mana yang sudah final vs draft/kandidat.
