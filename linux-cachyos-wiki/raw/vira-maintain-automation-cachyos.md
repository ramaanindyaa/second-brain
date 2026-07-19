# VIRA Maintain — Automasi Maintenance Mingguan CachyOS

> **Konteks:** Dokumentasi ini mencatat proses lengkap setup automated system maintenance (update + cleanup) di CachyOS pakai systemd user timer, termasuk semua error yang ketemu di jalan dan cara fix-nya. Ditulis buat referensi diri sendiri dan jaga-jaga kalau migrasi laptop.
>
> **Sistem:** HP Pavilion Plus 14-ep1xxx | CachyOS (Arch-based) | Fish shell | KDE Plasma 6.7

---

## 1. Latar Belakang

Awalnya ada 3 alias di Fish shell buat maintenance manual:

- `update` — update sistem + flatpak
- `cleanup` — bersihin cache, orphan package, journal, snapshot
- `maintain` — gabungan update + cleanup

Masalahnya: semua ini manual, butuh diinget dan dijalanin sendiri. Solusinya: automasi pakai **systemd user timer** yang jalan otomatis tiap minggu, tanpa perlu login/interaksi manual.

---

## 2. Alias Fish yang Sudah Ada (Referensi)

Lokasi: `~/.config/fish/config.fish`

```fish
# ── System Update ──
alias update='paru -Syu && flatpak update'

# ── Deep Cleanup ──
alias cleanup='sudo find /var/cache/pacman/pkg -maxdepth 1 -type d -name "download-*" -exec rm -rf {} + && sudo paccache -r && paru -Sc --noconfirm && { pacman -Qtdq | sudo pacman -Rns - 2>/dev/null || true; } && flatpak uninstall --unused && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/* && sudo snapper -c root cleanup number'

# ── Update + Cleanup Sekaligus ──
alias maintain='paru -Syu && flatpak update && sudo find /var/cache/pacman/pkg -maxdepth 1 -type d -name "download-*" -exec rm -rf {} + && sudo paccache -r && paru -Sc --noconfirm && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/* && sudo snapper -c root cleanup number'
```

**Catatan penting:** alias-alias ini didesain untuk dipakai **interaktif** (diketik manual di terminal). Mereka nggak butuh `NOPASSWD` sudo, karena pas nyampe baris `sudo`, sistem nanya password langsung ke user — itu normal dan aman untuk pemakaian manual.

> ⚠️ **Risiko drift:** Ada 2 salinan logic yang mirip — alias `maintain` (manual) dan script `vira-maintain.sh` (otomatis, lihat bawah). Kalau salah satu diedit (misal nambah step baru), yang satu lagi harus ikut diupdate manual, karena keduanya nggak saling memanggil.

---

## 3. Kenapa Snapper Perlu Dituning Duluan

Sebelum bikin automasi, snapshot Btrfs sempat jadi concern karena jumlahnya terlihat banyak (~54 snapshot pas dicek, `NUMBER_LIMIT` default 50).

### Analisis

- Sistem menciptakan sepasang snapshot `pre`/`post` tiap kali `pacman -Syu` jalan (via pacman hook).
- Update dilakukan hampir harian → snapshot numpuk cepat.
- `TIMELINE_CREATE=no` sudah tepat untuk kasus rolling-release ini — snapshot berbasis **waktu buta** (tiap jam, ada perubahan atau nggak) itu boros dan nggak berguna dibanding snapshot berbasis **event** (pre/post update), yang justru pas di titik rawan sistem.

### Konfigurasi final yang diterapkan

```fish
sudo snapper -c root set-config NUMBER_LIMIT=40 NUMBER_LIMIT_IMPORTANT=15 TIMELINE_LIMIT_HOURLY=0
sudo snapper -c root cleanup number
```

Alasan angka:
- `NUMBER_LIMIT=40` — cukup untuk ~2-3 minggu rollback history tanpa nyimpen berlebihan.
- `NUMBER_LIMIT_IMPORTANT=15` — dibiarkan, sudah pas untuk snapshot penting (mis. snapshot instalasi awal yang ditandai `important=yes`).
- `TIMELINE_LIMIT_HOURLY=0` — sebelumnya bernilai 5 tapi jadi *dead config* karena `TIMELINE_CREATE=no`, jadi nggak ngefek apa-apa. Dinolin biar config bersih dan nggak membingungkan di masa depan.

### Verifikasi

