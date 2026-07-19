---
type: entity
title: "Ghostty"
description: "Terminal emulator pilihan user, dengan theme custom"
tags: [entity, terminal]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Ghostty

Terminal emulator yang dipakai user di [[kde-plasma]] (default terminal,
shortcut `Ctrl+Alt+T`). Config di `~/.config/ghostty/config`.
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Config Optimal

```ini
font-family = JetBrainsMono Nerd Font
font-size = 13
theme = Catppuccin Mocha
background-opacity = 0.9
window-padding-x = 8
window-padding-y = 8
copy-on-select = true
mouse-hide-while-typing = true
```

## Custom Themes

Disimpan di `~/.config/ghostty/themes/`:

**claude-code-dark**:
```
background = 141413
foreground = faf9f5
cursor-color = d97757
cursor-text = 141413
selection-background = 2d2d2b
selection-foreground = faf9f5
```

**vercel**:
```
background = #101010
foreground = #fafafa
cursor-color = #f32882
```

Aktifkan dengan `theme = claude-code-dark` (atau `vercel`) di config utama.

## Cek Theme Tersedia

```bash
ls /usr/share/ghostty/themes/ | grep -i nama
```
