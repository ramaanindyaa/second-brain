---
type: progress
title: "Roadmap Progress — Linux & CachyOS"
description: "Checklist belajar Linux dasar sampai terbiasa dengan CachyOS sehari-hari"
tags: [progress, roadmap]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Roadmap Progress

Status: `todo | in-progress | done`. Update kolom "Terakhir update" setiap kali
status berubah. Field ini dibaca duluan saat QUERY untuk "aku sudah belajar
sampai mana".

| Milestone | Status | Terakhir Update | Terkait |
|---|---|---|---|
| Instalasi & first boot CachyOS (partisi, bootloader, kernel variant) | done | 2026-07-19 | [dual-boot-windows-linux](concepts/dual-boot-windows-linux.md), [cachyos](entities/cachyos.md) |
| Shell & terminal dasar (fish, navigasi, permission, package management) | done | 2026-07-19 | [fish-shell](entities/fish-shell.md), [ghostty](entities/ghostty.md) |
| Package management Arch-based (pacman, paru/AUR, update system) | done | 2026-07-19 | [paru-pacman](entities/paru-pacman.md), [rolling-release](concepts/rolling-release.md) |
| Filesystem Btrfs & snapshot/rollback (snapper, grub-btrfs) | done | 2026-07-19 | [snapper-grub-btrfs](entities/snapper-grub-btrfs.md), [btrfs-snapshot-rollback](concepts/btrfs-snapshot-rollback.md) |
| Desktop environment/WM setup & customization | done | 2026-07-19 | [kde-plasma](entities/kde-plasma.md) |
| System maintenance (log, systemd services, cleanup, troubleshooting umum) | done | 2026-07-19 | [system-maintenance-cachyos](concepts/system-maintenance-cachyos.md), [tlp](entities/tlp.md), [thermald](entities/thermald.md) |
| Performance tuning khas CachyOS (scx scheduler, kernel pilihan, gaming) | done | 2026-07-19 | [x86-64-v3-compilation](concepts/x86-64-v3-compilation.md) |

Catatan: user mengonfirmasi (2026-07-19) bahwa seluruh setup di
`cachyos-wiki.md` sudah benar-benar dijalankan & dipahami, bukan sekadar
rencana — semua milestone dinaikkan ke "done". Rencana backup/migration ke
laptop baru (lihat [sources/cachyos-wiki.md](sources/cachyos-wiki.md)) tetap
belum masuk milestone karena memang belum dieksekusi (belum ada laptop baru).
