# LINUX INSTALL FEST — TROUBLESHOOTING RUNBOOK
*(Use this document ONLY when standard procedures fail or errors occur.)*

---

## 1. BIOS & PRE-BOOT FAILURES

### Problem 1.1: Internal NVMe/SSD drive not visible in Ubuntu installer
* **Root Cause:** Storage controller is operating in proprietary Intel VMD / RAID / RST mode.
* **Resolution:**
  1. Reboot machine and enter BIOS (`F2`/`F10`/`F12`/`Del`).
  2. Locate **SATA Operation**, **Storage Configuration**, or **NVMe Settings**.
  3. Change controller mode from `RST / VMD / RAID` to **`AHCI`**.
  4. Save changes, reboot, and restart installer.

### Problem 1.2: USB drive does not appear in boot menu
* **Root Cause:** Secure boot restrictions, corrupted flash, or CSM/Legacy mismatch.
* **Resolution:**
  1. Confirm **Secure Boot** is set to `Disabled` in BIOS.
  2. Ensure **Fast Boot** is `Disabled`.
  3. Verify BIOS is set to **UEFI Only** (Disable CSM/Legacy mode).
  4. Swap USB drive to a direct motherboard/laptop port (avoid unpowered USB hubs).

---

## 2. INSTALLATION & RAM INJECTION FAILURES

### Problem 2.1: System crashes / drops to initramfs after adding `toram`
* **Root Cause:** System has insufficient physical RAM (<6 GB), causing an Out-Of-Memory (OOM) panic while copying the ISO image to tempfs.
* **Resolution:**
  1. Hard power-off the laptop.
  2. Re-insert USB drive.
  3. Boot without modifying the GRUB command line (do **NOT** add `toram`).
  4. Keep the USB drive plugged in for the entire install process.

### Problem 2.2: Installer hangs on Subiquity loading screen
* **Root Cause:** Corrupted live image flash or GPU driver lockup during Plymouth splash screen.
* **Resolution:**
  1. Force reboot. At the GRUB menu, highlight `Try or Install Ubuntu Server` and press `e`.
  2. Locate line starting with `linux`.
  3. Append `nomodeset` to the end of the parameters line.
  4. Press `F10` to boot with generic display drivers.

---

## 3. PARTITIONING & STORAGE EMERGENCY PROCEDURES

### Problem 3.1: Windows Disk Management refuses to shrink C: drive beyond a small amount
* **Root Cause:** Windows unmovable system files (pagefile, hibernation file, system restore shadow copies) located at the end of the volume.
* **Resolution:**
  1. Boot into Windows as Administrator.
  2. Open Command Prompt (`cmd`) as Admin and disable hibernation:
     ```cmd
     powercfg /h off
     ```
  3. Temporarily disable Pagefile: `System Properties > Advanced > Performance Settings > Advanced > Virtual Memory > No paging file`.
  4. Reboot Windows, open `diskmgmt.msc`, and re-attempt shrink.
  5. Re-enable pagefile after shrink completes.

### Problem 3.2: Accidental modification / deletion of existing EFI partition
* **Root Cause:** Operator formatted the existing FAT32 boot partition during custom layout.
* **Resolution (Emergency EFI Repair):**
  1. Complete Linux installation.
  2. Boot into Linux, open terminal, and reinstall GRUB to EFI:
     ```bash
     sudo grub-install /dev/nvme0n1 # adjust to actual disk ID
     sudo update-grub
     ```
  3. To restore Windows bootloader, prepare a Windows Recovery USB, boot into command prompt, and run:
     ```cmd
     bootrec /fixmbr
     bootrec /fixboot
     bcdboot C:\Windows
     ```

---

## 4. NETWORKING & CAPTIVE PORTAL FAILURES

### Problem 4.1: `nmcli dev` shows no wireless interfaces (`wlan0` missing)
* **Root Cause:** Wi-Fi card requires proprietary drivers (e.g., Realtek `rtl8821ce`, Broadcom `b43`, Wi-Fi 7 chipsets).
* **Resolution:**
  1. Connect internet via **USB Tethering** using a mobile phone connected to Wi-Fi.
  2. Alternatively, attach a USB-to-Ethernet dongle or offline driver payload USB.
  3. Run automatic driver installation:
     ```bash
     sudo ubuntu-drivers install
     ```

### Problem 4.2: Captive bypass script reports success, but `ping google.com` fails
* **Root Cause:** DNS resolution failure or invalid `/etc/resolv.conf` entries.
* **Resolution:**
  1. Test direct IP connectivity:
     ```bash
     ping -c 3 8.8.8.8
     ```
  2. If IP ping works but domain ping fails, override system DNS manually:
     ```bash
     echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
     ```

---

## 5. POST-INSTALL & DESKTOP ENVIRONMENT FAILURES

### Problem 5.1: Laptop boots straight into Windows, skipping GRUB menu
* **Root Cause:** Windows Boot Manager remains prioritized at the UEFI firmware level.
* **Resolution:**
  1. Enter BIOS setup on reboot.
  2. Navigate to **Boot Priority / Boot Order**.
  3. Move **`ubuntu`** or **`GRUB`** to position #1, above `Windows Boot Manager`.
  4. Save and exit.

### Problem 5.2: Dual-boot GRUB menu appears, but Windows option is missing
* **Root Cause:** `os-prober` disabled by default in modern GRUB packages.
* **Resolution:**
  1. Boot into Ubuntu.
  2. Open terminal and run:
     ```bash
     sudo os-prober
     sudo update-grub
     ```
  3. Verify Windows Boot Manager is detected and listed in terminal output. Reboot.

### Problem 5.3: KDE display manager drops into login loop (logs in, screen flashes, drops back to SDDM)
* **Root Cause:** Root partition (`/`) ran out of disk space mid-installation during package downloads.
* **Resolution:**
  1. At SDDM login screen, press `Ctrl + Alt + F3` to access TTY text console.
  2. Log in and inspect free space: `df -h /`.
  3. Clean cached packages:
     ```bash
     sudo apt clean
     sudo apt install -f
     ```
