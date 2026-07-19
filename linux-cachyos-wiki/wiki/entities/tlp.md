---
type: entity
title: "TLP"
description: "Power management daemon yang dipakai CachyOS, mengatur CPU governor & battery charge threshold"
tags: [entity, tool, power-management]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# TLP

Power management daemon default di [[cachyos]] — sudah terpasang dan
me-mask `power-profiles-daemon` (dianggap konflik). Lebih powerful: bisa set
battery charge threshold, CPU governor per-device, WiFi power saving, dll.
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Konfigurasi Terpasang (`/etc/tlp.conf`)

```ini
# CPU Governor
CPU_SCALING_GOVERNOR_ON_AC=performance
CPU_SCALING_GOVERNOR_ON_BAT=powersave
CPU_SCALING_GOVERNOR_ON_SAV=powersave

# CPU Energy Policy
CPU_ENERGY_PERF_POLICY_ON_AC=performance
CPU_ENERGY_PERF_POLICY_ON_BAT=balance_power
CPU_ENERGY_PERF_POLICY_ON_SAV=power

# Battery Health Threshold
START_CHARGE_THRESH_BAT0=20
STOP_CHARGE_THRESH_BAT0=80
```

Cara edit cepat via `sed` (uncomment + ganti nilai default) ada lengkap di
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md) bagian "TLP Power Management".

## Cara Kerja

```
Charger masuk  → TLP auto: CPU Governor = performance
Cabut charger  → TLP auto: CPU Governor = powersave
Battery        → charge dibatasi 20-80% saja (jaga health jangka panjang)
```

## Kenapa Threshold 20-80%

Battery Li-Ion degradasi paling cepat saat charge penuh 100% terus-menerus
(stress high voltage) atau discharge sampai 0% (stress low voltage). Range
20-80% adalah sweet spot — estimasi memperpanjang battery life 2-3x lipat.
Kalau butuh 100% untuk perjalanan panjang: `sudo tlp fullcharge`.

## Verifikasi

```bash
sudo tlp-stat -s   # alias: tlpcheck — status profile (AC/performance atau bat)
sudo tlp-stat -b   # alias: batcheck — health battery
```

## Alias Fish Terkait

`acmode` (force performance/AC), `batmode` (force powersave/battery) — lihat
[[fish-shell]].

## Terkait

Dipasangkan dengan [[thermald]] untuk kontrol suhu — kombinasi keduanya
menurunkan suhu idle dari 69-75°C menjadi 53-59°C di device user (lihat
[[hp-pavilion-plus-14]]).
