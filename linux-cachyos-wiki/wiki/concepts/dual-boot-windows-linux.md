---
type: concept
title: "Dual Boot Windows + Linux"
description: "Persiapan dan langkah partisi untuk dual boot CachyOS dengan Windows 11 tanpa merusak instalasi Windows"
tags: [concept, dual-boot, partitioning]
skill_level: beginner
timestamp: 2026-07-19
sources: [raw/cachyos-wiki.md]
---

# Dual Boot Windows + Linux

Cara aman mempersiapkan partisi Windows untuk instalasi Linux ([[cachyos]]) di
samping Windows 11 tanpa merusak instalasi Windows-nya, dipraktikkan di
[[hp-pavilion-plus-14]]. [raw/cachyos-wiki.md](../../raw/cachyos-wiki.md)

## Langkah Pre-Installation (di Windows)

### 1. Matikan BitLocker

```powershell
manage-bde -off C:
manage-bde -status C:   # tunggu sampai "Fully Decrypted"
```

⚠️ Proses decrypt ~15-30 menit di SSD — jangan shutdown selama proses berjalan.

### 2. Matikan Fast Startup

```powershell
powercfg /h off
```

Atau: Control Panel → Power Options → uncheck "Turn on fast startup".

**Kenapa penting**: Fast Startup membuat Windows tidak pernah benar-benar
shutdown, sehingga partisi C: terkunci — kalau tidak dimatikan ada risiko
Windows corrupt saat dual boot.

### 3. Shrink Partisi Windows

Disk Management → klik kanan C: → Shrink Volume → masukkan ukuran (MB) yang
mau dialokasikan ke Linux.

### 4. Hasil Layout Disk

```
Sebelum: [EFI 260MB][  Windows C: 475.91 GB (BitLocker)  ][Recovery 772MB]
Sesudah: [EFI 260MB][Windows C: 174.91GB][Unallocated 301GB][Recovery 772MB]
```

## Instalasi di Ruang Unallocated (Calamares Installer)

Manual Partitioning di installer:
```
EFI (existing 260MB)  → mount /boot/efi → JANGAN format
Windows C: (NTFS)     → JANGAN disentuh
Unallocated 285GB     → Format: Btrfs → mount: /
Unallocated 16GB      → Format: linux-swap
```

Hasil akhir (contoh dari device user):
```
[EFI 260MB][Windows 174GB][CachyOS Btrfs 278GB][Swap 22GB][Recovery 772MB]
```

## Fix Jam Berbeda Antar OS

Windows pakai UTC di RTC, Linux biasanya localtime — kalau tidak disamakan, jam
selalu selisih saat ganti OS.

```bash
# Cara cepat: set Linux pakai localtime seperti Windows
sudo timedatectl set-local-rtc 1 --adjust-system-clock
```

Cara lebih proper (fix di Windows, bukan di Linux):
```powershell
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /t REG_DWORD /d 1 /f
```

## Boot ke Installer

Restart → tombol boot menu device (F9 di HP) → pilih USB installer yang sudah
di-flash pakai Rufus (mode GPT, UEFI non-CSM, ISO Image mode).
