---
type: concept
title: "Automated Maintenance via Systemd User Timer"
description: "Cara mengubah maintenance manual (alias fish) jadi otomatis mingguan lewat systemd --user timer, termasuk sudoers whitelist untuk eksekusi unattended"
tags: [concept, automation, systemd, sudoers, unattended]
skill_level: intermediate
timestamp: 2026-07-19
sources: [raw/vira-maintain-automation-cachyos.md]
---

# Automated Maintenance via Systemd User Timer

Cara menjadikan rutinitas `maintain` (lihat [[system-maintenance-cachyos]])
otomatis jalan tiap minggu tanpa perlu diketik manual, pakai **systemd user
timer** — bukan cron, bukan system-wide root service.
[raw/vira-maintain-automation-cachyos.md](../../raw/vira-maintain-automation-cachyos.md)

## Kenapa `systemd --user`, Bukan Root System-Wide Service

`makepkg` (dipanggil `paru` untuk build package AUR) **menolak dijalankan
sebagai root**. Karena maintenance mingguan mencakup `paru -Syu` (update
termasuk AUR), service harus jalan sebagai systemd **user** service milik
user biasa, bukan root service.

## Kendala & Solusi

### 1. Fish Tidak Support Heredoc

Fish shell error kalau dikasih heredoc bash (`cat > file << 'EOF'`):
```
fish: Expected a string, but found a redirection
```
Solusi: masuk sub-shell `bash` sementara untuk menulis file multi-baris,
lalu `exit` balik ke fish.
```fish
bash
# ... heredoc di sini ...
exit
```

### 2. Script Harus Reliable Tanpa Interaksi

Versi awal script pakai `set -e` — begitu satu command gagal, **seluruh
script berhenti**, termasuk melewati baris notifikasi kegagalan di akhir
(gagal diam-diam). Fix: hapus `set -e`, ganti fungsi wrapper `run_step()`
yang menangkap exit code tiap command secara individual ke array
`FAILED_STEPS`.

Lokasi: `~/.local/bin/vira-maintain.sh`

```bash
#!/bin/bash

LOG="/home/rama/.local/share/vira-maintain.log"
FAILED_STEPS=()

echo "=== VIRA Maintain: $(date) ===" >> "$LOG"

run_step() {
    local desc="$1"
    shift
    echo "--- $desc ---" >> "$LOG"
    if ! "$@" >> "$LOG" 2>&1; then
        echo "!!! GAGAL: $desc" >> "$LOG"
        FAILED_STEPS+=("$desc")
    fi
}

run_step "Update sistem (paru)" paru -Syu --noconfirm
run_step "Update flatpak" flatpak update -y
run_step "Bersihin staging download pacman" sudo find /var/cache/pacman/pkg -maxdepth 1 -type d -name "download-*" -exec rm -rf {} +
run_step "Paccache cleanup" sudo paccache -r
run_step "Bersihin cache AUR build" paru -Sc --noconfirm
run_step "Hapus orphan packages" bash -c '{ pacman -Qtdq | sudo pacman -Rns --noconfirm - 2>/dev/null || true; }'
run_step "Bersihin flatpak unused" flatpak uninstall --unused -y
run_step "Vacuum journal" sudo journalctl --vacuum-time=2weeks
run_step "Bersihin user cache" bash -c 'rm -rf ~/.cache/*'
run_step "Snapper cleanup number" sudo snapper -c root cleanup number

echo "=== Selesai: $(date) ===" >> "$LOG"

if [ ${#FAILED_STEPS[@]} -eq 0 ]; then
    notify-send "VIRA Maintain" "✅ Semua step selesai, nggak ada yang gagal." -i software-update-available
else
    SUMMARY=$(printf "%s, " "${FAILED_STEPS[@]}")
    notify-send "VIRA Maintain" "⚠️ Gagal di: ${SUMMARY%, }. Cek log." -i dialog-error
fi
```
```fish
chmod +x ~/.local/bin/vira-maintain.sh
```

Command yang mengandung pipe (`|`) wajib dibungkus `bash -c '...'` karena
`run_step` menerima command sebagai argumen array biasa (`"$@"`), sedangkan
pipe butuh dieksekusi dalam satu subshell.