```fish
sudo snapper list | wc -l
sudo snapper -c root get-config
```

Hasil: 54 → 44 snapshot (akan settle ke 40 setelah `NUMBER_MIN_AGE` 1800 detik terlewati).

---

## 4. Frekuensi Ideal Menjalankan Alias

| Alias | Frekuensi Ideal | Alasan |
|---|---|---|
| `update` standalone | Hanya kalau ada security patch urgent atau butuh versi terbaru sebuah paket | Update harian bikin snapshot numpuk 2x lebih cepat dari perlu, dan naikin risiko ketemu update yang belum matang |
| `cleanup` standalone | Reaktif — trigger dari `syscheck`/`diskusage` kalau disk mulai penuh | Nggak ada gunanya cleanup kalau cache belum sempat numpuk |
| `maintain` | **Sekali seminggu** (di-otomasi tiap Minggu 09:00) | Sudah gabungan update + cleanup penuh dalam satu tembakan, nggak perlu mikirin 3 alias terpisah |

---

## 5. Setup Automasi — Systemd User Timer

### 5.1 Masalah Fish Shell dengan Heredoc

Fish shell **tidak mendukung** syntax heredoc (`<< 'EOF'`) yang lazim dipakai di bash. Error yang muncul:

```
fish: Expected a string, but found a redirection
cat > ~/.local/bin/vira-maintain.sh << 'EOF'
```

**Solusi:** masuk ke sub-shell bash sementara khusus untuk menulis file multi-baris, lalu `exit` balik ke fish.

```fish
bash
# ... jalankan heredoc di sini ...
exit
```

### 5.2 Membuat Script Maintenance

Lokasi: `~/.local/bin/vira-maintain.sh`

Script **tidak boleh** jalan sebagai root murni karena `makepkg` (dipanggil `paru` untuk build AUR) menolak dijalankan sebagai root. Maka service harus jalan sebagai **systemd `--user`**, bukan system-wide root service.

### 5.3 Versi Final Script (Reliable — dengan Error Tracking)

Versi awal script memakai `set -e`, yang bikin script **berhenti total** begitu ada satu command gagal — termasuk gagal diam-diam tanpa sempat mengirim notifikasi kegagalan di akhir. Maka di-rewrite total memakai fungsi wrapper `run_step()` yang menangkap exit code tiap command secara individual.

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

**Kenapa desain ini lebih reliable:**

1. Tanpa `set -e` global — script jalan terus sampai akhir apapun yang terjadi di tengah, nggak berhenti diam-diam.
2. `run_step()` mencatat tiap kegagalan ke array `FAILED_STEPS`, plus penanda `!!! GAGAL: ...` di log yang gampang di-grep.
3. Notifikasi akhir selalu presisi — kalau ada kegagalan, notifikasi menyebutkan **nama step yang gagal**, bukan sekadar "ada error" generik.
4. Command yang mengandung pipe (`|`) dibungkus `bash -c '...'` karena `run_step` menerima command sebagai argumen array biasa (`"$@"`), sedangkan pipe butuh dieksekusi dalam satu subshell.

### 5.4 Sudoers Whitelist — NOPASSWD untuk Command Spesifik

Karena script dijalankan unattended (tanpa TTY interaktif), command `sudo` di dalamnya akan langsung gagal dengan error:

```
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
sudo: a password is required
```

**Solusi:** whitelist command spesifik lewat `visudo` (JANGAN edit file sudoers langsung, selalu lewat `visudo` supaya syntax tervalidasi otomatis sebelum disimpan).

```fish
sudo EDITOR=nano visudo -f /etc/sudoers.d/vira-maintain
```

> **Catatan:** kalau muncul error `visudo: no editor found (editor path = /usr/bin/vi)`, itu karena CachyOS minimal install nggak menyertakan binary bernama persis `vi` (meski `vim`/`nano`/`micro` mungkin sudah terinstall). Override sementara pakai `EDITOR=nano` di depan command, tanpa mengubah default sistem secara permanen.

Isi file `/etc/sudoers.d/vira-maintain` (final, setelah revisi):

```
rama ALL=(root) NOPASSWD: /usr/bin/find /var/cache/pacman/pkg -maxdepth 1 -type d -name download-* -exec rm -rf {} +
rama ALL=(root) NOPASSWD: /usr/bin/pacman
rama ALL=(root) NOPASSWD: /usr/bin/paccache -r
rama ALL=(root) NOPASSWD: /usr/bin/journalctl --vacuum-time=2weeks
rama ALL=(root) NOPASSWD: /usr/bin/snapper -c root cleanup number
```

