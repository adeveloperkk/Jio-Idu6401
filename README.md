# 🔧 OpenWRT Flash Guide (JIDU 6401 / Filogic)

<p align="center">
  <img src="https://img.shields.io/badge/OpenWRT-Flashing-green?style=for-the-badge">
  <img src="https://img.shields.io/badge/Platform-Filogic-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/Access-UART-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Status-Tested-success?style=for-the-badge">
</p>

---

## ⚠️ Disclaimer

This guide is for:

* Advanced users
* Firmware enthusiasts
* Embedded developers

❗ Incorrect steps may:

* Brick your router
* Break bootloader
* Require hardware recovery

---

## 📌 Overview

This guide walks you through:

* UART access setup
* U-Boot configuration
* RAM booting OpenWRT
* Flashing firmware safely
* Fixing boot configuration
* Performing final sysupgrade

---

## 🧰 Requirements

* UART adapter (USB to TTL)
* PC with Ethernet port
* TFTP server (Tftpd64)
* SCP tool (WinSCP)
* OpenWRT firmware files

---

## 🔐 Step 1: Login to U-Boot

* Connect via UART (115200 baud)
* Use terminal (PuTTY / Minicom)

### Credentials:

* Username: Last 8 digits of serial
* Password: < retrieve using below >

```id="7k1xmd"
/usr/bin/mfg_data get bootpass
```

---

## 🌐 Step 2: Setup TFTP Server

* Install **Tftpd64**
* Place firmware files in TFTP folder
* Select correct directory in TFTP

---

## 🌍 Step 3: Configure Network

### In U-Boot:

```id="2px9vn"
setenv ipaddr 192.168.1.1
setenv serverip 192.168.1.2
saveenv
```

### On PC:

* IP: `192.168.1.2`
* Subnet: `255.255.255.0`
* DNS1: `192.168.1.1`
* DNS2: `8.8.8.8`
* Gateway: *(leave empty)*

---

## 📦 Step 4: Boot OpenWRT in RAM

```id="z4n2bs"
tftpboot 0x46000000 openwrt-*-initramfs-kernel.bin
fdt addr $(fdtcontroladdr)
fdt rm /signature
bootm

```

👉 Use exact firmware filename

---

## ✅ Step 5: Temporary Boot

* OpenWRT runs in RAM
* ⚠️ Do NOT reboot

---

## 📁 Step 6: Transfer Firmware

Use SCP (WinSCP):

* Host: `192.168.1.1`
* User: `root`
* Password: *(empty)*

Upload file:

```id="x9q2we"
/tmp/factory.ubi
```

---

## 💾 Step 7: Flash Firmware

### Remove existing partition:

```id="h7k3lo"
ubidetach -m 5
```

### Flash new firmware:

```id="l8p1rc"
ubiformat /dev/mtd5 -y -f /tmp/factory.ubi
```

---

## 🔄 Step 8: Reboot

```id="m3s9dz"
reboot
```

---

## ⚙️ Step 9: Fix Boot Configuration

Enter U-Boot again and run:

```id="w5t8nx"
setenv bootcmd 'ubi read 46000000 kernel;fdt addr $(fdtcontroladdr);fdt rm /signature;bootm'
setenv dual_boot 0
setenv boot_image_slot 0
setenv upgrade_image_slot 0
saveenv
reset
```

---

## 📤 Step 10: Final Sysupgrade

Upload sysupgrade file to `/tmp`

Then:

```id="p4v7ac"
sysupgrade -n /tmp/openwrt*.bin
```

---

##Attach mtd6 partition if storage needed
## ⚠️ Important Notes

* Device uses **dual boot (mtd5 + mtd6)**
* You disabled dual boot for stability ✔️
* Always verify:

  * Kernel boots
  * LAN works
  * SSH access available

---

## 🚨 Troubleshooting

* ❌ Boot fails → Check bootcmd
* ❌ No LAN → Verify network config
* ❌ Flash error → Ensure correct partition

---

## 🚀 Pro Tips

* RAM boot first = safest method ✔️
* Always flash from `/tmp`
* Avoid persistent writes before testing
* Keep firmware filename simple

---

## 📺 YouTube Channel

🎥 Full tutorials & walkthroughs:
👉 https://www.youtube.com/@developerkk

---

## 👨‍💻 Author

**DeveloperKK**
🌐 https://developerkk.in

---

## 💖 Support

Buy Me a Coffee ☕
👉 https://razorpay.me/@elevatebharat

---

## 📄 License

For educational and research purposes only.

---
