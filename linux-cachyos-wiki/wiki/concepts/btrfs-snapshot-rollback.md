---
type: concept
title: "Btrfs Snapshot & Rollback"
description: "Konsep jaring pengaman sistem lewat snapshot Btrfs otomatis dan cara rollback kalau ada yang rusak"
tags: [concept, btrfs, snapshot, safety-net]
skill_level: beginner
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Btrfs Snapshot & Rollback

Konsep jaring pengaman sistem: karena root filesystem [[cachyos]] pakai Btrfs,
sistem bisa "di-freeze" ke suatu titik waktu (snapshot) dan dikembalikan kalau
ada yang rusak. Tool-nya dibahas di [[snapper-grub-btrfs]].
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Kenapa Ini Penting di Rolling Release

Karena [[rolling-release]] tidak punya "versi stabil yang sudah lama dites",
setiap `pacman -Syu` berpotensi membawa regresi. Snapshot mengubah risiko dari
"sistem bisa rusak permanen" menjadi "sistem bisa dikembalikan dalam hitungan
menit".

## Kapan Snapshot Dibuat

- **Otomatis** — lewat `snap-pac`, tiap kali `pacman` menjalankan transaksi
  (snapshot pre-* sebelum, post-* sesudah).
- **Terjadwal** — lewat `snapper-timeline.timer` (snapshot berkala) dan
  `snapper-cleanup.timer` (hapus snapshot lama otomatis).
- **Manual** — sebelum eksperimen berisiko (install DE baru, ubah config
  besar), pakai alias `snapnow "deskripsi"`.

## Dua Jalur Rollback

1. **Lewat GRUB** (kalau sistem sampai tidak bisa boot): restart → pilih
   "CachyOS Snapshots" di boot menu → pilih snapshot sebelum masalah muncul →
   boot ke situ.
2. **Lewat terminal** (kalau sistem masih bisa dipakai): `sudo snapper
   rollback [nomor]` untuk permanen kembali ke state snapshot tertentu.

## Aturan Praktis dari User

> Sebelum eksperimen besar: `snapnow "deskripsi sebelum eksperimen"`. Ini bisa
> menyelamatkan sistem kalau ada yang salah.

Command detail dan setup awal ada di [[snapper-grub-btrfs]], termasuk
tuning `NUMBER_LIMIT` supaya jumlah snapshot tidak numpuk tak terkendali
saat update dilakukan sering (prasyarat untuk
[[automated-maintenance-systemd-timer]]).
