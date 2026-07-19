---
type: entity
title: "Fish Shell"
description: "Shell default user di CachyOS, dengan alias command center custom ('VIRA')"
tags: [entity, shell]
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
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
| `maintain` | `paru -Syu && flatpak update && sudo paccache -r && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/paru/` | Update + cleanup ringan (weekly) |
| `cleanup` | `sudo paccache -r && pacman -Qtdq \| sudo pacman -Rns - && flatpak uninstall --unused && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/*` | Nuclear cleanup (monthly) |
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

## Reload Config

```bash
source ~/.config/fish/config.fish
```

## Terkait

Alias-alias di atas adalah wrapper untuk [[paru-pacman]], [[snapper-grub-btrfs]],
[[tlp]], dan referensi ke [[cachyos]] (`cachyos-rate-mirrors`).
