# CachyOS Wiki — HP Pavilion Plus 14 Setup
> Dokumentasi lengkap setup CachyOS dari nol hingga fully configured daily driver.
> Dibuat: Juli 2026 | Author: Rama | AI Assistant: VIRA (Claude Sonnet 4.6)

---

## Daftar Isi
1. [[#Hardware Specs]]
2. [[#Riset Distro Linux]]
3. [[#Mengapa CachyOS]]
4. [[#Perbandingan CachyOS vs Ubuntu]]
5. [[#Persiapan Dual Boot]]
6. [[#Instalasi CachyOS]]
7. [[#Post-Installation Setup]]
8. [[#Fish Shell & Aliases]]
9. [[#TLP Power Management]]
10. [[#Thermald]]
11. [[#Firewall UFW]]
12. [[#Snapper & GRUB-BTRFS]]
13. [[#KDE Plasma Customization]]
14. [[#Aplikasi yang Terinstall]]
15. [[#System Maintenance]]
16. [[#Troubleshooting]]
17. [[#Backup & Migration]]

---

## Hardware Specs

| Komponen | Spesifikasi |
|---|---|
| **Model** | HP Pavilion Plus 14-ep1xxx |
| **CPU** | Intel Core Ultra 5 125H (Meteor Lake) |
| **Cores** | 14 core (4P + 8E + 2LP-E), 18 thread |
| **Boost Clock** | Up to 4.5 GHz |
| **GPU** | Intel Arc 7-core iGPU (aktif karena 16GB dual-channel) |
| **RAM** | 16GB DDR5 dual-channel |
| **Storage** | 475GB NVMe SSD (Btrfs) |
| **WiFi** | Intel CNVi (iwlwifi — zero Linux drama) |
| **Display** | 14" 1920x1080 60Hz |
| **OS** | CachyOS (dual boot dengan Windows 11) |
| **Kernel** | linux-cachyos 7.1.2-3 |
| **Desktop** | KDE Plasma 6.7 |
| **Shell** | Fish 4.7.1 |
| **Terminal** | Ghostty |

### Kenapa Hardware Ini Cocok untuk CachyOS

- **Meteor Lake** terlalu baru untuk Ubuntu 24.04 kernel 6.8 — Intel Arc freeze issues
- **CachyOS kernel 7.x** punya full Intel Arc (XE driver) support yang confirmed stable
- **Intel CNVi WiFi** = zero driver drama di semua distro Linux
- **16GB DDR5 dual-channel** = Intel Arc GPU aktif penuh (butuh min 16GB)
- **Core Ultra 5 125H** support AVX2 = dapat benefit x86-64-v3 compilation CachyOS
- **NPU Intel AI Boost** = hardware AI acceleration, support penuh di kernel 7.x

---

## Riset Distro Linux

### Ranking Distro Linux 2026 (Top 10)

| Rank | Distro | Base | Release | Cocok untuk |
|---|---|---|---|---|
| 1 | **CachyOS** | Arch | Rolling | Performance, Gaming, Modern hardware |
| 2 | **Fedora 44** | Independent | Semi-rolling | Developer, balanced |
| 3 | **Bazzite** | Fedora Atomic | Immutable | Gaming console-like |
| 4 | **Ubuntu 24.04 LTS** | Debian | LTS | Enterprise, beginner |
| 5 | **Linux Mint 22.3** | Ubuntu | LTS | Beginner, Windows migrant |
| 6 | **Pop!_OS 24.04** | Ubuntu | LTS | NVIDIA users, tiling |
| 7 | **Debian 12** | Independent | Stable | Server, sysadmin |
| 8 | **NixOS** | Independent | Declarative | DevOps, reproducible |
| 9 | **openSUSE Tumbleweed** | Independent | Rolling+QA | Enterprise sysadmin |
| 10 | **Arch Linux** | Independent | Rolling | Purist, DIY |

### Mengapa CachyOS #1 DistroWatch 18+ Bulan

- Paket dikompilasi ulang dengan **LTO, PGO, BOLT** post-link optimization
- **x86-64-v3/v4** targeting untuk CPU modern (AVX2, AVX-512)
- **BORE/EEVDF scheduler** — desktop responsif saat CPU load tinggi
- **Custom kernel** dengan latency patches
- **AUR access** penuh — repository terbesar di ekosistem Linux
- Multiple DE choice saat installer
- Benchmark Phoronix: **11.6% lebih cepat dari Ubuntu 24.04** di 75 workload test

---

## Mengapa CachyOS (bukan Ubuntu) untuk Hardware Ini

### 8 Alasan Konkrit

**1. Intel Arc GPU Stability**
- Ubuntu 24.04 kernel 6.8: freeze/crash issues dengan Intel Arc Meteor Lake
- CachyOS kernel 7.x: XE driver enabled by default, confirmed stable
- Community report: *"perfectly stable on Meteor Lake 155H, vanilla stack"*

**2. CPU P-State Tuning**
- Kernel 6.9+ diperlukan untuk proper Intel P-State EPP tuning Meteor Lake
- CachyOS kernel 7.x include semua perbaikan ini
- Result: tidak ada thermal throttling yang tidak perlu

**3. AVX2 Compilation**
- Ubuntu: compile untuk x86-64-v1 (generic, CPU 2008+)
- CachyOS: compile untuk x86-64-v3 (AVX2, CPU 2013+)
- Core Ultra 5 125H support penuh x86-64-v3
- Result: ~11% performa lebih cepat untuk compute workloads

**4. BORE Scheduler**
- Menangani arsitektur heterogen P-core/E-core/LP-E-core dengan lebih baik
- Desktop tetap responsif saat background task berat
- Input latency lebih rendah untuk semua task

**5. Intel NPU Support**
- Intel AI Boost NPU di Meteor Lake
- Full support di kernel 7.x
- Ubuntu 24.04 kernel 6.8: support terbatas

**6. Intel CNVi WiFi**
- Driver iwlwifi — works perfectly di semua distro
- Tidak ada Realtek rtw89 drama seperti HP 14 lower-end
- Zero setup required

**7. Mesa Terbaru**
- Ubuntu 24.04: Mesa frozen di 24.x
- CachyOS rolling: selalu Mesa terbaru (Mesa 26.1.4 di Juli 2026)
- Intel Arc performance improvement signifikan setiap Mesa update

**8. Tidak Ada Snap**
- Ubuntu makin paksa Snap — lebih lambat, auto-update tanpa izin
- CachyOS: semua package native APT atau Flatpak pilihan user
- Tidak ada overhead container, tidak ada late loading

---

## Perbandingan CachyOS vs Ubuntu

| Kategori | CachyOS | Ubuntu 24.04 LTS |
|---|---|---|
| **Raw Performance** | 9.7/10 | 8.2/10 |
| **Stabilitas** | 7.2/10 | 9.6/10 |
| **Kemudahan** | 7.0/10 | 9.5/10 |
| **Software terbaru** | 9.9/10 | 7.3/10 |
| **Ekosistem developer** | 8.2/10 | 9.8/10 |
| **Keamanan jangka panjang** | 6.5/10 | 9.7/10 |
| **Customizability** | 9.8/10 | 7.2/10 |
| **Komunitas** | 7.5/10 | 9.9/10 |
| **Intel Arc support** | ✅ Kernel 7.x | ⚠️ Kernel 6.8 bermasalah |
| **AVX2 packages** | ✅ x86-64-v3 | ❌ x86-64-v1 |
| **Snap** | ❌ Tidak ada | ⚠️ Dipaksakan |
| **Support lifetime** | Rolling | 5 tahun (10 Pro) |

### Rolling Release vs LTS — Perbedaan Fundamental

```
Ubuntu (LTS):
Ubuntu 22.04 → 2 tahun → Ubuntu 24.04 → 2 tahun → Ubuntu 26.04
- Package di-freeze selama 2 tahun
- Hanya dapat security patch
- Upgrade besar = ribet, kadang break

CachyOS (Rolling):
Install sekali → paru -Syu → paru -Syu → selamanya
- Selalu package terbaru
- Tidak ada upgrade besar
- Satu command update selamanya
```

---

## Persiapan Dual Boot

### Kondisi Disk Sebelum

```
[EFI 260MB][  Windows C: 475.91 GB (BitLocker)  ][Recovery 772MB]
```

### Langkah Pre-Installation (di Windows)

#### 1. Matikan BitLocker
```powershell
# PowerShell Admin
manage-bde -off C:

# Cek status (tunggu "Fully Decrypted")
manage-bde -status C:
```
⚠️ Tunggu proses decrypt selesai (~15-30 menit di SSD). Jangan shutdown selama proses.

#### 2. Matikan Fast Startup
```powershell
powercfg /h off
```
Atau: Control Panel → Power Options → "Turn on fast startup" → uncheck

**Kenapa penting:** Fast Startup = Windows tidak pernah benar-benar shutdown, partisi C: terkunci. Kalau tidak dimatikan = risiko Windows corrupt saat dual boot.

#### 3. Shrink Partisi Windows

Di **Disk Management** → klik kanan C: → Shrink Volume:
```
Enter amount to shrink: 308224 MB
```

Hasil: Windows 174.91 GB + Unallocated 301 GB

#### 4. Layout Disk Final

```
[EFI 260MB][Windows C: 174.91GB][Unallocated 301GB][Recovery 772MB]
```

### Rencana Partisi CachyOS

| Partisi | Ukuran | Filesystem | Mount |
|---|---|---|---|
| EFI (existing) | 260 MB | FAT32 | /boot/efi |
| Windows C: | 174.91 GB | NTFS | — |
| CachyOS root | ~285 GB | **Btrfs** | / |
| Swap | 16 GB | linux-swap | [swap] |
| Recovery | 772 MB | — | — |

---

## Instalasi CachyOS

### Download & Flash USB

1. Download ISO dari **cachyos.org/download** → pilih KDE Plasma
2. Flash dengan **Rufus** → GPT → UEFI (non CSM) → ISO Image mode

### Boot & Install

1. Restart → **F9** (HP Boot Menu) → pilih USB
2. Di Calamares installer → **Manual Partitioning**

### Konfigurasi Partisi di Calamares

```
EFI (existing 260MB)  → mount /boot/efi → JANGAN format
Windows C: (NTFS)     → JANGAN disentuh
Unallocated 285GB     → Format: Btrfs → mount: /
Unallocated 16GB      → Format: linux-swap
```

### Post-Install Disk Layout

```
[EFI 260MB][Windows 174GB][CachyOS Btrfs 278GB][Swap 22GB][Recovery 772MB]
```

---

## Post-Installation Setup

### Urutan Wajib Setelah Install

#### 1. Rate Mirrors (PERTAMA KALI)
```bash
sudo cachyos-rate-mirrors
```
Benchmark semua mirror, pilih tercepat dari Jakarta. Jalankan sebelum apapun.

#### 2. Update Sistem
```bash
sudo pacman -Syu
# Kalau ada kernel update → REBOOT dulu
sudo reboot
```

#### 3. Install LTS Kernel sebagai Fallback
```bash
sudo pacman -S linux-cachyos-lts linux-cachyos-lts-headers
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### 4. Setup Snapper
```bash
sudo pacman -S snapper grub-btrfs
sudo snapper -c root create-config /
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
sudo systemctl enable --now grub-btrfs.path
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### 5. Fix Clock Dual Boot (Windows/Linux)
```bash
# Set CachyOS pakai localtime seperti Windows
sudo timedatectl set-local-rtc 1 --adjust-system-clock

# Atau fix di Windows (lebih proper):
# PowerShell Admin:
# reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /t REG_DWORD /d 1 /f
```

#### 6. Setup Firewall UFW
```bash
sudo pacman -S ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo systemctl enable ufw
```

#### 7. Verifikasi x86-64-v3
```bash
/lib64/ld-linux-x86-64.so.2 --help | grep "x86-64-v3"
# Harus muncul: x86-64-v3 (supported, searched)
```

---

## Fish Shell & Aliases

### Lokasi Config
```
~/.config/fish/config.fish
```

### Complete config.fish

```fish
source /usr/share/cachyos-fish-config/cachyos-config.fish

# overwrite greeting
# potentially disabling fastfetch
#function fish_greeting
#     # smth smth
#end

# ════════════════════════════════════════
#  ALIASES — VIRA Command Center
#  HP Pavilion Plus 14 | CachyOS | rama
# ════════════════════════════════════════

# ── System Update ──
alias update='paru -Syu && flatpak update'

# ── Deep Cleanup ──
alias cleanup='sudo paccache -r && { pacman -Qtdq | sudo pacman -Rns - 2>/dev/null || true; } && flatpak uninstall --unused && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/* && rm -rf ~/.cache/paru/ && rm -rf ~/.cache/thumbnails/'

# ── Update + Cleanup Sekaligus ──
alias maintain='paru -Syu && flatpak update && sudo paccache -r && sudo journalctl --vacuum-time=2weeks && rm -rf ~/.cache/paru/'

# ── Package Management ──
alias install='paru -S'
alias remove='sudo pacman -Rns'
alias search='paru -Ss'
alias pkginfo='paru -Si'
alias listpkg='pacman -Qe'

# ── System Health ──
alias syscheck='echo "=== Disk ===" && df -h / && echo "=== RAM ===" && free -h && echo "=== Suhu ===" && sensors 2>/dev/null || echo "run: sudo pacman -S lm_sensors"'
alias diskusage='du -sh /* 2>/dev/null | sort -rh | head -20'
alias logerror='sudo journalctl -p err -n 20'
alias sysinfo='fastfetch'
alias ports='ss -tulnp'
alias myip='ip addr show | grep "inet " | grep -v 127.0.0.1'
alias myip-public='curl -s ifconfig.me'

# ── Snapper Snapshots ──
alias snaplist='sudo snapper list'
alias snapnow='sudo snapper -c root create --description'

# ── Mirror Update ──
alias updatemirrors='sudo cachyos-rate-mirrors'

# ── Power ──
alias reboot='sudo reboot'
alias off='sudo poweroff'

# ── TLP Power Control ──
alias acmode='sudo tlp ac && echo "⚡ AC/Performance mode aktif"'
alias batmode='sudo tlp bat && echo "🔋 Battery mode aktif"'
alias tlpcheck='sudo tlp-stat -s'
alias batcheck='sudo tlp-stat -b'

# ── Git ──
alias gs='git status'
alias ga='git add .'
alias gc='git commit -m'
alias gp='git push'
alias gl='git log --oneline --graph --decorate -10'
alias gd='git diff'
```

### Reload Config
```bash
source ~/.config/fish/config.fish
```

### Alias Reference

| Alias | Command | Fungsi |
|---|---|---|
| `update` | paru -Syu && flatpak update | Update semua |
| `maintain` | update + cleanup ringan | Weekly maintenance |
| `cleanup` | Nuclear cleaner | Monthly deep clean |
| `install` | paru -S | Install package |
| `remove` | pacman -Rns | Hapus + dependency |
| `search` | paru -Ss | Cari package |
| `pkginfo` | paru -Si | Info detail package |
| `listpkg` | pacman -Qe | List package manual |
| `syscheck` | disk + RAM + suhu | Health check |
| `diskusage` | du -sh sorted | Cek storage |
| `logerror` | journalctl -p err | Error log |
| `sysinfo` | fastfetch | System info |
| `ports` | ss -tulnp | Port terbuka |
| `myip` | ip addr show | IP lokal |
| `myip-public` | curl ifconfig.me | IP public |
| `snaplist` | snapper list | Daftar snapshot |
| `snapnow "desc"` | snapper create | Buat snapshot manual |
| `updatemirrors` | cachyos-rate-mirrors | Update mirror |
| `reboot` | sudo reboot | Restart |
| `off` | sudo poweroff | Shutdown |
| `acmode` | tlp ac | Force performance |
| `batmode` | tlp bat | Force powersave |
| `tlpcheck` | tlp-stat -s | Status TLP |
| `batcheck` | tlp-stat -b | Health battery |
| `gs` | git status | Git status |
| `ga` | git add . | Stage semua |
| `gc "msg"` | git commit -m | Commit |
| `gp` | git push | Push |
| `gl` | git log --oneline | Log kompak |
| `gd` | git diff | Lihat perubahan |

---

## TLP Power Management

### Kenapa TLP (bukan power-profiles-daemon)

CachyOS sudah include TLP by default dan mask power-profiles-daemon karena konflik. TLP lebih powerful — bisa set battery threshold, CPU governor per-device, WiFi power saving, dll.

### Konfigurasi /etc/tlp.conf

Setting yang diubah dari default:

```ini
# CPU Governor
CPU_SCALING_GOVERNOR_ON_AC=performance
CPU_SCALING_GOVERNOR_ON_BAT=powersave
CPU_SCALING_GOVERNOR_ON_SAV=powersave

# CPU Energy Policy
CPU_ENERGY_PERF_POLICY_ON_AC=performance
CPU_ENERGY_PERF_POLICY_ON_BAT=balance_power
CPU_ENERGY_PERF_POLICY_ON_SAV=power

# Battery Health Threshold
START_CHARGE_THRESH_BAT0=20
STOP_CHARGE_THRESH_BAT0=80
```

### Edit via sed (cara cepat)
```bash
sudo sed -i 's/#CPU_SCALING_GOVERNOR_ON_AC=performance/CPU_SCALING_GOVERNOR_ON_AC=performance/' /etc/tlp.conf
sudo sed -i 's/#CPU_SCALING_GOVERNOR_ON_BAT=powersave/CPU_SCALING_GOVERNOR_ON_BAT=powersave/' /etc/tlp.conf
sudo sed -i 's/#CPU_ENERGY_PERF_POLICY_ON_AC=balance_performance/CPU_ENERGY_PERF_POLICY_ON_AC=performance/' /etc/tlp.conf
sudo sed -i 's/#CPU_ENERGY_PERF_POLICY_ON_BAT=balance_power/CPU_ENERGY_PERF_POLICY_ON_BAT=balance_power/' /etc/tlp.conf
sudo sed -i 's/#START_CHARGE_THRESH_BAT0=75/START_CHARGE_THRESH_BAT0=20/' /etc/tlp.conf
sudo sed -i 's/#STOP_CHARGE_THRESH_BAT0=80/STOP_CHARGE_THRESH_BAT0=80/' /etc/tlp.conf
sudo tlp start
```

### Cara Kerja

```
Charger masuk  → TLP auto: CPU Governor = performance ⚡
Cabut charger  → TLP auto: CPU Governor = powersave 🔋
Battery        → Charge 20-80% saja (jaga health jangka panjang)
```

### Kenapa 20-80% Battery Threshold?

Battery Li-Ion degradasi paling cepat saat:
- Charge penuh 100% terus-menerus (stress di high voltage)
- Discharge sampai 0% (stress di low voltage)

Range 20-80% = sweet spot untuk longevity. Estimasi memperpanjang battery life 2-3x lipat.

### Verifikasi
```bash
tlpcheck
# Harus menunjukkan:
# TLP profile = performance/AC (kalau charger masuk)
# atau
# TLP profile = bat (kalau pakai baterai)
```

---

## Thermald

### Fungsi

Intel Thermal Daemon — monitor suhu CPU real-time dan ambil tindakan sebelum thermal throttle paksa terjadi.

```
Tanpa thermald:
CPU panas → kernel throttle paksa → FPS drop drastis → tidak terkontrol

Dengan thermald:
CPU naik → thermald detect early → kurangi P-state sedikit →
suhu turun → performa tetap konsisten → tidak ada cliff-drop
```

### Setup
```bash
sudo pacman -S thermald
sudo systemctl enable --now thermald
```

### Hasil (setelah thermald aktif)

```
Sebelum: 69-75°C idle
Sesudah: 53-59°C idle
Penurunan: 16-22°C hanya dari thermald + TLP yang benar
```

---

## Firewall UFW

### Setup
```bash
sudo pacman -S ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo systemctl enable ufw
```

### Konfigurasi Aktif

```
Firewall type:          ufw
Default Incoming:       Ignore (lebih aman dari Deny)
Default Outgoing:       Allow
KDE Connect IPv4/IPv6:  Allow incoming (untuk sync HP)
Status:                 Enabled ✅
```

### Kenapa "Ignore" lebih baik dari "Deny"

- **Deny** = tolak koneksi dan kirim pesan balik ke attacker (attacker tahu ada sistem)
- **Ignore** = diam saja, seolah port tidak ada (attacker tidak tahu ada sistem)

---

## Snapper & GRUB-BTRFS

### Fungsi

Snapper membuat snapshot Btrfs otomatis sebelum dan sesudah setiap pacman transaction. Kalau update rusak → boot ke snapshot sebelumnya dari GRUB.

### Setup Lengkap
```bash
sudo pacman -S snapper grub-btrfs

# Buat config untuk root partition
sudo snapper -c root create-config /

# Aktifkan auto-snapshot
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer

# Aktifkan GRUB btrfs (snapshot muncul di boot menu)
sudo systemctl enable --now grub-btrfs.path

# Update GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Cara Kerja snap-pac

snap-pac sudah terinclude di CachyOS — setiap kali `pacman -Syu` dijalankan:
1. Snapshot dibuat **sebelum** update (pre)
2. Update dijalankan
3. Snapshot dibuat **sesudah** update (post)

Ini otomatis tanpa konfigurasi tambahan.

### Rollback via GRUB

Kalau sistem tidak bisa boot setelah update:
1. Restart → di GRUB pilih "CachyOS Snapshots"
2. Pilih snapshot sebelum update
3. Boot → sistem kembali ke state sebelumnya

### Rollback via Terminal
```bash
# Lihat daftar snapshot
snaplist

# Rollback ke snapshot tertentu
sudo snapper rollback [nomor]
```

### Buat Snapshot Manual
```bash
snapnow "sebelum install DE baru"
snapnow "sebelum update besar"
```

---

## KDE Plasma Customization

### Theme Stack

| Komponen | Pilihan |
|---|---|
| **Global Theme** | Layan Dark |
| **Icons** | Tela Circle Dark |
| **Application Style** | Kvantum (Layan) |
| **GTK Theme** | Layan-Dark |
| **Wallpaper** | Layan collection |
| **Fonts** | Inter 11pt (UI), JetBrains Mono 11pt (fixed) |
| **Cursor** | Layan |

### Install Theme Stack
```bash
# Kvantum engine
sudo pacman -S kvantum

# Layan complete stack
paru -S layan-kde-git layan-gtk-theme-git tela-icon-theme-git

# Fonts
sudo pacman -S inter-font ttf-jetbrains-mono ttf-jetbrains-mono-nerd

# Refresh font cache
fc-cache -fv && kbuildsycoca6 --noincremental
```

### Setup Kvantum
```bash
kvantummanager
# → Change/Delete Theme → pilih Layan → Use this theme
```

### Urutan Apply di System Settings

1. **Global Theme** → Layan Dark → Apply (centang semua)
2. **Application Style** → Kvantum → Apply
3. **Icons** → Tela Circle Dark → Apply
4. **Text & Fonts**:
   - General: Inter 11pt
   - Fixed width: JetBrains Mono 11pt
   - Small: Inter 9pt
   - Toolbar/Menu/Window title: Inter 11pt
   - Sub-pixel rendering: RGB
   - Hinting: Slight
5. **GTK Application Style** → Layan-Dark

### Animations (Rekomendasi)

| Setting | Nilai |
|---|---|
| Global speed | 75% ke kanan (fast tapi visible) |
| Window open/close | Glide |
| Window maximize | Stretch |
| Window minimize | **Magic Lamp** ← highlight |
| Window full screen | Stretch |
| Peek at desktop | Window Aperture |
| Virtual desktop switch | Slide |

### Shortcut KDE yang Penting

| Shortcut | Fungsi |
|---|---|
| `Super` | Overview semua window (dikonfigurasi) |
| `Super + W` | Toggle Overview (default) |
| `Super + Tab` | Switch antar window |
| `Alt + Tab` | Switch window |
| `Super + D` | Show desktop |
| `Ctrl + F1/F2/F3` | Pindah Virtual Desktop |
| `Ctrl + Alt + F1-F6` | TTY (terminal darurat) |
| `Ctrl + Alt + F7` | Kembali ke desktop grafis |
| `Ctrl + Alt + T` | Buka terminal (dikonfigurasi ke Ghostty) |

### Default Applications

| Kategori | Aplikasi |
|---|---|
| Web browser | Brave |
| Terminal | Ghostty |
| File manager | Dolphin |
| Text editor | Kate |
| PDF viewer | Okular |
| Archive | Ark |
| Image viewer | Gwenview |
| Video/Audio | Haruna |

---

## Aplikasi yang Terinstall

### Via pacman (Official Repo)
```bash
sudo pacman -S \
  ghostty \
  inter-font \
  ttf-jetbrains-mono \
  ttf-jetbrains-mono-nerd \
  kvantum \
  ufw \
  thermald \
  gamemode lib32-gamemode \
  steam \
  docker docker-compose \
  python python-pip python-virtualenv \
  nodejs npm \
  git base-devel \
  btop \
  wget curl unzip \
  neofetch \
  okular \
  vlc \
  telegram-desktop \
  github-cli \
  lm_sensors
```

### Via paru (AUR)
```bash
paru -S \
  brave-bin \
  claude-desktop-appimage \
  onlyoffice-bin \
  layan-kde-git \
  layan-gtk-theme-git \
  tela-icon-theme-git \
  proton-ge-custom-bin \
  protonup-qt \
  cursor-bin \
  obsidian \
  ghostty
```

### Via Flatpak
```bash
flatpak install flathub \
  md.obsidian.Obsidian \
  org.libreoffice.LibreOffice
```

---

## Ghostty Terminal

### Lokasi Config
```
~/.config/ghostty/config
```

### Config Optimal
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

### Custom Themes

Simpan di `~/.config/ghostty/themes/`:

**Claude Code Dark** (`~/.config/ghostty/themes/claude-code-dark`):
```
background = 141413
foreground = faf9f5
cursor-color = d97757
cursor-text = 141413
selection-background = 2d2d2b
selection-foreground = faf9f5
```

**Vercel** (`~/.config/ghostty/themes/vercel`):
```
background = #101010
foreground = #fafafa
cursor-color = #f32882
```

Aktifkan di config:
```ini
theme = claude-code-dark
```

### Cek Themes Tersedia
```bash
ls /usr/share/ghostty/themes/ | grep -i nama
```

---

## Package Management

### Paru — AUR Helper Utama

```bash
# Update semua (sistem + AUR)
paru -Syu

# Install package
paru -S nama-package

# Hapus package + dependency
sudo pacman -Rns nama-package

# Search package
paru -Ss keyword

# Info package
paru -Si nama-package

# List package terinstall
pacman -Qe          # yang diinstall manual
pacman -Qm          # yang dari AUR
pacman -Qtdq        # orphan packages
```

### Flatpak

```bash
# Update semua Flatpak
flatpak update

# Install
flatpak install flathub com.example.App

# Uninstall
flatpak uninstall com.example.App

# Hapus yang tidak terpakai
flatpak uninstall --unused
```

### Update Rutin (Mingguan)
```bash
paru -Syu && flatpak update
# atau pakai alias:
update
```

---

## System Maintenance

### Weekly Routine
```bash
update    # update semua package
```

### Monthly Routine
```bash
maintain  # update + cleanup ringan
# atau
cleanup   # nuclear clean (hapus semua cache)
```

### Cleanup Manual
```bash
# Cache package lama
sudo paccache -r

# Hapus orphan packages
sudo pacman -Rns $(pacman -Qtdq)

# Cache Flatpak
flatpak uninstall --unused

# Log lama
sudo journalctl --vacuum-time=2weeks

# Cache user
rm -rf ~/.cache/*
rm -rf ~/.cache/paru/
rm -rf ~/.cache/thumbnails/

# Cek disk usage
df -h /
```

---

## System Health Check

### Monitoring dengan btop
```bash
btop
```

### Quick Health Check
```bash
syscheck
# Output: Disk usage + RAM + Suhu CPU
```

### Metrics Normal

| Metrik | Normal | Warning |
|---|---|---|
| CPU temp idle | 50-65°C | >80°C |
| CPU temp load | 70-85°C | >95°C |
| RAM idle | <30% | >80% |
| Disk usage | <70% | >85% |
| Swap usage | <5% | >50% |

---

## Troubleshooting

### Keyring Error saat Update

```
error: cachyos-extra-v3: signature from "CachyOS" is invalid
```

Fix:
```bash
sudo pacman -S cachyos-keyring
sudo pacman-key --populate cachyos
paru -Syu
```

### Sistem Tidak Bisa Boot setelah Update

1. Di GRUB → pilih **CachyOS Snapshots**
2. Pilih snapshot sebelum update (pre-*)
3. Boot → sistem kembali normal
4. Rollback permanent: `sudo snapper rollback [nomor]`

### Package Build Error di AUR

```bash
# Hapus cache build yang corrupt
rm -rf ~/.cache/paru/clone/nama-package/
paru -S nama-package
```

### Pacman Partial Update Hang

```bash
# Kill dan clean
sudo pkill pacman
sudo rm /var/lib/pacman/db.lck
sudo pacman -Syu
```

### Font Baru Tidak Muncul di Dialog

```bash
fc-cache -fv
kbuildsycoca6 --noincremental
# Tutup dan buka ulang System Settings
```

### Service Tidak Jalan

```bash
# Cek status
systemctl status nama-service

# Enable dan start
sudo systemctl enable --now nama-service

# Lihat log error
sudo journalctl -u nama-service -n 50
```

---

## Backup & Migration ke Laptop Baru

### Export Package Lists
```bash
pacman -Qe | awk '{print $1}' > ~/packages-official.txt
pacman -Qm | awk '{print $1}' > ~/packages-aur.txt
flatpak list --app --columns=application > ~/packages-flatpak.txt
```

### Backup Configs & Data
```bash
# Semua config + home ke external drive
rsync -avh --progress ~/.config /run/media/rama/ExternalDrive/backup/
rsync -avh --progress ~/.local/share /run/media/rama/ExternalDrive/backup/
rsync -avh --progress /home/rama/ /run/media/rama/ExternalDrive/home-backup/

# System configs
sudo cp /etc/tlp.conf ~/backup-system/
sudo cp -r /etc/ufw ~/backup-system/

# SSH Keys
cp -r ~/.ssh ~/backup/ && chmod 700 ~/backup/.ssh

# Git config
cp ~/.gitconfig ~/backup/
```

### Restore di Laptop Baru

```bash
# 1. Install CachyOS fresh → rate mirrors → update
sudo cachyos-rate-mirrors && sudo pacman -Syu

# 2. Install paru
sudo pacman -S paru

# 3. Restore packages
sudo pacman -S --needed $(cat packages-official.txt)
paru -S --needed $(cat packages-aur.txt)
flatpak install $(cat packages-flatpak.txt)

# 4. Restore configs
rsync -avh /path/to/backup/.config/ ~/.config/
rsync -avh /path/to/backup/.local/share/ ~/.local/share/

# 5. Restore home data
rsync -avh /path/to/home-backup/ /home/rama/

# 6. Restore system configs
sudo cp backup-system/tlp.conf /etc/
sudo systemctl enable --now tlp thermald

# 7. Setup Snapper
sudo snapper -c root create-config /
sudo systemctl enable --now snapper-timeline.timer snapper-cleanup.timer

# 8. Reload Fish config
source ~/.config/fish/config.fish
```

**Estimasi waktu:** ~45 menit (vs 3-4 jam di Ubuntu)

---

## Services yang Aktif

```bash
# Cek semua service penting
systemctl status tlp          # Power management
systemctl status thermald     # Thermal management
systemctl status ufw          # Firewall
systemctl status docker       # Container runtime
systemctl status snapper-*    # Snapshot automation
systemctl status grub-btrfs   # GRUB snapshot integration
```

---

## Informasi Sistem

### Cek Versi & Status

```bash
# Info sistem lengkap
fastfetch

# Versi OS
cat /etc/os-release

# Versi kernel
uname -r

# CPU info
lscpu | grep -E "Model name|CPU(s)|Thread"

# GPU info
lspci | grep -i vga

# RAM
free -h

# Disk
df -h /

# Suhu
sensors
```

### Output neofetch Referensi

```
OS:      CachyOS x86_64
Host:    HP Laptop 14-ep1xxx
Kernel:  Linux 7.1.2-3-cachyos
DE:      KDE Plasma 6.7.1
WM:      KWin (Wayland)
CPU:     Intel Core Ultra 5 125H (14) @ 4.50 GHz
GPU:     Intel Arc Graphics @ 2.20 GHz [Integrated]
Memory:  ~2.5 GiB / 15.1 GiB
Disk:    ~35 GiB / 278 GiB (Btrfs)
```

---

## Catatan Penting

> ⚠️ **Rolling Release Warning**
> CachyOS adalah rolling release. Jalankan `update` minimal seminggu sekali.
> Jangan skip update lebih dari 2 minggu untuk menghindari partial upgrade issues.

> 💡 **Gunakan Terminal untuk Package Management**
> Jangan pakai GUI package manager (Pamac, KDE Discover) untuk system packages.
> Selalu gunakan `paru` atau `pacman` via terminal.

> 🔒 **Snapper adalah Jaring Pengaman**
> Sebelum eksperimen besar: `snapnow "deskripsi sebelum eksperimen"`
> Ini bisa menyelamatkan sistem kalau ada yang salah.

> 🔋 **Battery Threshold 20-80%**
> Battery tidak akan charge di atas 80% — ini by design untuk menjaga kesehatan
> battery jangka panjang. Kalau butuh 100% untuk perjalanan panjang:
> `sudo tlp fullcharge`

---

*Last updated: Juli 2026 | CachyOS Rolling | Kernel 7.1.2-3-cachyos*
