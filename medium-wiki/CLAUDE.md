# CLAUDE.md — medium-wiki

## Tujuan Wiki

Menyintesis riset lintas sumber (akademis + jurnalistik) untuk artikel Medium
bergaya historical journalism ala Rama Anindya. Wiki ini adalah working memory
riset: begitu satu topik/peristiwa historis pernah diriset, sintesisnya reusable
untuk artikel-artikel berikutnya yang bersinggungan — bukan diriset ulang dari nol.

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend, bukan
menggantikan.

## Struktur Direktori

- `raw/` — artikel, PDF, buku (scan/epub), transkrip dokumenter. Immutable.
- `wiki/entities/` — tokoh historis, organisasi, institusi
- `wiki/concepts/` — tema, gerakan, ide besar
- `wiki/sources/` — ringkasan per-sumber
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Field tambahan khusus domain ini:
- `era` — rentang waktu yang relevan (mis. "1945-1949")
- `credibility` — primary / secondary / tertiary source

## Alur INGEST

1. Baca sumber baru dari `raw/`
2. Diskusikan poin kunci dan relevansinya dengan riset yang sedang berjalan bareng user
3. Tulis ringkasan di `wiki/sources/`, sitasi halaman/paragraf spesifik kalau memungkinkan
4. Update `wiki/index.md`
5. Update/buat halaman entities & concepts terkait
6. Log ke `wiki/log.md`: `## [YYYY-MM-DD] ingest | Judul Sumber`

## Alur QUERY

Baca `index.md` → drill ke halaman relevan → sintesis dengan sitasi APA-adjacent →
kalau hasilnya draft/outline artikel yang berharga, tawarkan simpan sebagai
`wiki/concepts/` atau halaman baru sebelum di-drop begitu saja ke chat.

## Alur LINT

Jalankan tiap ~15 ingest atau sebelum mulai artikel baru. Fokus tambahan: sumber
sejarah sering saling kontradiksi soal tanggal/fakta — **tandai eksplisit sebagai
kontradiksi, jangan resolve sepihak**, biarkan user yang memutuskan versi mana
yang dipakai di artikel.

## Gaya Penulisan

- Third-person, historical journalism
- Tanpa narasi personal ("saya", opini penulis) di halaman wiki — itu ranah draft
  artikel, bukan wiki
- Referensi APA-adjacent: `(Nama, Tahun)` inline, daftar sumber lengkap di
  frontmatter `sources`
- Bedakan eksplisit: fakta terverifikasi vs. interpretasi/spekulasi historiografis
