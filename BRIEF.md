# LINUX INSTALL FEST — OPERATIONAL BRIEF

## SECTION 1: ATTENDEE INSTRUCTIONS (Before Event)

### Mandatory Pre-Requisites
1. **Back Up Everything:** You are solely responsible for your data. Back up all important files to an external drive or cloud storage before arriving.
2. **Disable Device Encryption / BitLocker (Crucial):**
   * **Windows Home:** Device Encryption lives under `Settings > Privacy & Security > Device Encryption`. Turn it **Off**.
   * **Windows Pro:** BitLocker Drive Encryption lives under `Control Panel > BitLocker Drive Encryption`. Save the recovery key first (from there or `https://aka.ms/myrecoverykey`), then select **Turn Off**.
   * Either way, wait for decryption to reach **100%** before arriving.
3. **Shrink Windows C: Drive:**
   * Press `Win + X` and select **Disk Management**.
   * Right-click your `C:` drive and select **Shrink Volume**.
   * Shrink by at least **50,000 MB (50 GB)**.
   * **Leave the newly created space as raw "Unallocated" space. Do not format or assign a drive letter to it.**
4. **BIOS Preparation:**
   * Note down your laptop model-specific BIOS boot keys (`F2`, `F10`, `F12`, `Del`).

---

## SECTION 2: STATION OPERATOR GUIDE

### Role Overview
Station Operators drive the physical installation pipeline, manage USB handoffs, execute storage partitioning under supervision, and trigger the post-install scripts.

### Execution Standard Operating Procedure (SOP)

#### Step 1: BIOS & Media Injection
1. Spam the BIOS key on power-up (`F2`, `F10`, `F12`, `Del`).
2. Set SATA mode to **AHCI** (Disable Intel VMD/RST).
3. Set **Secure Boot = Disabled** and **Fast Boot = Disabled**.
4. Boot the Install USB. At the GRUB menu, highlight `Try or Install Ubuntu Server` and press `e`.
5. **RAM Allocation Rule:** Check system RAM.
   * **≥ 6 GB RAM:** Append `toram` to the end of the `linux` line. Press `F10`. As soon as the installer language screen appears, **yank the USB** and pass it to the next queue operator.
   * **< 6 GB RAM:** Do **NOT** add `toram`. Leave the USB plugged in throughout the entire base installation.

#### Step 2: Custom Storage Setup
1. Select **Custom Storage Layout**.
2. Find the ~50 GB **Unallocated Space**.
3. **STOP:** Call another Team Leader for Point-and-Call verification before touching any partition tables.
4. Assign Root (`/`) formatted as `ext4` on the unallocated space. The installer will warn about missing swap space — that is expected; add a swapfile after boot if needed (see RUNBOOK Problem 3.4).
5. Select the existing FAT32 EFI partition (~100–512 MB) and set mount point to `/boot/efi`. **Do NOT format it.**
6. Complete installer profile setup and trigger base installation.

#### Step 3: Network & Post-Install Setup
1. Post-reboot, log into the text terminal (TTY).
2. Connect to Wi-Fi via `nmcli`:
   ```bash
   nmcli dev wifi connect "NETWORK_NAME" password "PASSWORD"
   ```
3. Run the captive portal bypass script:
   ```bash
   git clone https://github.com/achar-pranav/captive-bypass.git
   cd captive-bypass && chmod +x captive-bypass && ./captive-bypass
   ```
4. Switch to the local India mirror to accelerate downloads:
   ```bash
   sudo sed -i 's|http://archive.ubuntu.com/ubuntu|http://in.archive.ubuntu.com/ubuntu|g' /etc/apt/sources.list.d/ubuntu.sources
   sudo apt update
   ```
5. Install base desktop environment:
   ```bash
   sudo apt install -y --no-install-recommends kde-plasma-desktop sddm network-manager
   sudo systemctl enable sddm
   sudo systemctl set-default graphical.target
   sudo reboot
   ```
6. Log into the graphical desktop. Open a terminal and run the interactive post-install script:
   ```bash
   ./post-install.sh
   ```
7. Enable persistent Wi-Fi captive bypass:
   ```bash
   cd captive-bypass && ./captive-bypass --install
   ```

---

## SECTION 3: TEAM LEADER MANUAL

### Core Responsibilities
- Supervise safety protocols and hold ultimate veto power over drive modifications.
- Perform mandatory double-verification (Point-and-Call) on all storage partitioning.
- Triage hardware edge cases to the Rescue Station.

### Mandatory Verification Protocols

#### Point-and-Call Partition Sign-Off
No operator or student may press "Done" or "Write Changes to Disk" without two Team Leaders executing this verbal check:

**TL 1:** Points to targeted space → "Unallocated block selected, [X] GB."
**TL 2:** Verifies drive layout → "Verified unallocated space. Windows NTFS partition untouched."
**TL 1:** Points to EFI partition → "Existing FAT32 EFI partition targeted at /boot/efi. Format checkbox UNCHECKED."
**TL 2:** Inspects configuration → "Verified. Safe to commit."

### Operational Control Rules

#### BitLocker Guard
If BitLocker status is "Encryption Paused" or "Encrypting/Decrypting", halt operation immediately. Do not touch partitions until decryption reaches 100%.

#### Rescue Station Offloading
Send laptops to the Rescue Station if:
- Disk Management cannot shrink C: drive due to unmovable system files.
- Wi-Fi interface is missing (`nmcli dev` shows no wlan device).
- Machine fails to boot past BIOS after 2 attempts.
---

