---
type: entity
title: "HP Pavilion Plus 14-ep1xxx"
description: "Laptop device user, host untuk instalasi CachyOS dual boot"
tags: [entity, hardware, device]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# HP Pavilion Plus 14-ep1xxx

Device utama user, dual boot CachyOS + Windows 11. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Spesifikasi

| Komponen | Spesifikasi |
|---|---|
| CPU | Intel Core Ultra 5 125H (Meteor Lake), 14 core (4P+8E+2LP-E), 18 thread, boost 4.5GHz |
| GPU | Intel Arc 7-core iGPU (aktif penuh karena RAM dual-channel 16GB) |
| RAM | 16GB DDR5 dual-channel |
| Storage | 475GB NVMe SSD (Btrfs) |
| WiFi | Intel CNVi (iwlwifi) |
| Display | 14" 1920x1080 60Hz |
| NPU | Intel AI Boost NPU (Meteor Lake) |

## Kenapa Hardware Ini Relevan untuk Pilihan OS

- Meteor Lake terlalu baru untuk kernel Ubuntu 24.04 (6.8) — Intel Arc iGPU
  punya freeze issue di sana.
- Intel CNVi WiFi = tidak ada driver drama di distro Linux manapun (beda dari
  varian HP 14 lower-end yang pakai Realtek rtw89 yang lebih rewel).
- RAM 16GB dual-channel adalah syarat minimum supaya Intel Arc iGPU aktif penuh.
- CPU support AVX2 penuh → dapat benefit dari kompilasi x86-64-v3 CachyOS, lihat
  [[x86-64-v3-compilation]].
- Punya NPU yang baru full-supported di kernel 7.x — jadi alasan tambahan pilih
  [[cachyos]] dibanding Ubuntu.

## Konfigurasi Terpasang

- OS: CachyOS, dual boot dengan Windows 11 — lihat [[dual-boot-windows-linux]]
- Kernel: linux-cachyos 7.1.2-3
- Desktop: KDE Plasma 6.7 — lihat [[kde-plasma]]
- Shell: Fish 4.7.1 — lihat [[fish-shell]]
- Terminal: Ghostty — lihat [[ghostty]]

## Output Referensi (fastfetch)

```
OS:      CachyOS x86_64
Host:    HP Laptop 14-ep1xxx
Kernel:  Linux 7.1.2-3-cachyos
DE:      KDE Plasma 6.7.1
WM:      KWin (Wayland)
CPU:     Intel Core Ultra 5 125H (14) @ 4.50 GHz
GPU:     Intel Arc Graphics @ 2.20 GHz [Integrated]
Memory:  ~2.5 GiB / 15.1 GiB
Disk:    ~35 GiB / 278 GiB (Btrfs)
```
