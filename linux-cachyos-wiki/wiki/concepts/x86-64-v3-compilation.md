---
type: concept
title: "x86-64-v3 Compilation"
description: "Kenapa CachyOS mengompilasi ulang package untuk CPU modern (AVX2) dan apa dampaknya ke performa"
tags: [concept, performance, compilation]
skill_level: intermediate
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# x86-64-v3 Compilation

Salah satu alasan utama [[cachyos]] diklaim lebih cepat dari distro lain untuk
hardware modern: paket dikompilasi ulang menyasar level instruksi CPU yang
lebih baru. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Konsep

x86-64 punya beberapa "microarchitecture level" yang menandai fitur CPU yang
diasumsikan tersedia saat compile:

| Level | Asumsi CPU | Siapa yang pakai |
|---|---|---|
| x86-64-v1 | Generic, CPU 2008+ | Ubuntu (default, demi kompatibilitas maksimal) |
| x86-64-v3 | AVX2, CPU 2013+ | CachyOS |
| x86-64-v4 | AVX-512 | Sebagian target CachyOS untuk CPU yang support |

Kompilasi v1 = paling kompatibel tapi tidak memanfaatkan instruksi CPU modern.
Kompilasi v3 = butuh CPU 2013+ tapi bisa pakai AVX2 untuk workload yang cocok
(compute-heavy), berpotensi lebih cepat.

## Relevansi ke Hardware User

[[hp-pavilion-plus-14]] pakai Intel Core Ultra 5 125H yang support penuh
x86-64-v3, jadi bisa memanfaatkan paket CachyOS yang dikompilasi di level ini.
Klaim sumber: ~11% performa lebih cepat untuk compute workload dibanding
kompilasi generic (x86-64-v1) yang dipakai Ubuntu.

## Verifikasi CPU Support v3

```bash
/lib64/ld-linux-x86-64.so.2 --help | grep "x86-64-v3"
# Harus muncul: x86-64-v3 (supported, searched)
```

## Terkait

Bagian dari kumpulan alasan performa CachyOS di [[cachyos]] (bersama BORE
scheduler dan Mesa terbaru).