### 3. Sudo Tanpa TTY (Unattended Execution)

Systemd timer jalan tanpa TTY interaktif, jadi `sudo` di dalam script gagal:
```
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
sudo: a password is required
```

Solusi: whitelist command spesifik lewat `visudo` (**selalu lewat `visudo`**,
jangan edit file sudoers langsung, supaya syntax tervalidasi otomatis).

```fish
sudo EDITOR=nano visudo -f /etc/sudoers.d/vira-maintain
```

> Kalau muncul `visudo: no editor found (editor path = /usr/bin/vi)` — CachyOS
> minimal install tidak menyertakan binary bernama persis `vi` walau
> `vim`/`nano`/`micro` mungkin sudah terinstall. Override sementara pakai
> `EDITOR=nano` di depan command.

Isi final `/etc/sudoers.d/vira-maintain`:
```
rama ALL=(root) NOPASSWD: /usr/bin/find /var/cache/pacman/pkg -maxdepth 1 -type d -name download-* -exec rm -rf {} +
rama ALL=(root) NOPASSWD: /usr/bin/pacman
rama ALL=(root) NOPASSWD: /usr/bin/paccache -r
rama ALL=(root) NOPASSWD: /usr/bin/journalctl --vacuum-time=2weeks
rama ALL=(root) NOPASSWD: /usr/bin/snapper -c root cleanup number
```

**Kenapa `/usr/bin/pacman` tanpa argumen spesifik** (bukan `pacman -Rns`
saja): percobaan pertama hanya whitelist `pacman -Rns` untuk hapus orphan
package, tapi `paru -Syu` di baris awal script juga memanggil
`sudo pacman -Syu <paket dinamis>` di baliknya — argumen ini dinamis (beda
nama paket tiap update) sehingga tidak bisa di-whitelist per-argumen.

**Trade-off yang disadari dan diterima**: ini memperluas scope
`NOPASSWD` dari "sespesifik mungkin" ke "seluruh pacman tanpa batas
argumen". Untuk mesin pribadi single-user dianggap wajar karena `pacman`
sendiri memang selalu butuh root untuk apapun terhadap paket sistem.
Alternatif kalau risiko ini terasa mengganggu di masa depan: bikin
wrapper script dengan argumen fix dan whitelist wrapper itu saja.

## Unit Systemd

Service, `~/.config/systemd/user/vira-maintain.service`:
```ini
[Unit]
Description=VIRA Weekly System Maintenance

[Service]
Type=oneshot
ExecStart=/home/rama/.local/bin/vira-maintain.sh
```

Timer, `~/.config/systemd/user/vira-maintain.timer`:
```ini
[Unit]
Description=Run VIRA Maintain tiap Minggu pagi

[Timer]
OnCalendar=Sun 09:00
Persistent=true

[Install]
WantedBy=timers.target
```

`Persistent=true` penting — kalau laptop mati pas jadwal seharusnya jalan
(mis. tidak dinyalakan hari Minggu), timer otomatis trigger begitu laptop
nyala lagi, bukan skip sampai minggu berikutnya.

## Aktivasi

```fish
loginctl enable-linger rama
systemctl --user daemon-reload
systemctl --user enable --now vira-maintain.timer
```

`enable-linger` memungkinkan systemd user service tetap jalan meski user
belum login secara aktif (mis. laptop baru boot tapi belum masuk desktop
session) — wajib untuk timer yang jadwalnya bisa jatuh sebelum login.

## Verifikasi & Testing

```fish
# Cek jadwal run berikutnya (kolom NEXT/LEFT, tekan q kalau kena pager)
systemctl --user list-timers vira-maintain.timer

# Test manual sebelum percaya ke jadwal otomatis
systemctl --user start vira-maintain.service
cat ~/.local/share/vira-maintain.log

# Pantau real-time (berguna kalau ada AUR package yang lama di-build)
systemctl --user status vira-maintain.service
tail -f ~/.local/share/vira-maintain.log
```

## Risiko Drift: Alias vs Script

