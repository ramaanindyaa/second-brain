---
type: concept
title: "Rolling Release vs LTS"
description: "Perbedaan fundamental model rilis rolling (CachyOS) vs LTS (Ubuntu) dan implikasinya untuk maintenance"
tags: [concept, package-management, release-model]
skill_level: beginner
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Rolling Release vs LTS

Perbedaan fundamental model rilis yang menjelaskan kenapa maintenance
[[cachyos]] terasa berbeda dari distro LTS seperti Ubuntu.
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Perbandingan

```
Ubuntu (LTS):
Ubuntu 22.04 → 2 tahun → Ubuntu 24.04 → 2 tahun → Ubuntu 26.04
- Package di-freeze selama 2 tahun
- Hanya dapat security patch di antara rilis besar
- Upgrade besar (major version) = ribet, kadang break

CachyOS (Rolling):
Install sekali → paru -Syu → paru -Syu → selamanya
- Selalu dapat package terbaru
- Tidak ada "upgrade besar" yang terpisah — semua incremental
- Satu command update, berlaku selamanya
```

## Implikasi Praktis

- **Update harus rutin.** Rolling release makin lama tidak di-update, makin
  besar risiko "partial upgrade issue" saat akhirnya di-update. Aturan dari
  user: jangan skip update lebih dari 2 minggu. Detail rutinitas di
  [[system-maintenance-cachyos]].
- **Software selalu terbaru** (termasuk Mesa untuk driver grafis) — trade-off
  dari stabilitas ke arah fitur/performa terkini.
- **Snapshot jadi krusial** karena tidak ada "versi stabil yang dites lama" —
  setiap update berpotensi membawa regresi baru. Lihat
  [[btrfs-snapshot-rollback]] sebagai mitigasi.

## Terkait

Trade-off skor Rolling (CachyOS) vs LTS (Ubuntu) dari sisi stabilitas vs
performa/kebaruan software ada di [[cachyos]].
