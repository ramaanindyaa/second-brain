# Log — Nexana Studio

Catatan kronologis semua aktivitas (ingest/query/lint). Format tiap entri:
`## [YYYY-MM-DD] <ingest|query|lint> | <judul>`

## [2026-07-05] init | Wiki diinisialisasi (dipindah dari client-wiki)
Domain ini dipindah keluar dari `client-wiki/wiki/entities/studio/` menjadi
domain sendiri, karena Nexana Studio adalah brand milik sendiri (bukan
klien eksternal) dan layak punya ruang berkembang independen.

## [2026-07-05] ingest | Nexana Studio — Brand Identity Guide
Sumber: `raw/Nexana_Studio_Brand_Identity.md` (dipindah dari
`client-wiki/raw/`). Entity page: `wiki/entities/nexana-studio.md`. Source
summary: `wiki/sources/nexana-studio-brand-identity.md`. Item yang masih
terbuka di sumber (belum final): pilihan tagline, hex warna pasti, konsep
logo final, copy brand story final — ditandai eksplisit di entity page,
jangan dianggap keputusan final.

## [2026-07-13] ingest | Koleksi Plugin WordPress Premium
Sumber: `raw/Plugin_WordPress_Premium.md` (9 link folder Google Drive:
Elementor Pro, Wordfence Security, Yoast SEO, Tutor LMS Pro, Premium Addons
PRO for Elementor, Bonus Elementor, Bonus Plugin, Elements Kit for
Elementor, Unlimited Elements for Elementor). Entity page:
`wiki/entities/plugin-wordpress-premium.md`. Source summary:
`wiki/sources/plugin-wordpress-premium.md`. Belum ada keterangan
versi/tanggal update/lisensi per plugin di sumber.

## [2026-07-15] ingest | Nexana Studio — Pricelist
Sumber: `raw/Nexana_Studio_Pricelist.md`. Dipecah jadi 2 concept page karena
audiens beda: `wiki/concepts/paket-layanan-pricing.md` (struktur harga
Starter/Growth/Pro/retainer/a la carte/syarat bayar — aman dipakai ke
klien) dan `wiki/concepts/strategi-penjualan-internal.md` (4 taktik
anchoring/closing — sumber menandai eksplisit "jangan tampilkan ke klien",
internal only). Source summary: `wiki/sources/nexana-studio-pricelist.md`.
Item terbuka: add-on AI WhatsApp Assistant masih status draft/candidate
(belum diaktifkan); angka retainer lokal masih perlu divalidasi ulang lewat
2-3 klien retainer pertama. Dikonfirmasi dengan user sebelum menulis
(pemisahan 2 halaman pricing publik vs strategi internal).

## [2026-07-15] ingest | Update — Nexana Studio Pricelist (Local SEO Bulanan)
Sumber diperbarui: `raw/Nexana_Studio_Pricelist.md`. Section baru: add-on
retainer "Local SEO Bulanan" (Rp1,5-3jt/bulan) + poin validasi pasar
terkait. Ditambahkan ke `wiki/concepts/paket-layanan-pricing.md` (in-place,
bukan halaman baru) beserta catatan penting: SEO dasar sudah include
otomatis di paket Growth & Pro (bukan biaya tambahan), dan go-to-market
sengaja selektif — hanya ditawarkan ke client retainer existing, tidak
dijual berdiri sendiri sejak awal. Source summary
`wiki/sources/nexana-studio-pricelist.md` di-update. Bagian lain pricelist
tidak berubah.

## [2026-07-15] ingest | Update — Nexana Brand Identity (HTML brand guide)
Sumber baru: `raw/nexana_brand_identity.html`, versi HTML dari brand guide
yang isinya sebagian besar sama dengan `raw/Nexana_Studio_Brand_Identity.md`
tapi membawa beberapa keputusan yang lebih pasti. Dikonfirmasi final oleh
user: **tagline** = "Toko Anda, Sistemnya Kami." (dari 4 kandidat draft
lama), **tipografi** = Plus Jakarta Sans (headline) + Inter (body, narrowed
dari beberapa opsi draft), **palet warna** = navy `#1B2A4A`, amber
`#F2994A`, amber deep `#D9782F` (baru), cream `#F7F5F2`, charcoal `#2A2A28`
(menggantikan nilai draft lama `#2E2E2E` — dicatat sebagai discrepancy yang
sudah diresolve, HTML jadi otoritatif). Entity page
`wiki/entities/nexana-studio.md` diupdate in-place, section Nama & Tagline
dan Visual Identity kini ditandai **DECIDED** (sebelumnya "belum
diputuskan"/indikatif). Konsep logo & brand story TETAP draft — tidak
termasuk konfirmasi ini. Backfill juga: contoh voice do/don't dan detail
persona "toko fashion, kelontong modern, produk lokal" yang sebenarnya
sudah ada di `.md` lama tapi belum ke-extract sebelumnya. Source summary
`wiki/sources/nexana-studio-brand-identity.md` diupdate untuk cover kedua
file raw.

## [2026-07-15] lint | Cek konsistensi seluruh wiki
Temuan: (1) `overview.md` kontradiksi dengan entity page — masih menulis
tagline/warna sebagai draft padahal sudah DECIDED, dan masih bilang "1
sumber" padahal sudah 4; trigger update yang ditulis di overview.md sendiri
("saat tagline dipilih") sudah terpenuhi tapi belum dieksekusi — **fixed**,
overview.md ditulis ulang + timestamp dibump ke 2026-07-15. (2) Cross-ref
hilang: overview.md tidak link ke `concepts/paket-layanan-pricing.md`,
`concepts/strategi-penjualan-internal.md`, `entities/plugin-wordpress-premium.md`
— **fixed**, ditambahkan. (3) `entities/nexana-studio.md` bagian Catatan
Penerapan tidak link ke detail retainer di paket-layanan-pricing — **fixed**.
(4) `index.md` frontmatter timestamp stale (2026-07-05 padahal isi sudah
2x diupdate) — **fixed**, dibump ke 2026-07-15. Tidak ditemukan halaman
orphan; status draft/final elemen brand konsisten antara entity & source
page; log.md kronologis cocok dengan isi halaman.
