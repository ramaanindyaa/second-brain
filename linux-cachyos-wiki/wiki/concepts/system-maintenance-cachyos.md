---
type: concept
title: "System Maintenance & Troubleshooting CachyOS"
description: "Rutinitas maintenance mingguan/bulanan dan solusi error umum di CachyOS"
tags: [concept, maintenance, troubleshooting]
skill_level: beginner
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# System Maintenance & Troubleshooting CachyOS

Rutinitas menjaga sistem [[cachyos]] tetap sehat sebagai rolling release, plus
solusi error yang sudah pernah ditemui. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Rutinitas

| Frekuensi | Command (alias) | Isi |
|---|---|---|
| Weekly | `update` | `paru -Syu && flatpak update` |
| Monthly | `maintain` atau `cleanup` | Update + cleanup ringan, atau nuclear cleanup penuh |

Aturan dari user (penting untuk rolling release, lihat [[rolling-release]]):
> Jangan skip update lebih dari 2 minggu — hindari partial upgrade issues.

## Cleanup Manual (Isi `cleanup`)

```bash
sudo paccache -r                          # cache package lama
sudo pacman -Rns $(pacman -Qtdq)          # orphan packages
flatpak uninstall --unused                # cache Flatpak
sudo journalctl --vacuum-time=2weeks      # log lama
rm -rf ~/.cache/* ~/.cache/paru/ ~/.cache/thumbnails/
df -h /                                   # cek disk usage
```

## Health Check

```bash
btop        # monitoring interaktif
syscheck    # alias: disk + RAM + suhu sekaligus
```

Metrik normal vs warning:

| Metrik | Normal | Warning |
|---|---|---|
| CPU temp idle | 50-65°C | >80°C |
| CPU temp load | 70-85°C | >95°C |
| RAM idle | <30% | >80% |
| Disk usage | <70% | >85% |
| Swap usage | <5% | >50% |

## Troubleshooting

### Keyring Error saat Update

```
error: cachyos-extra-v3: signature from "CachyOS" is invalid
```
Fix:
```bash
sudo pacman -S cachyos-keyring
sudo pacman-key --populate cachyos
paru -Syu
```

### Sistem Tidak Bisa Boot setelah Update

Rollback lewat GRUB — lihat [[btrfs-snapshot-rollback]].

### Package Build Error di AUR

```bash
rm -rf ~/.cache/paru/clone/nama-package/
paru -S nama-package
```

### Pacman Partial Update Hang

```bash
sudo pkill pacman
sudo rm /var/lib/pacman/db.lck
sudo pacman -Syu
```

### Font Baru Tidak Muncul di Dialog

```bash
fc-cache -fv
kbuildsycoca6 --noincremental
# Tutup dan buka ulang System Settings
```

### Service Tidak Jalan

```bash
systemctl status nama-service
sudo systemctl enable --now nama-service
sudo journalctl -u nama-service -n 50
```

## Services Kunci yang Perlu Dicek Kalau Ada Masalah

```bash
systemctl status tlp          # lihat [[tlp]]
systemctl status thermald     # lihat [[thermald]]
systemctl status ufw          # firewall
systemctl status docker       # container runtime
systemctl status snapper-*    # lihat [[snapper-grub-btrfs]]
systemctl status grub-btrfs
```
