---
type: entity
title: "Snapper & grub-btrfs"
description: "Tooling snapshot Btrfs otomatis dan integrasinya ke boot menu GRUB"
tags: [entity, tool, snapshot, btrfs]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md, raw/vira-maintain-automation-cachyos.md]
---

# Snapper & grub-btrfs

Jaring pengaman utama sistem: snapshot Btrfs otomatis sebelum/sesudah tiap
transaksi pacman, bisa di-rollback lewat boot menu GRUB kalau sistem rusak.
Konsep umum snapshot/rollback dijelaskan di [[btrfs-snapshot-rollback]];
halaman ini fokus ke tool-nya. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Setup

```bash
sudo pacman -S snapper grub-btrfs

# Buat config untuk root partition
sudo snapper -c root create-config /

# Aktifkan auto-snapshot terjadwal
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer

# Aktifkan integrasi GRUB (snapshot muncul di boot menu)
sudo systemctl enable --now grub-btrfs.path

# Update GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## snap-pac (Auto-Snapshot per Transaksi)

Sudah terinclude di CachyOS. Setiap `pacman -Syu` dijalankan:
1. Snapshot dibuat **sebelum** update (pre-*)
2. Update dijalankan
3. Snapshot dibuat **sesudah** update (post-*)

Otomatis, tanpa konfigurasi tambahan.

## Command Sehari-hari

```bash
# Lihat daftar snapshot (alias: snaplist)
sudo snapper list

# Buat snapshot manual dengan deskripsi (alias: snapnow "deskripsi")
sudo snapper -c root create --description "sebelum eksperimen"

# Rollback ke snapshot tertentu (via terminal)
sudo snapper rollback [nomor]
```

## Rollback via GRUB (Kalau Sistem Tidak Bisa Boot)

1. Restart → di GRUB pilih "CachyOS Snapshots"
2. Pilih snapshot sebelum update (pre-*)
3. Boot → sistem kembali ke state sebelumnya
4. Kalau mau permanen: `sudo snapper rollback [nomor]`

## Aturan dari User

> Snapper adalah jaring pengaman. Sebelum eksperimen besar:
> `snapnow "deskripsi sebelum eksperimen"` — ini bisa menyelamatkan sistem
> kalau ada yang salah.

## Tuning NUMBER_LIMIT (Prasyarat Automasi Maintenance)

Snapshot bisa numpuk cepat kalau update dilakukan hampir harian — tiap
`pacman -Syu` bikin sepasang snapshot `pre`/`post` (via `snap-pac`). Pernah
ketemu ~54 snapshot padahal `NUMBER_LIMIT` default cuma 50.
[raw/vira-maintain-automation-cachyos.md](../../raw/vira-maintain-automation-cachyos.md)

```bash
sudo snapper -c root set-config NUMBER_LIMIT=40 NUMBER_LIMIT_IMPORTANT=15 TIMELINE_LIMIT_HOURLY=0

sudo snapper -c root cleanup number
```

Alasan angka:
- `NUMBER_LIMIT=40` — cukup untuk ~2-3 minggu rollback history tanpa
  nyimpen berlebihan.
- `NUMBER_LIMIT_IMPORTANT=15` — dibiarkan, sudah pas untuk snapshot yang
  ditandai `important=yes` (mis. snapshot instalasi awal).
- `TIMELINE_LIMIT_HOURLY=0` — sebelumnya 5 tapi jadi *dead config* karena
  `TIMELINE_CREATE=no` (snapshot berbasis event pre/post-update lebih
  berguna daripada snapshot berbasis waktu buta untuk rolling release).
  Dinolin biar config bersih.

Verifikasi:
```bash
sudo snapper list | wc -l
sudo snapper -c root get-config
```

Hasil: 54 → 44 snapshot (settle ke 40 setelah `NUMBER_MIN_AGE` 1800 detik
terlewati). Tuning ini jadi prasyarat sebelum setup
[[automated-maintenance-systemd-timer]], karena `maintain` otomatis
mingguan bikin `snapper -c root cleanup number` jadi bagian rutin.
