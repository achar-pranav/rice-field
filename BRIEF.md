# LINUX INSTALL FEST — OPERATIONAL BRIEF

## SECTION 1: ATTENDEE INSTRUCTIONS (Send Before Event)

### Mandatory Pre-Requisites
1. **Back Up Everything:** You are solely responsible for your data. Back up all important files to an external drive or cloud storage before arriving.
2. **Disable BitLocker (Crucial):**
   * Save your BitLocker recovery key from `Settings > Privacy & Security > Device Encryption` or `https://aka.ms/myrecoverykey`.
   * Turn BitLocker completely **OFF**: Go to `Control Panel > BitLocker Drive Encryption` and select **Turn Off**. Wait for decryption to reach 100%.
3. **Shrink Windows C: Drive:**
   * Press `Win + X` and select **Disk Management**.
   * Right-click your `C:` drive and select **Shrink Volume**.
   * Shrink by at least **50,000 MB (50 GB)**.
   * **Leave the newly created space as raw "Unallocated" space. Do not format or assign a drive letter to it.**
4. **BIOS Preparation:**
   * Take a photo of your current BIOS boot order screen on your phone.
   * Note down your laptop model and BIOS boot keys (`F2`, `F10`, `F12`, `Del`).

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
   * **$\ge$ 6 GB RAM:** Append `toram` to the end of the `linux` line. Press `F10`. As soon as the installer language screen appears, **yank the USB** and pass it to the next queue operator.
   * **< 6 GB RAM:** Do **NOT** add `toram`. Leave the USB plugged in throughout the entire base installation.

#### Step 2: Custom Storage Setup
1. Select **Custom Storage Layout**.
2. Find the ~50 GB **Unallocated Space**.
3. **STOP:** Call a Team Leader for Point-and-Call verification before touching any partition tables.
4. Assign Root (`/`) formatted as `ext4` on the unallocated space.
5. Select the existing FAT32 EFI partition (~100–512 MB) and set mount point to `/boot/efi`. **Do NOT format it.**
6. Complete installer profile setup and trigger base installation.

#### Step 3: Network & Post-Install Setup
1. Post-reboot, log into the text terminal (TTY).
2. Connect to Wi-Fi via `nmcli`:
   ```bash
   nmcli dev wifi connect "NETWORK_NAME" password "PASSWORD"
Run the captive portal bypass script:Bashgit clone [https://github.com/achar-pranav/captive-bypass.git](https://github.com/achar-pranav/captive-bypass.git)
cd captive-bypass && chmod +x captive-bypass.sh && ./captive-bypass.sh
Switch to the local India mirror to accelerate downloads:Bashsudo sed -i 's/[archive.ubuntu.com/in.archive.ubuntu.com/g](https://archive.ubuntu.com/in.archive.ubuntu.com/g)' /etc/apt/sources.list.d/ubuntu.sources
sudo apt update
Install base desktop environment:Bashsudo apt install -y --no-install-recommends kde-plasma-desktop sddm network-manager
sudo systemctl enable sddm
sudo systemctl set-default graphical.target
sudo reboot
Log into graphical desktop. Open terminal and run the interactive post-install script:Bash./post-install.sh
Enable persistent Wi-Fi captive bypass:Bashcd captive-bypass && ./captive-bypass.sh --install
SECTION 3: TEAM LEADER MANUALCore ResponsibilitiesSupervise safety protocols and hold ultimate veto power over drive modifications.Perform mandatory double-verification (Point-and-Call) on all storage partitioning.Triage hardware edge cases to the Rescue Station.Mandatory Verification ProtocolsPoint-and-Call Partition Sign-OffNo operator or student may press "Done" or "Write Changes to Disk" without two Team Leaders executing this verbal check:TL 1: Points to targeted space $\rightarrow$ "Unallocated block selected, [X] GB."TL 2: Verifies drive layout $\rightarrow$ "Verified unallocated space. Windows NTFS partition untouched."TL 1: Points to EFI partition $\rightarrow$ "Existing FAT32 EFI partition targeted at /boot/efi. Format checkbox UNCHECKED."TL 2: Inspects configuration $\rightarrow$ "Verified. Safe to commit."Operational Control RulesBitLocker Guard: If BitLocker status is "Encryption Paused" or "Encrypting/Decrypting", halt operation immediately. Do not touch partitions until decryption reaches 100%.Rescue Station Offloading: Send laptops to the Rescue Station if:Disk Management cannot shrink C: drive due to unmovable system files.Wi-Fi interface is missing (nmcli dev shows no wlan device).Machine fails to boot past BIOS after 2 attempts.
---

# FLOWCHART.md

```markdown
# LINUX INSTALL FEST — MASTER FLOWCHART

