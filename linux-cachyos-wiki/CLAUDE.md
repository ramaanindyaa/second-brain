# CLAUDE.md — linux-cachyos-wiki

## Tujuan Wiki

Satu sumber kebenaran untuk proses belajar Linux dari nol di device dengan
CachyOS (Arch-based, fokus performa). Menampung catatan konsep Linux umum
(filesystem, permission, shell, systemd, dll), hal spesifik CachyOS (pacman/paru,
Btrfs + snapshot, kernel variants, scx scheduler, gaming/performance tweaks), serta
insiden troubleshooting yang biasanya hilang begitu sesi chat ditutup.

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend, bukan
menggantikan.

## Struktur Direktori

- `raw/` — catatan instalasi, output command (`neofetch`, `pacman -Q`, dll),
  screenshot config, artikel/dokumentasi Arch Wiki & CachyOS Wiki yang dibaca,
  log error dari troubleshooting. Immutable.
- `wiki/entities/` — tools & software (pacman, paru, btrfs-assistant, systemd,
  package tertentu), komponen sistem (kernel variant, desktop environment/WM)
- `wiki/concepts/` — topik/konsep (package management, snapshot & rollback,
  permission Linux, shell scripting, boot process, dll)
- `wiki/sources/` — ringkasan per-sumber
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`
- `wiki/progress.md` — tracker checklist belajar (khusus domain ini, lihat di bawah)

## progress.md (tambahan khusus domain ini)

Checklist milestone belajar Linux/CachyOS dengan status per milestone
(`todo|in-progress|done`) + tanggal update terakhir + link ke concept/entity
terkait. Ini **bukan** pengganti `index.md` — index.md tetap katalog semua
halaman, progress.md adalah tampilan roadmap belajar.

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Field tambahan: `skill_level` (beginner/intermediate/advanced) untuk halaman concept.

## Alur INGEST

1. Baca sumber baru (catatan instalasi, output command, artikel wiki, log error)
2. Update halaman entity/concept terkait (mis. kalau baru belajar snapshot Btrfs,
   update/buat concept `btrfs-snapshot.md`)
3. Update `wiki/progress.md` kalau sumber ini menandai milestone selesai/berubah
4. Update `index.md`, log ke `log.md`

## Alur QUERY

Baca `index.md` dan `progress.md` dulu (progress.md sering jadi entry point untuk
"aku sudah belajar apa saja"), drill ke halaman relevan, sintesis dengan sitasi.

## Alur LINT

Cek tambahan: command/tool yang disebut berulang di banyak sumber tapi belum
punya halaman entity sendiri; milestone di `progress.md` yang stale (ditandai
in-progress tapi tidak ada update lama); solusi troubleshooting yang mungkin
sudah usang (mis. karena update kernel/paket baru).

## Gaya Penulisan

Ringkas, actionable, second-person oke di sini ("kamu perlu jalankan...") karena
ini working notes untuk belajar, bukan artikel publik. Prioritaskan practical
takeaway (command yang benar-benar dipakai) di atas teori panjang. Kalau sumber
berisi command, selalu simpan command persisnya (bukan cuma deskripsi) supaya
bisa langsung dipakai lagi.