**Kenapa `/usr/bin/pacman` tanpa argumen spesifik (bukan `/usr/bin/pacman -Rns`)?**

Percobaan pertama hanya whitelist `pacman -Rns` (untuk hapus orphan package), tapi ternyata `paru -Syu` di baris paling awal script juga memanggil `sudo pacman -Syu ...` di baliknya untuk instalasi paket resmi (non-AUR) — dan argumen ini **dinamis** (beda nama paket tiap kali update), sehingga nggak bisa di-whitelist per-argumen. Solusinya: broaden whitelist ke seluruh `pacman` tanpa batasan argumen.

**Trade-off yang disadari:** ini memperluas scope akses `NOPASSWD` dari filosofi awal "sespesifik mungkin" ke "seluruh pacman tanpa batas". Untuk mesin pribadi single-user, ini dianggap wajar karena `pacman` sendiri memang selalu butuh root untuk apapun yang dia lakukan terhadap paket sistem. Kalau di masa depan risiko ini terasa mengganggu, alternatifnya adalah bikin wrapper script dengan argumen fix dan whitelist wrapper itu saja — tapi menambah kompleksitas yang saat ini dianggap tidak perlu.

Verifikasi isi file:
```fish
sudo cat /etc/sudoers.d/vira-maintain
```

### 5.5 Systemd User Service

Lokasi: `~/.config/systemd/user/vira-maintain.service`

```ini
[Unit]
Description=VIRA Weekly System Maintenance

[Service]
Type=oneshot
ExecStart=/home/rama/.local/bin/vira-maintain.sh
```

### 5.6 Systemd Timer

Lokasi: `~/.config/systemd/user/vira-maintain.timer`

```ini
[Unit]
Description=Run VIRA Maintain tiap Minggu pagi

[Timer]
OnCalendar=Sun 09:00
Persistent=true

[Install]
WantedBy=timers.target
```

`Persistent=true` penting — kalau laptop mati pas jadwal seharusnya jalan (misal laptop nggak dinyalain hari Minggu), timer akan otomatis trigger begitu laptop nyala lagi, bukan skip sampai minggu berikutnya.

### 5.7 Aktivasi

```fish
loginctl enable-linger rama
systemctl --user daemon-reload
systemctl --user enable --now vira-maintain.timer
```

`enable-linger` memungkinkan systemd user service tetap bisa jalan meski user belum login secara aktif (misal laptop baru boot tapi belum masuk desktop session).

### 5.8 Verifikasi Jadwal

```fish
systemctl --user list-timers vira-maintain.timer
```

Output menunjukkan kolom `NEXT` (jadwal run berikutnya) dan `LEFT` (hitung mundur). Kalau output ditampilkan lewat pager (`less`), tekan `q` untuk keluar.

### 5.9 Testing Manual (Wajib Sebelum Percaya ke Jadwal Otomatis)

```fish
systemctl --user start vira-maintain.service
cat ~/.local/share/vira-maintain.log
```

Untuk memantau proses yang sedang berjalan secara real-time (berguna kalau ada AUR package yang perlu di-build dari source, yang bisa makan waktu cukup lama tanpa terlihat progress-nya karena dijalankan lewat `systemctl` bukan terminal interaktif):

```fish
systemctl --user status vira-maintain.service
tail -f ~/.local/share/vira-maintain.log
```

---

## 6. Ringkasan Error yang Ditemukan & Solusinya

| Error | Penyebab | Solusi |
|---|---|---|
| `fish: Expected a string, but found a redirection` | Fish shell tidak support heredoc (`<< 'EOF'`) | Jalankan heredoc di dalam sub-shell `bash`, lalu `exit` |
| `visudo: no editor found (editor path = /usr/bin/vi)` | Binary `vi` tidak ada meski `nano`/`vim` terinstall | `sudo EDITOR=nano visudo -f ...` |
| `sudo: a terminal is required to read the password` | Script dijalankan unattended oleh systemd, tidak ada TTY untuk input password | Whitelist command via `/etc/sudoers.d/vira-maintain` dengan `NOPASSWD` |
| Password masih diminta meski sudoers sudah di-whitelist sebagian | `paru -Syu` memanggil `sudo pacman -Syu <paket dinamis>` yang argumennya tidak bisa di-whitelist spesifik | Broaden whitelist ke `/usr/bin/pacman` tanpa batasan argumen |
| Script berhenti di tengah tanpa notifikasi kegagalan | `set -e` menghentikan seluruh script begitu satu command gagal, termasuk melewati baris notifikasi di akhir | Hapus `set -e`, ganti dengan fungsi `run_step()` yang menangkap exit code per command |