===================================================================================
                       STAGE 0: ATTENDEE PRE-CHECK
===================================================================================
                                     │
                                     ▼
                    [ Has attendee backed up data? ]
                               /          \
                             YES           NO ──► [ STOP: Force Backup / Reject ]
                              │
                              ▼
               [ BitLocker Status Check in Windows ]
                               /          \
                          DISABLED       ENABLED ──► [ Control Panel > Turn Off ]
                              │                           Wait for 100% Decryption
                              ▼
            [ Shrink C: Drive in diskmgmt.msc (50GB+) ]
                              │
                              ▼
           [ Is there ~50GB raw "Unallocated Space"? ]
                               /          \
                             YES           NO ──► [ Offload to Rescue Station ]
                              │
                              ▼
===================================================================================
                       STAGE 1: BIOS & BOOT SELECTION
===================================================================================
                              │
                              ▼
           [ Enter BIOS: Disable Secure Boot & Fast Boot ]
           [ Change SATA Mode from VMD/RST to AHCI       ]
                              │
                              ▼
                    [ Check Hardware RAM ]
                               /          \
                        >= 6 GB RAM      < 6 GB RAM
                             /              \
         [ Boot USB w/ `toram` flag ]     [ Boot USB standard mode ]
                     │                               │
       [ Language screen loads ]                     │
                     │                               │
         [ YANK USB & PASS ON ]                      │
                     │                               │
                     └───────────────┬───────────────┘
                                     │
                                     ▼
===================================================================================
                   STAGE 2: DUAL-TL CUSTOM PARTITIONING
===================================================================================
                              │
                              ▼
               [ Select "Custom Storage Layout" ]
                              │
                              ▼
           [ CALL 2 TEAM LEADERS FOR POINT-AND-CALL ]
                              │
       ┌──────────────────────┴──────────────────────┐
       │ TL Verification Checklist:                  │
       │ 1. Mount unallocated space to `/` (ext4)    │
       │ 2. Set existing EFI partition to `/boot/efi`│
       │ 3. Confirm DO NOT FORMAT on EFI partition   │
       └──────────────────────┬──────────────────────┘
                              │
                              ▼
                 [ Commit & Run Installation ]
                              │
                              ▼
===================================================================================
                 STAGE 3: NETWORK & CAPTIVE PORTAL BYPASS
===================================================================================
                              │
                              ▼
               [ Reboot into Server TTY Console ]
                              │
                              ▼
              [ Connect Wi-Fi via `nmcli dev wifi` ]
                              │
                              ▼
          [ Execute `./captive-bypass.sh` with credentials ]
                              │
                              ▼
                [ Test Connection: `ping google.com` ]
                               /          \
                            PASS          FAIL ──► [ Refer to RUNBOOK.md ]
                              │
                              ▼
===================================================================================
                  STAGE 4: DESKTOP BUILD & REBOOT
===================================================================================
                              │
                              ▼
       [ Set India mirror: `/etc/apt/sources.list.d/ubuntu.sources` ]
                              │
                              ▼
         [ Install minimal desktop: `kde-plasma-desktop` + `sddm` ]
                              │
                              ▼
              [ Enable SDDM & Reboot into Desktop GUI ]
                              │
                              ▼
===================================================================================
             STAGE 5: POST-INSTALL SCRIPT & PERSISTENCE
===================================================================================
                              │
                              ▼
          [ Launch Interactive Post-Install Setup Script ]
                              │
                              ▼
       [ Execute `./captive-bypass.sh --install` in GUI terminal ]
                              │
                              ▼
===================================================================================
                         STAGE 6: FINAL QA & HANDOFF
===================================================================================
                              │
                              ▼
           [ Verify Dual-Boot: GRUB shows Ubuntu & Windows ]
           [ Verify GUI Wi-Fi Auto-Reconnect & Internet    ]
                              │
                              ▼
                     [ SYSTEM COMPLETE ]
