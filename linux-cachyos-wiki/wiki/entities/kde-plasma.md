---
type: entity
title: "KDE Plasma"
description: "Desktop environment pilihan user di CachyOS, dengan theme stack Layan Dark + Kvantum"
tags: [entity, desktop-environment]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# KDE Plasma

Desktop environment yang dipakai user di [[cachyos]] — versi 6.7, WM KWin
(Wayland). [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Theme Stack

| Komponen | Pilihan |
|---|---|
| Global Theme | Layan Dark |
| Icons | Tela Circle Dark |
| Application Style | Kvantum (Layan) |
| GTK Theme | Layan-Dark |
| Fonts | Inter 11pt (UI), JetBrains Mono 11pt (fixed) |
| Cursor | Layan |

## Install

```bash
sudo pacman -S kvantum
paru -S layan-kde-git layan-gtk-theme-git tela-icon-theme-git
sudo pacman -S inter-font ttf-jetbrains-mono ttf-jetbrains-mono-nerd
fc-cache -fv && kbuildsycoca6 --noincremental
```

Setup Kvantum: `kvantummanager` → Change/Delete Theme → pilih Layan → Use this theme.

## Urutan Apply di System Settings

1. Global Theme → Layan Dark → Apply (centang semua)
2. Application Style → Kvantum → Apply
3. Icons → Tela Circle Dark → Apply
4. Text & Fonts: General Inter 11pt, Fixed width JetBrains Mono 11pt, Small
   Inter 9pt, sub-pixel rendering RGB, hinting Slight
5. GTK Application Style → Layan-Dark

## Animasi Rekomendasi

Global speed 75%, window open/close Glide, maximize Stretch, **minimize Magic
Lamp**, peek desktop Window Aperture, virtual desktop switch Slide.

## Shortcut Penting

| Shortcut | Fungsi |
|---|---|
| `Super` | Overview semua window (dikonfigurasi) |
| `Super + Tab` | Switch antar window |
| `Ctrl + F1/F2/F3` | Pindah Virtual Desktop |
| `Ctrl + Alt + F1-F6` | TTY (terminal darurat) |
| `Ctrl + Alt + F7` | Kembali ke desktop grafis |
| `Ctrl + Alt + T` | Buka terminal (dikonfigurasi ke [[ghostty]]) |

## Default Applications

Browser Brave, Terminal [[ghostty]], File manager Dolphin, Text editor Kate,
PDF Okular, Archive Ark, Image viewer Gwenview, Video/Audio Haruna.
