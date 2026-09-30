<div align="center">

# 🛜 Armbian Trixie WiFi-ON Image

### Armbian Trixie (Debian 13) untuk STB B860H / HG680P
### dengan WiFi Internal Realtek RTL8189FS Siap Pakai

[![Version](https://img.shields.io/badge/Version-v1.0.0-blue.svg)](releases)
[![Kernel](https://img.shields.io/badge/Linux%20Kernel-6.12.107--ophub-green.svg)]()
[![Distro](https://img.shields.io/badge/Distro-Armbian%20Trixie%20(Debian%2013)-cyan.svg)]()
[![WiFi](https://img.shields.io/badge/WiFi-Realtek%20RTL8189FS-red.svg)]()
[![Status](https://img.shields.io/badge/WiFi%20ON-Tested%203%20Devices-brightgreen.svg)]()

### ⚡ Flash. Boot. WiFi ON. Tanpa ribet.

[Download Image](releases) · [Driver Repository](https://github.com/jhopan/Armbian-Wifi-on) · [Report Issue](issues)

</div>

---

## 📖 Tentang Project

Repo ini menyediakan **Armbian image siap pakai** untuk STB Amlogic S905X (ZTE B860H / FiberHome HG680P) yang sudah di-inject:

- ✅ Driver WiFi Realtek RTL8189FS (`8189fs.ko`)
- ✅ Auto-load driver saat boot
- ✅ Kernel di-hold (locked) supaya WiFi tidak hilang saat update
- ✅ Script `quick-install.sh` untuk reinstall driver

> 💡 Butuh driver saja (tanpa image)? Kunjungi **[Armbian-Wifi-on](https://github.com/jhopan/Armbian-Wifi-on)**

---

## 📦 Download

Buka halaman [**Releases**](releases) dan download file:

```
Armbian-Trixie-6.12.107-WifiON-B860H-HG680P.img.gz
```

Ukuran: ~829 MB

---

## ✅ Tested & Working

| Device | RAM | Kernel | Status |
|--------|-----|--------|--------|
| **ZTE B860H V1** | 1GB | 6.12.107-ophub | ✅ WiFi ON |
| **ZTE B860H V2** | 2GB | 6.6.193-ophub | ✅ WiFi ON |
| **FiberHome HG680P** | - | 6.12.107-ophub | ✅ WiFi ON |

---

## 🚀 Quick Start

### Langkah 1: Pilih DTB Sesuai STB Anda

Image ini default-nya untuk **B860H**. Jika STB Anda **HG680P**, Anda **WAJIB** ganti DTB di file `uEnv.txt` sebelum flash.

1. Flash image ke SD card pakai [Rufus](https://rufus.ie/) atau [balenaEtcher](https://www.balena.io/etcher/)
2. Buka partisi **BOOT** (FAT32) di Windows Explorer
3. Edit file **`uEnv.txt`** dengan Notepad
4. Cari baris `FDT=`, lalu ganti sesuai STB Anda:

<div align="center">

| STB Anda | Isi baris `FDT=` di `uEnv.txt` |
| :---: | :--- |
| **B860H** (default) | `FDT=/dtb/amlogic/meson-gxl-s905x-b860h.dtb` |
| **HG680P** | `FDT=/dtb/amlogic/meson-gxl-s905x-p212.dtb` |

</div>

5. Simpan file, eject SD card

> ⚠️ **Jika salah pilih DTB, STB tidak akan booting (layar hitam).** Cabut SD card, edit `uEnv.txt` dari PC, lalu coba lagi.
> 
> 💡 Panduan ini juga tersimpan di dalam image di `/root/README-WifiON.txt`

### Langkah 2: Flash & Boot

1. Colok SD card ke STB
2. Tancapkan power, tunggu first boot (2-3 menit)
3. Login: `root` / `1234`
4. WiFi langsung ON! Verifikasi dengan:

```bash
ip link show wlan0
```

Jika muncul `wlan0`, **WiFi berhasil diaktifkan!** 🎉

Sekarang connect ke WiFi:

```bash
nmtui   # Menu interaktif
# atau
nmcli device wifi connect "NAMA_WIFI" password "PASSWORD_WIFI" ifname wlan0
```

---

## 🔒 Kernel Locked

Kernel sengaja di-hold (dikunci) supaya driver WiFi tidak hilang saat sistem di-update:

```bash
apt-mark hold linux-image-* linux-headers-* linux-dtb-*
```

### Jika Ingin Update Kernel

> ⚠️ Update kernel akan menghapus driver WiFi. Anda perlu reinstall driver setelahnya.

```bash
# 1. Buka kunci kernel
apt-mark unhold linux-image-* linux-headers-* linux-dtb-*

# 2. Update kernel
armbian-update

# 3. Reboot
reboot

# 4. Setelah reboot, install ulang driver
bash /root/quick-install.sh
```

---

## ⚙️ Apa Yang Sudah Include?

<div align="center">

| Komponen | Status | Keterangan |
| :--- | :---: | :--- |
| Armbian Trixie (Debian 13) | ✅ | Base image dari ophub |
| Kernel 6.12.107-ophub | ✅ | LTS, stabil |
| Driver `8189fs.ko` | ✅ | Pre-installed di `/lib/modules/` |
| Auto-load saat boot | ✅ | `/etc/modules-load.d/8189fs.conf` |
| Kernel update disabled | ✅ | `apt-mark hold` |
| `quick-install.sh` | ✅ | Di `/root/` untuk reinstall driver |
| First-boot fixup service | ✅ | Otomatis hold kernel & depmod |

</div>

---

## 🛠️ Troubleshooting

<details>
<summary><b>🔧 WiFi tidak muncul setelah boot</b></summary>

```bash
# Cek apakah chip terdeteksi
dmesg | grep -iE "sdio|mmc0|8189"

# Cek apakah driver ter-load
lsmod | grep 8189fs

# Manual load
modprobe 8189fs
```
</details>

<details>
<summary><b>🔧 Ada dua WiFi (wlan0 dan wlan1)</b></summary>

Ini normal. Driver Realtek mengaktifkan Concurrent Mode:
- `wlan0` → untuk connect ke WiFi (Station mode)
- `wlan1` → untuk Hotspot/AP atau WiFi Direct

Gunakan `wlan0` untuk koneksi internet. `wlan1` biarkan saja.
</details>

<details>
<summary><b>🔧 STB tidak booting (layar hitam)</b></summary>

Kemungkinan DTB salah. Cabut SD card, edit `uEnv.txt` di partisi BOOT dari PC, pastikan DTB sesuai dengan STB Anda (B860H atau HG680P).
</details>

<details>
<summary><b>🔧 WiFi connect tapi tidak bisa internet</b></summary>

```bash
ip addr show wlan0
systemctl restart NetworkManager
nmcli device wifi connect "NAMA_WIFI" password "PASSWORD_WIFI" ifname wlan0
```
</details>

<details>
<summary><b>🔧 nmtui tidak menampilkan daftar WiFi</b></summary>

Gunakan command line langsung:
```bash
nmcli device wifi connect "NAMA_WIFI" password "PASSWORD_WIFI" ifname wlan0
```
</details>

---

## 📦 Build System

Image ini di-build otomatis menggunakan **GitHub Actions**:
1. Download base image dari ophub
2. Mount partisi rootfs
3. Inject driver `8189fs.ko` ke `/lib/modules/`
4. Inject `quick-install.sh` ke `/root/`
5. Setup first-boot fixup service (hold kernel + depmod)
6. Compress dan upload ke Release

Build time: ~5 menit

---

## 🙏 Credits & References

<div align="center">

| Nama | Kontribusi |
| :--- | :--- |
| **JhopanStore** | Image builder, testing (3 device), dokumentasi |
| **[ophub](https://github.com/ophub/amlogic-s9xxx-armbian)** | Base Armbian image & kernel |
| **[Armbian-Wifi-on](https://github.com/jhopan/Armbian-Wifi-on)** | Driver source & prebuilt |
| **[gustiarto](https://github.com/gustiarto/rtl8189fs-armbian)** | Driver source code |
| **[jwrdegoede](https://github.com/jwrdegoede/rtl8189ES_linux)** | Original driver |
| **[alive4ever](https://github.com/alive4ever/rtl8189fs-armbian-current-meson64)** | DKMS reference |

</div>

---

<div align="center">

### ⭐ Jika project ini membantu, berikan star ya!

**[Download Image](releases)** · **[Driver Repository](https://github.com/jhopan/Armbian-Wifi-on)** · **[Report Issue](issues)**

</div>
