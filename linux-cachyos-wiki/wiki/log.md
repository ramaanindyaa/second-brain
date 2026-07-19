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
