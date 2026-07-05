# CLAUDE.md — career-wiki

## Tujuan Wiki

Satu sumber kebenaran untuk progress roadmap karier AI Engineering (Python →
backend → RAG chatbot → job readiness) dan riset tooling (n8n, Flowise, Dify,
dst) yang biasanya menyebar di banyak sesi chat berbeda dan gampang hilang.

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend, bukan
menggantikan.

## Struktur Direktori

- `raw/` — catatan course, dokumentasi tool, hasil riset roadmap.sh, job posting
  yang jadi acuan skill gap. Immutable.
- `wiki/entities/` — tools & platform (n8n, Flowise, Dify, dst), course/provider
- `wiki/concepts/` — topik/skill (RAG, embeddings, vector DB, backend patterns, dst)
- `wiki/sources/` — ringkasan per-sumber
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`
- `wiki/progress.md` — tracker checklist roadmap (khusus domain ini, lihat di bawah)

## progress.md (tambahan khusus domain ini)

Checklist linear roadmap dengan status per milestone (`todo|in-progress|done`) +
tanggal update terakhir + link ke concept/entity page terkait. Ini **bukan**
pengganti `index.md` — index.md tetap katalog semua halaman, progress.md adalah
tampilan roadmap/timeline.

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Field tambahan: `skill_level` (beginner/intermediate/advanced) untuk halaman concept

## Alur INGEST

1. Baca sumber baru (catatan course/dokumentasi tool/hasil riset)
2. Update halaman tool/concept terkait
3. Update `wiki/progress.md` kalau sumber ini menandai milestone selesai/berubah
4. Update `index.md`, log ke `log.md`

## Alur QUERY

Baca `index.md` dan `progress.md` dulu (progress.md sering jadi entry point untuk
"aku sekarang di mana"), drill ke halaman relevan, sintesis dengan sitasi.

## Alur LINT

Cek tambahan: tool yang disebut di banyak sumber tapi belum ada halaman entity-nya
sendiri; milestone di `progress.md` yang statusnya stale (tidak ada update dalam
waktu lama padahal ditandai in-progress).

## Gaya Penulisan

Ringkas, actionable, second-person oke di sini ("kamu perlu...") karena ini
working notes, bukan artikel publik. Prioritaskan practical takeaway di atas teori.
