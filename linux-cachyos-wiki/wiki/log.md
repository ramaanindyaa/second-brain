# Log — Linux & CachyOS

Catatan kronologis semua aktivitas (ingest/query/lint). Format tiap entri:
`## [YYYY-MM-DD] <ingest|query|lint> | <judul>`

## [2026-07-19] init | Wiki diinisialisasi
Struktur 3-layer (raw/, wiki/, CLAUDE.md) dibuat mengikuti pola LLM Wiki
Karpathy. Domain baru karena user baru mulai pakai CachyOS di device-nya.
Belum ada sumber di-ingest.

## [2026-07-19] ingest | cachyos-wiki.md — Setup HP Pavilion Plus 14
Sumber pertama: dokumentasi setup CachyOS lengkap (dual boot, instalasi,
post-install, fish/TLP/thermald/UFW/snapper, KDE theming, aplikasi,
maintenance, troubleshooting, rencana backup). Breakdown pages dikonfirmasi
user dulu sebelum ditulis (14 halaman + 1 source page).

Dibuat: 9 entity (hp-pavilion-plus-14, cachyos, paru-pacman,
snapper-grub-btrfs, tlp, thermald, kde-plasma, fish-shell, ghostty), 5 concept
(dual-boot-windows-linux, rolling-release, btrfs-snapshot-rollback,
x86-64-v3-compilation, system-maintenance-cachyos), 1 source
(sources/cachyos-wiki.md). index.md dan progress.md diupdate (semua 7
milestone jadi in-progress, ditandai perlu konfirmasi user untuk naik ke done).

Belum di-cover jadi halaman terpisah: ranking distro 2026 top-10 (bukan fokus
domain, user sudah settle di CachyOS), rencana backup/migration ke laptop baru
(masih rencana, belum dieksekusi — cek raw langsung kalau perlu).

## [2026-07-19] update | Konfirmasi status "done" untuk semua milestone
User konfirmasi seluruh setup di cachyos-wiki.md sudah benar-benar dijalankan
(bukan rencana). progress.md: 7 milestone diubah in-progress → done.
sources/cachyos-wiki.md: catatan "Yang Belum Terjadi" diganti "Status
Eksekusi", tetap menandai bagian backup/migration ke laptop baru sebagai
rencana murni (belum ada laptop baru).

## [2026-07-19] ingest | vira-maintain-automation-cachyos.md — Automasi maintenance mingguan
Sumber kedua: dokumentasi lengkap setup automated weekly maintenance via
systemd user timer (menggantikan alias `maintain` manual), termasuk tuning
snapper (`NUMBER_LIMIT`), sudoers whitelist `NOPASSWD` untuk eksekusi
unattended, script bash dengan error tracking per-step (`run_step()`), dan
checklist migrasi laptop (belum dieksekusi).

Dibuat: 1 concept baru (automated-maintenance-systemd-timer), 1 source
(sources/vira-maintain-automation-cachyos.md). Update in-place: entities
snapper-grub-btrfs (section tuning NUMBER_LIMIT) dan fish-shell (alias
cleanup/maintain diperbarui ke command paling presisi + catatan risiko
drift alias-vs-script), concepts system-maintenance-cachyos (tabel
frekuensi + link automasi) dan btrfs-snapshot-rollback (link tuning
limit). progress.md: milestone baru "Automasi maintenance" ditambahkan
(langsung done, sudah diuji end-to-end oleh user). index.md diupdate.

Belum di-cover jadi halaman terpisah: checklist migrasi laptop detail —
sudah dirangkum sebagai section di automated-maintenance-systemd-timer.md,
belum dieksekusi jadi tidak dinaikkan jadi milestone progress.md sendiri.

## [2026-07-19] re-ingest lengkap | vira-maintain-automation-cachyos.md — melengkapi gap
Audit ulang menyeluruh raw/vira-maintain-automation-cachyos.md terhadap
seluruh halaman wiki yang sudah kena dampak (concept
automated-maintenance-systemd-timer, entities snapper-grub-btrfs &
fish-shell, concepts system-maintenance-cachyos & btrfs-snapshot-rollback).
Ditemukan 1 gap: bagian §8 raw "Cara Mematikan Automasi" (pause sementara
vs hapus total, plus cara verifikasi status) belum tercakup di wiki sama
sekali. Ditambahkan sebagai section baru di
concepts/automated-maintenance-systemd-timer.md, dan poin ke-10 di
sources/vira-maintain-automation-cachyos.md. Bagian lain raw (latar
belakang, tuning snapper, frekuensi ideal, setup automasi §5.1-5.9,
ringkasan error, checklist migrasi) sudah lengkap dari ingest sebelumnya —
tidak ada perubahan lain.