Alias fish `maintain` (manual, lihat [[fish-shell]]) dan
`vira-maintain.sh` (otomatis) adalah **2 salinan logic terpisah** yang
tidak saling memanggil. Kalau salah satu diedit (mis. nambah step baru),
yang lain harus diupdate manual — kalau tidak, keduanya diam-diam beda
perilaku. **Belum dikonsolidasi** (backlog, lihat catatan di
[[fish-shell]]).

## Ringkasan Error yang Ditemukan

| Error | Penyebab | Solusi |
|---|---|---|
| `fish: Expected a string, but found a redirection` | Fish tidak support heredoc | Jalankan heredoc di sub-shell `bash`, lalu `exit` |
| `visudo: no editor found (editor path = /usr/bin/vi)` | Binary `vi` tidak ada meski `nano`/`vim` terinstall | `sudo EDITOR=nano visudo -f ...` |
| `sudo: a terminal is required to read the password` | Systemd timer jalan tanpa TTY | Whitelist command via `/etc/sudoers.d/vira-maintain` dengan `NOPASSWD` |
| Password masih diminta meski sudoers sudah di-whitelist sebagian | `paru -Syu` memanggil `sudo pacman -Syu <paket dinamis>`, argumen tidak bisa diprediksi | Broaden whitelist ke `/usr/bin/pacman` tanpa batasan argumen |
| Script berhenti di tengah tanpa notifikasi kegagalan | `set -e` menghentikan seluruh script begitu satu command gagal | Hapus `set -e`, ganti `run_step()` per-command |

## Checklist Migrasi Laptop (Belum Dieksekusi)

Disiapkan untuk referensi kalau pindah laptop baru — rsync `~/` otomatis
bawa `vira-maintain.sh`, unit systemd, dan alias fish, tapi yang **harus
disetel ulang manual** (di luar `$HOME` atau merupakan systemd/logind
state, bukan file):

1. Buat ulang `/etc/sudoers.d/vira-maintain` (isi sama seperti di atas)
2. `loginctl enable-linger <username>`
3. `systemctl --user daemon-reload && systemctl --user enable --now vira-maintain.timer`
4. Pastikan `nano`, `vim`, `libnotify` (dan `paru` sendiri) terinstall
5. Test manual (`systemctl --user start vira-maintain.service`) sebelum
   percaya ke jadwal otomatis

## Cara Mematikan Automasi

Dua level "matiin", tergantung kebutuhan.

### Pause Sementara (file tetap ada, cuma nonaktif)

```fish
systemctl --user stop vira-maintain.timer
systemctl --user disable vira-maintain.timer
```

- `stop` — matiin instance yang sedang jalan/menunggu.
- `disable` — hapus symlink activation-nya supaya tidak auto-start lagi
  saat boot.

File `.timer` dan `.service` **tetap ada** di
`~/.config/systemd/user/`. Nyalain lagi kapan saja:

```fish
systemctl --user enable --now vira-maintain.timer
```

### Hapus Total (bongkar semua yang sudah disetup)

```fish
# Matiin dan disable dulu
systemctl --user stop vira-maintain.timer
systemctl --user disable vira-maintain.timer

# Hapus file service & timer
rm ~/.config/systemd/user/vira-maintain.service
rm ~/.config/systemd/user/vira-maintain.timer

# Reload systemd biar dia lupa soal unit ini
systemctl --user daemon-reload

# (Opsional) hapus script & log-nya juga
rm ~/.local/bin/vira-maintain.sh
rm ~/.local/share/vira-maintain.log

# (Opsional) cabut sudoers whitelist
sudo rm /etc/sudoers.d/vira-maintain

# (Opsional) matiin linger — HATI-HATI kalau ada user systemd service lain
# yang masih butuh linger
loginctl disable-linger rama
```

> ⚠️ Jangan buru-buru `disable-linger` kalau ada service `--user` lain
> (di luar VIRA maintain) yang juga bergantung pada linger tetap aktif.

### Verifikasi Status

```fish
systemctl --user list-timers vira-maintain.timer
```

Kalau timer tidak muncul di list sama sekali, berarti sudah benar-benar
mati/terhapus.

## Terkait

Konfigurasi snapshot limit yang jadi prasyarat automasi ini ada di
[[snapper-grub-btrfs]]. Rutinitas maintenance yang di-automasi berasal
dari [[system-maintenance-cachyos]].
