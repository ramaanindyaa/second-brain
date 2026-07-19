---
type: entity
title: "Thermald"
description: "Intel Thermal Daemon — cegah thermal throttle kasar dengan menurunkan P-state lebih awal"
tags: [entity, tool, thermal-management]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Thermald

Intel Thermal Daemon — monitor suhu CPU real-time dan ambil tindakan sebelum
kernel memaksa thermal throttle. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Kenapa Perlu

```
Tanpa thermald:
CPU panas → kernel throttle paksa → performa drop drastis → tidak terkontrol

Dengan thermald:
CPU naik → thermald detect early → kurangi P-state sedikit →
suhu turun → performa tetap konsisten → tidak ada cliff-drop
```

## Setup

```bash
sudo pacman -S thermald
sudo systemctl enable --now thermald
```

## Hasil di Device User

Setelah thermald + [[tlp]] aktif dan dikonfigurasi benar, di
[[hp-pavilion-plus-14]]:

| Kondisi | Sebelum | Sesudah |
|---|---|---|
| Suhu idle | 69-75°C | 53-59°C |

Penurunan 16-22°C hanya dari kombinasi thermald + TLP yang benar dikonfigurasi.
