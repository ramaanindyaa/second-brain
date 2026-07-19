---
type: entity
title: "CachyOS"
description: "Distro Linux Arch-based yang dipakai user, fokus performa lewat kompilasi & scheduler custom"
tags: [entity, distro, arch-based]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# CachyOS

Distro Linux yang dipakai di [[hp-pavilion-plus-14]], base Arch, rolling release.
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Identitas

- Base: Arch Linux
- Release model: Rolling — lihat [[rolling-release]]
- Kernel yang dipakai: `linux-cachyos` (versi 7.1.2-3 di device user), dengan
  `linux-cachyos-lts` terpasang sebagai fallback
- Desktop pilihan user: KDE Plasma 6.7 — lihat [[kde-plasma]]
- Ranking #1 DistroWatch selama 18+ bulan (per klaim sumber, Juli 2026)

## Kenapa CachyOS Dipilih Dibanding Ubuntu (untuk hardware Meteor Lake)

1. **Intel Arc GPU stability** — kernel 7.x sudah XE driver stabil, Ubuntu 24.04
   (kernel 6.8) masih ada freeze issue di Meteor Lake.
2. **CPU P-State tuning** — perlu kernel 6.9+ untuk P-State EPP Meteor Lake yang
   proper, CachyOS sudah include.
3. **Kompilasi x86-64-v3** (AVX2) vs Ubuntu yang masih x86-64-v1 generic — lihat
   [[x86-64-v3-compilation]].
4. **BORE/EEVDF scheduler** — menangani arsitektur heterogen P-core/E-core/
   LP-E-core lebih baik, desktop tetap responsif saat load tinggi.
5. **Intel NPU (AI Boost)** full support di kernel 7.x, terbatas di Ubuntu 6.8.
6. **Intel CNVi WiFi** — zero driver drama (ini sebenarnya bukan keunggulan
   spesifik CachyOS, tapi karakteristik hardware — lihat [[hp-pavilion-plus-14]]).
7. **Mesa selalu terbaru** (rolling) vs Ubuntu yang frozen di versi Mesa rilis.
8. **Tidak ada Snap** — semua package native (pacman) atau Flatpak pilihan user.

## Benchmark yang Diklaim Sumber

- Phoronix: CachyOS **11.6% lebih cepat** dari Ubuntu 24.04 di 75 workload test.
- Perbandingan skor kualitatif (skala 1-10, dari sumber) — Raw Performance 9.7
  vs Ubuntu 8.2; Stabilitas 7.2 vs 9.6; Kemudahan 7.0 vs 9.5; Keamanan jangka
  panjang 6.5 vs 9.7. Trade-off performa vs stabilitas/kemudahan konsisten di
  seluruh perbandingan — CachyOS unggul performa & customizability, Ubuntu
  unggul stabilitas & ekosistem.

## Tooling Khas CachyOS

- `cachyos-rate-mirrors` — benchmark & pilih mirror tercepat, wajib dijalankan
  pertama kali setelah install
- `cachyos-fish-config` — base config Fish shell, di-source di
  `~/.config/fish/config.fish` — lihat [[fish-shell]]
- `cachyos-keyring` — paket keyring, sumber umum error signature invalid (lihat
  [[system-maintenance-cachyos]] bagian troubleshooting)
- Package manager: pacman + paru (AUR) — lihat [[paru-pacman]]

## Catatan

Ranking distro 2026 lengkap (top 10, dari riset user) ada di
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md) bagian "Riset Distro Linux" —
belum dibuatkan halaman concept terpisah karena bukan fokus utama domain ini
(user sudah memutuskan pakai CachyOS).