---

## 7. Checklist Migrasi Laptop (Untuk Referensi Masa Depan)

> ⚠️ Bagian ini **belum dieksekusi**, disiapkan untuk dipakai nanti kalau pindah ke laptop baru.

Rsync home directory (`~/`) akan otomatis membawa:
- ✅ `~/.local/bin/vira-maintain.sh`
- ✅ `~/.config/systemd/user/vira-maintain.service` & `.timer`
- ✅ `~/.config/fish/config.fish` (alias `update`/`cleanup`/`maintain`)

Yang **HARUS disetel ulang manual** di laptop baru (tidak ikut ter-rsync karena berada di luar `$HOME` atau merupakan systemd/logind state, bukan file):

1. **Sudoers whitelist** — buat ulang `/etc/sudoers.d/vira-maintain` dengan isi yang sama seperti bagian 5.4.
   ```fish
   sudo EDITOR=nano visudo -f /etc/sudoers.d/vira-maintain
   ```
2. **Enable linger:**
   ```fish
   loginctl enable-linger <username>
   ```
3. **Aktifkan timer** (file config sudah ada, tapi belum otomatis aktif hanya karena filenya ada — symlink activation perlu digenerate ulang):
   ```fish
   systemctl --user daemon-reload
   systemctl --user enable --now vira-maintain.timer
   ```
4. **Pastikan paket prasyarat terinstall:**
   ```fish
   paru -S nano vim libnotify --noconfirm
   ```
   (`paru` sendiri juga harus sudah terinstall lebih dulu sebagai AUR helper)
5. **Test manual sebelum percaya ke jadwal otomatis:**
   ```fish
   systemctl --user start vira-maintain.service
   cat ~/.local/share/vira-maintain.log
   ```

---

## 8. Cara Mematikan Automasi

Ada dua level "matiin", tergantung kebutuhan.

### 8.1 Pause Sementara (file tetap ada, cuma nonaktif)

```fish
systemctl --user stop vira-maintain.timer
systemctl --user disable vira-maintain.timer
```

- `stop` — matiin instance yang sedang jalan/menunggu.
- `disable` — hapus symlink activation-nya supaya tidak auto-start lagi saat boot.

File `.timer` dan `.service` di `~/.config/systemd/user/` **tetap ada**. Untuk menyalakan lagi nanti:

```fish
systemctl --user enable --now vira-maintain.timer
```

### 8.2 Hapus Total (bongkar semua yang sudah disetup)

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

# (Opsional) matiin linger — HATI-HATI kalau ada user systemd service lain yang masih butuh linger
loginctl disable-linger rama
```

> ⚠️ Jangan buru-buru `disable-linger` kalau ternyata ada service `--user` lain (di luar VIRA maintain) yang juga bergantung pada linger tetap aktif.

### 8.3 Cara Verifikasi Status

```fish
systemctl --user list-timers vira-maintain.timer
```

Kalau timer tidak muncul di list sama sekali, berarti sudah benar-benar mati/terhapus.

---

## 9. Status Akhir

- ✅ Snapper config dituning (`NUMBER_LIMIT=40`, `TIMELINE_LIMIT_HOURLY=0`)
- ✅ Script `vira-maintain.sh` reliable dengan error tracking per-step
- ✅ Sudoers whitelist lengkap dan teruji
- ✅ Systemd timer aktif, terjadwal tiap Minggu 09:00 WIB, dengan `Persistent=true`
- ✅ Notifikasi desktop sukses/gagal via `notify-send`
- ✅ Diuji end-to-end: dari gagal password → fix sudoers → sukses penuh

**Belum dikerjakan (untuk nanti):**
- Konsolidasi alias fish (`maintain`) dengan script (`vira-maintain.sh`) supaya tidak ada 2 salinan logic yang bisa drift
- Dokumen migrasi laptop lengkap (checklist di atas baru garis besar, belum digabung dengan strategi migrasi penuh — package list export, Btrfs send/receive, dll)
