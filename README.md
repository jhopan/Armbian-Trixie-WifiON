# Armbian Trixie WiFi-ON Image (Prebuilt)

Repository ini berisi Armbian Trixie image yang sudah di-inject driver WiFi Realtek RTL8189FS dan kernel di-hold (locked) agar WiFi tidak hilang saat update sistem.

## 📦 Download Image

Lihat di [Releases](releases) untuk download file `.img.gz`.

## ✅ Yang Sudah Include di Image

- Armbian Trixie (Debian 13) - Kernel 6.12.107-ophub
- Driver WiFi `8189fs.ko` pre-installed
- Auto-load driver saat boot (`/etc/modules-load.d/8189fs.conf`)
- Kernel update **DISABLED** (`apt-mark hold`)
- `quick-install.sh` disimpan di `/root/` untuk reinstall driver jika dibutuhkan

## 🚀 Cara Pakai

### Langkah 1: Pilih DTB yang Sesuai (PENTING!)

Image ini defaultnya untuk **B860H**. Jika Anda menggunakan **HG680P**, Anda HARUS ganti DTB sebelum flash:

1. Flash image ke SD card pakai [Rufus](https://rufus.ie/) atau [balenaEtcher](https://www.balena.io/etcher/)
2. Buka partisi BOOT (FAT32) di Windows Explorer
3. Edit file `uEnv.txt` dengan Notepad
4. Ganti baris `FDT=` sesuai STB Anda:

| STB | DTB yang dipakai |
|-----|-------------------|
| **B860H** (default) | `FDT=/dtb/amlogic/meson-gxl-s905x-b860h.dtb` |
| **HG680P** | `FDT=/dtb/amlogic/meson-gxl-s905x-p212.dtb` |

5. Simpan file, eject SD card

### Langkah 2: Flash & Boot

1. Colok SD card ke STB
2. Tancapkan power, tunggu first boot (2-3 menit)
3. Login: `root` / `1234`
4. WiFi langsung ON! Cek dengan: `ip link show wlan0`

> 💡 Panduan ini juga ada di dalam image di `/root/README-WifiON.txt`

## 🔒 Kernel Locked

Kernel di-hold agar driver tidak hilang:
```bash
apt-mark hold linux-image-* linux-headers-* linux-dtb-*
```

Jika ingin update kernel (driver WiFi akan hilang, perlu reinstall):
```bash
apt-mark unhold linux-image-* linux-headers-* linux-dtb-*
armbian-update
# Setelah reboot, jalankan:
bash /root/quick-install.sh
```

## 🙏 Credits

- **JhopanStore** - Image builder, testing, dan dokumentasi
- **[ophub/amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian)** - Base Armbian image
- **[jhopan/Armbian-Wifi-on](https://github.com/jhopan/Armbian-Wifi-on)** - Driver source & prebuilt
- **[gustiarto/rtl8189fs-armbian](https://github.com/gustiarto/rtl8189fs-armbian)** - Driver source code