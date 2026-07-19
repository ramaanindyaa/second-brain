---
type: source
title: "VIRA Maintain — Automasi Maintenance Mingguan CachyOS"
description: "Ringkasan proses setup automated weekly maintenance (update+cleanup) via systemd user timer, termasuk semua error dan fix-nya"
tags: [source, automation, systemd, sudoers, snapper]
timestamp: 2026-07-19
sources: [raw/vira-maintain-automation-cachyos.md]
---

# Source: VIRA Maintain — Automasi Maintenance Mingguan CachyOS

[raw/vira-maintain-automation-cachyos.md](../../raw/vira-maintain-automation-cachyos.md)

## Ringkasan

Dokumentasi end-to-end proses mengubah alias `maintain` (manual, harus diketik
sendiri) jadi automated lewat **systemd user timer** yang jalan tiap Minggu
09:00 tanpa interaksi. Ditulis di HP Pavilion Plus 14-ep1xxx (CachyOS, Fish
shell, KDE Plasma 6.7). Mencakup: tuning config snapper duluan, alasan
frekuensi ideal tiap alias maintenance, script bash dengan error tracking
per-step, whitelist sudoers `NOPASSWD` buat eksekusi unattended, unit
systemd service+timer, dan checklist migrasi laptop.

## Poin Kunci

1. **Snapper perlu dituning duluan sebelum automasi** — snapshot numpuk
   (~54, limit default 50) karena update hampir harian. Fix:
   `NUMBER_LIMIT=40`, `NUMBER_LIMIT_IMPORTANT=15`,
   `TIMELINE_LIMIT_HOURLY=0` (dead config karena `TIMELINE_CREATE=no`).
2. **Frekuensi ideal**: `update` standalone cuma untuk security patch
   urgent (update harian bikin snapshot numpuk 2x lebih cepat & risiko
   ketemu update belum matang); `cleanup` standalone reaktif dari
   disk penuh; `maintain` sekali seminggu otomatis.
3. **Fish shell tidak support heredoc** (`<< 'EOF'`) — solusinya masuk
   sub-shell `bash` sementara untuk nulis file multi-baris.
4. **Service harus `systemd --user`, bukan root system-wide** — karena
   `makepkg` (dipanggil `paru` untuk build AUR) menolak jalan sebagai root.
5. **Script di-desain tanpa `set -e`**, pakai fungsi wrapper `run_step()`
   yang menangkap exit code tiap command individual ke array
   `FAILED_STEPS`, supaya script tidak berhenti diam-diam di tengah dan
   notifikasi akhir selalu presisi menyebut step mana yang gagal.
6. **Eksekusi unattended butuh sudoers whitelist** (`NOPASSWD`) via
   `visudo -f /etc/sudoers.d/vira-maintain` — karena tidak ada TTY untuk
   input password. `pacman` di-whitelist tanpa batasan argumen (bukan
   `pacman -Rns` spesifik) karena `paru -Syu` memanggil `sudo pacman -Syu
   <paket dinamis>` yang argumennya tidak bisa diprediksi/di-whitelist
   per-argumen. Trade-off disadari & diterima untuk mesin single-user.
7. **`Persistent=true`** di timer unit penting — kalau laptop mati pas
   jadwal seharusnya jalan, timer trigger otomatis begitu laptop nyala
   lagi (bukan skip sampai minggu berikutnya). Butuh
   `loginctl enable-linger` supaya user service bisa jalan sebelum login
   aktif.
8. **Risiko drift**: alias `maintain` (manual) dan script
   `vira-maintain.sh` (otomatis) adalah 2 salinan logic terpisah yang
   tidak saling memanggil — kalau satu diedit, yang lain harus diupdate
   manual. Belum dikonsolidasi (backlog).
9. Checklist migrasi laptop disiapkan tapi **belum dieksekusi** — rsync
   `~/` otomatis bawa script+config, tapi sudoers whitelist, enable-linger,
   dan aktivasi timer harus disetel ulang manual di laptop baru.
10. **Dua level mematikan automasi**: pause sementara (`stop` + `disable`
    timer, file tetap ada, gampang dinyalain lagi) vs hapus total (bongkar
    file service/timer, script, log, sudoers whitelist, dan opsional
    `disable-linger` — dengan catatan hati-hati kalau ada user service lain
    yang masih butuh linger aktif).

## Status Eksekusi

Semua langkah di sumber ini sudah **dijalankan dan diuji end-to-end**
(dari kegagalan awal karena password → fix sudoers → sukses penuh),
bukan sekadar rencana. Bagian checklist migrasi laptop (§7 raw) eksplisit
ditandai belum dieksekusi.

## Halaman Wiki yang Terdampak

- Concept baru: [[automated-maintenance-systemd-timer]] — inti dari sumber ini
- [[snapper-grub-btrfs]] — ditambah section tuning `NUMBER_LIMIT`
- [[fish-shell]] — alias `cleanup`/`maintain` diupdate ke versi command
  paling presisi dari sumber ini + catatan risiko drift
- [[system-maintenance-cachyos]] — rutinitas maintenance sekarang
  ter-automasi, ditambah link ke concept baru
- [[btrfs-snapshot-rollback]] — link ke tuning limit snapshot
