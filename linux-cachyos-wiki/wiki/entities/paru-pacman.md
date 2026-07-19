---
type: entity
title: "Paru & Pacman"
description: "Package manager utama CachyOS — pacman (official repo) dan paru (AUR helper)"
tags: [entity, tool, package-manager]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Paru & Pacman

Pasangan package manager utama di [[cachyos]]: `pacman` untuk official repo,
`paru` sebagai AUR helper (superset dari pacman, bisa install dari AUR juga).
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Command Inti

```bash
# Update semua (sistem + AUR)
paru -Syu

# Install package
paru -S nama-package

# Hapus package + dependency yang tidak dipakai lagi
sudo pacman -Rns nama-package

# Search package
paru -Ss keyword

# Info detail package
paru -Si nama-package

# List package terinstall
pacman -Qe          # yang diinstall manual
pacman -Qm          # yang dari AUR
pacman -Qtdq        # orphan packages (tidak dibutuhkan package lain)
```

## Flatpak (pelengkap, di luar pacman/paru)

```bash
flatpak update
flatpak install flathub com.example.App
flatpak uninstall com.example.App
flatpak uninstall --unused
```

## Alias Fish yang Dipetakan ke Command Ini

Lihat [[fish-shell]] untuk daftar lengkap — yang relevan ke package management:
`update`, `install`, `remove`, `search`, `pkginfo`, `listpkg`.

## Aturan Penting dari User

> Jangan pakai GUI package manager (Pamac, KDE Discover) untuk system packages.
> Selalu gunakan `paru` atau `pacman` via terminal.

## Terkait Troubleshooting

Error keyring signature invalid dan pacman hang (db.lck) — solusinya ada di
[[system-maintenance-cachyos]].
