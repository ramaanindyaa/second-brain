---
type: entity
title: "Snapper & grub-btrfs"
description: "Tooling snapshot Btrfs otomatis dan integrasinya ke boot menu GRUB"
tags: [entity, tool, snapshot, btrfs]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
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
