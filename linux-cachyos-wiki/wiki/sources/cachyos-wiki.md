---
type: source
title: "Sumber — CachyOS Wiki: HP Pavilion Plus 14 Setup"
description: "Dokumentasi lengkap setup CachyOS dari nol sampai daily driver di HP Pavilion Plus 14 (Meteor Lake)"
tags: [source, cachyos, setup, dual-boot]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Sumber — CachyOS Wiki: HP Pavilion Plus 14 Setup

Dokumen setup lengkap (dibuat Juli 2026) yang menutupi seluruh perjalanan: riset
distro → alasan pilih CachyOS → dual boot dengan Windows 11 → instalasi →
post-install setup → konfigurasi shell/power/firewall/snapshot → theming KDE →
daftar aplikasi → maintenance rutin → troubleshooting → rencana backup/migration.
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Konteks

- Device: HP Pavilion Plus 14-ep1xxx, Intel Core Ultra 5 125H (Meteor Lake),
  16GB DDR5, 475GB NVMe, dual boot dengan Windows 11.
- Ini instalasi CachyOS pertama user di device ini (bukan migrasi ulang) —
  lihat [[hp-pavilion-plus-14]].
- AI assistant yang membantu proses ini disebut "VIRA" (Claude Sonnet 4.6) —
  nama dipakai di alias command center fish shell.

## Poin Kunci

1. **Alasan pindah dari kandidat Ubuntu ke CachyOS**: Meteor Lake (Intel Arc iGPU)
   punya freeze issue di kernel 6.8 (Ubuntu 24.04), sedangkan CachyOS kernel 7.x
   sudah punya XE driver stabil + P-State tuning + NPU support penuh. Detail di
   [[cachyos]].
2. **Dual boot** dilakukan dengan shrink partisi Windows (BitLocker off dulu,
   Fast Startup off dulu) lalu install CachyOS di ruang unallocated dengan
   filesystem Btrfs. Detail di [[dual-boot-windows-linux]].
3. **CachyOS adalah rolling release** — tidak ada versi besar seperti Ubuntu
   22.04→24.04, cukup `paru -Syu` terus-menerus. Detail di [[rolling-release]].
4. **Snapper + grub-btrfs** dipasang sejak awal sebagai jaring pengaman sebelum
   update besar/eksperimen — snapshot otomatis tiap `pacman` transaction lewat
   snap-pac. Detail di [[btrfs-snapshot-rollback]].
5. **Package management** pakai kombinasi pacman (official repo) + paru (AUR) +
   flatpak. Detail di [[paru-pacman]].
6. **Power management** pakai TLP (bukan power-profiles-daemon, di-mask CachyOS
   by default) dengan battery charge threshold 20-80% untuk longevity, plus
   thermald untuk cegah thermal throttle kasar. Detail di [[tlp]] dan [[thermald]].
7. **Shell**: Fish dengan alias command center custom (`update`, `maintain`,
   `cleanup`, `syscheck`, dll) — semua command persis tersimpan di
   [[fish-shell]].
8. **Desktop**: KDE Plasma 6.7 dengan theme stack Layan Dark + Kvantum + Tela
   icons, terminal Ghostty dengan theme custom. Detail di [[kde-plasma]] dan
   [[ghostty]].
9. **Kompilasi x86-64-v3**: paket CachyOS dikompilasi untuk CPU modern (AVX2+),
   beda dari Ubuntu yang masih x86-64-v1 generic — klaim ~11% lebih cepat.
   Detail di [[x86-64-v3-compilation]].
10. Ada rencana **maintenance rutin** (weekly `update`, monthly `cleanup`) dan
    **troubleshooting** untuk error umum (keyring, boot gagal, AUR build error,
    pacman hang). Detail di [[system-maintenance-cachyos]].
11. Ada **rencana backup/migration** ke laptop baru nanti (export package list,
    rsync config, restore via script) — estimasi 45 menit vs 3-4 jam di Ubuntu.
    Belum jadi halaman terpisah karena masih rencana, bukan pengalaman
    terjalani — cek langsung ke raw kalau perlu detail command-nya.

## Status Eksekusi

User mengonfirmasi (2026-07-19) bahwa seluruh setup di dokumen ini — instalasi,
dual boot, post-install, fish/TLP/thermald/UFW/snapper, KDE theming, aplikasi —
sudah benar-benar dijalankan di [[hp-pavilion-plus-14]], bukan sekadar rencana.
Lihat [progress.md](../progress.md) (semua milestone "done").

Pengecualian: bagian **Backup & Migration ke Laptop Baru** masih rencana murni
(belum ada laptop baru untuk dieksekusi) — tidak dihitung sebagai milestone
selesai.
