---
type: entity
title: "Fish Shell"
description: "Shell default user di CachyOS, dengan alias command center custom ('VIRA')"
tags: [entity, shell]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md, raw/vira-maintain-automation-cachyos.md]
---

# Fish Shell

Shell pilihan user (versi 4.7.1), config berbasis `cachyos-fish-config` bawaan
[[cachyos]] ditambah alias command center custom bernama "VIRA". Lokasi config:
`~/.config/fish/config.fish`. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Base Config

```fish
source /usr/share/cachyos-fish-config/cachyos-config.fish
```

## Daftar Alias

| Alias | Command | Fungsi |
|---|---|---|
| `update` | `paru -Syu && flatpak update` | Update semua |
| `cleanup` | `sudo find /var/cache/pacman/pkg -maxdepth 1 -type d -name "download-*" -exec rm -rf {} + && sudo paccache -r && paru -Sc --noconfirm && { pacman -Qtdq \| sudo pacman -Rns - 2>/dev/null \|\| true; } && flatpak uninstall --unused && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/* && sudo snapper -c root cleanup number` | Deep cleanup |
| `maintain` | `paru -Syu && flatpak update && sudo find /var/cache/pacman/pkg -maxdepth 1 -type d -name "download-*" -exec rm -rf {} + && sudo paccache -r && paru -Sc --noconfirm && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/* && sudo snapper -c root cleanup number` | Update + cleanup sekaligus (weekly, sekarang juga di-automasi — lihat bawah) |
| `install` | `paru -S` | Install package |
| `remove` | `sudo pacman -Rns` | Hapus package + dependency |
| `search` | `paru -Ss` | Cari package |
| `pkginfo` | `paru -Si` | Info detail package |
| `listpkg` | `pacman -Qe` | List package manual |
| `syscheck` | disk + RAM + suhu | Health check cepat |
| `diskusage` | `du -sh /* \| sort -rh \| head -20` | Cek storage terbesar |
| `logerror` | `sudo journalctl -p err -n 20` | Error log terbaru |
| `sysinfo` | `fastfetch` | System info |
| `ports` | `ss -tulnp` | Port terbuka |
| `myip` | `ip addr show` | IP lokal |
| `myip-public` | `curl ifconfig.me` | IP public |
| `snaplist` | `sudo snapper list` | Daftar snapshot |
| `snapnow "desc"` | `sudo snapper -c root create --description` | Buat snapshot manual |
| `updatemirrors` | `sudo cachyos-rate-mirrors` | Update mirror tercepat |
| `reboot` | `sudo reboot` | Restart |
| `off` | `sudo poweroff` | Shutdown |
| `acmode` | `sudo tlp ac` | Force performance mode |
| `batmode` | `sudo tlp bat` | Force battery/powersave mode |
| `tlpcheck` | `sudo tlp-stat -s` | Status TLP |
| `batcheck` | `sudo tlp-stat -b` | Health battery |
| `gs` / `ga` / `gc` / `gp` / `gl` / `gd` | git status/add ./commit -m/push/log/diff | Git shortcuts |

Isi lengkap `config.fish` (verbatim) ada di
[raw/cachyos-wiki.md](../../raw/cachyos-wiki.md) bagian "Fish Shell & Aliases".
Command persis `cleanup`/`maintain` di atas dikonfirmasi ulang (dan
diperbarui) di
[raw/vira-maintain-automation-cachyos.md](../../raw/vira-maintain-automation-cachyos.md).

## Alias `maintain` Sekarang Juga Ter-Automasi

Selain dipakai manual, logic `maintain` di-porting jadi script
`~/.local/bin/vira-maintain.sh` yang jalan otomatis tiap Minggu 09:00
lewat systemd user timer — lihat
[[automated-maintenance-systemd-timer]] untuk setup lengkap.

> ⚠️ **Risiko drift**: alias `maintain` (manual, di atas) dan script
> `vira-maintain.sh` (otomatis) adalah 2 salinan logic yang tidak saling
> memanggil. Kalau salah satu diedit (nambah step baru dll), yang lain
> harus diupdate manual — belum dikonsolidasi.

## Reload Config

```bash
source ~/.config/fish/config.fish
```

## Terkait

Alias-alias di atas adalah wrapper untuk [[paru-pacman]], [[snapper-grub-btrfs]],
[[tlp]], dan referensi ke [[cachyos]] (`cachyos-rate-mirrors`).
