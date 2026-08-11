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
* **RST/RAID → AHCI warning:** If Windows was installed in RST/RAID mode, this switch can BSOD it with `INACCESSIBLE_BOOT_DEVICE` (0x7B). Prepare Windows first so it handshakes with the AHCI driver:
  1. In Windows (admin terminal): `bcdedit /set {current} safeboot minimal`.
  2. Reboot into BIOS, switch to **AHCI**, and let Windows boot into **Safe Mode** — it installs the AHCI driver there.
  3. Back in Windows (admin terminal): `bcdedit /deletevalue {current} safeboot`, then reboot normally.
  4. Only proceed with partitioning once Windows boots cleanly in AHCI mode.

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
* **Root Cause:** System has insufficient physical RAM (<6 GB), causing an Out-Of-Memory (OOM) panic while copying the ISO image to tmpfs.
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
* **Note:** Hibernation stays **off** by design — Windows Fast Startup depends on it and must remain off for clean dual-boot and for GRUB's `os-prober` to detect Windows (see Problem 5.2).

### Problem 3.2: Accidental modification / deletion of existing EFI partition
* **Root Cause:** Operator formatted the existing FAT32 boot partition during custom layout.
* **Resolution (Emergency EFI Repair):**
  1. Complete Linux installation.
  2. Boot into Linux, open terminal, and reinstall GRUB to EFI. UEFI requires explicit `--target` and `--efi-directory` (the bare `grub-install /dev/nvme0n1` form only works for legacy BIOS):
     ```bash
     sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu /dev/nvme0n1 # adjust to actual disk ID
     sudo update-grub
     ```
  3. To restore Windows bootloader, prepare a Windows Recovery USB, boot into command prompt, and run:
     ```cmd
     bootrec /fixmbr
     bootrec /fixboot
     bcdboot C:\Windows
     ```

### Problem 3.3: Installer won't use the existing Windows EFI partition for `/boot/efi`
* **Root Cause:** Subiquity only lets you mount an existing ESP when its disk is selected as a boot device, and the Format checkbox can default to on.
* **Resolution:**
  1. In Custom Storage Layout, select the small FAT32 partition flagged **EFI System**.
  2. Confirm its mount point is `/boot/efi` and **Format is UNCHECKED** — one ESP safely holds both Windows and Ubuntu boot files.
  3. If the mount option is greyed out, the target disk is not selected as a boot device in the storage screen; enable it, and the installer reuses the existing ESP.
  4. Do not let the installer create a second ESP — a new ~538 MiB ESP is auto-created only on disks with none, and a second ESP on another disk orphans GRUB from the firmware boot order.

### Problem 3.4: Installer warns "no swap space has been specified"
* **Root Cause:** The custom layout only created `/`, so no swap exists; Ubuntu's guided-install swapfile is not created for custom layouts.
* **Resolution:**
  1. This is a warning, not an error — continue the install.
  2. After first boot, add a swapfile (recommended on <6 GB RAM laptops):
     ```bash
     sudo fallocate -l 4G /swapfile
     sudo chmod 600 /swapfile
     sudo mkswap /swapfile
     sudo swapon /swapfile
     echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
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
* **Root Cause:** DNS resolution failure — the portal's DNS servers aren't resolving, or a stale server is cached.
* **Resolution:**
  1. Test direct IP connectivity:
     ```bash
     ping -c 3 8.8.8.8
     ```
  2. If IP ping works but domain ping fails, override DNS for the active link. Do **not** overwrite `/etc/resolv.conf` — it is a symlink managed by `systemd-resolved`/NetworkManager and gets clobbered on reconnect:
     ```bash
     nmcli -t dev status          # find the Wi-Fi interface name (e.g. wlp2s0, wlan0)
     sudo resolvectl dns <interface> 8.8.8.8
     ```
  3. Verify with `ping -c 3 google.com`.

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
* **Root Cause:** `os-prober` is disabled by default in modern GRUB packages (`GRUB_DISABLE_OS_PROBER` defaults to true).
* **Resolution:**
  1. Boot into Ubuntu.
  2. Open terminal, enable os-prober, and regenerate the menu:
     ```bash
     echo 'GRUB_DISABLE_OS_PROBER=false' | sudo tee -a /etc/default/grub
     sudo update-grub
     ```
  3. Verify Windows Boot Manager is detected and listed in terminal output. Reboot.
* **Note:** os-prober also skips a hibernated/dirty NTFS volume. If Windows Fast Startup is on, disable it in Windows (`Control Panel > Power Options > Choose what the power buttons do` → untick **Turn on fast startup**), reboot Windows once, then repeat step 2.

### Problem 5.3: KDE display manager drops into login loop (logs in, screen flashes, drops back to SDDM)
* **Root Cause:** Usually the minimal desktop install (`--no-install-recommends`) is missing the session component Plasma needs to start (e.g. `plasma-workspace-wayland`) — not a full root partition. A 50 GB `/` almost never fills from a KDE install.
* **Resolution:**
  1. At the SDDM login screen, press `Ctrl + Alt + F3` to access a TTY text console.
  2. Log in and inspect free space: `df -h /`.
  3. If space is tight, clean cached packages:
     ```bash
     sudo apt clean
     sudo apt install -f
     ```
  4. Otherwise, install the missing session component and restart the login manager:
     ```bash
     sudo apt install -y plasma-workspace-wayland
     sudo systemctl restart sddm
     ```
  5. At SDDM, try the **Wayland** session if the X11 session keeps looping.
