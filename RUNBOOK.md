# Troubleshooting Runbook — by Department (v2 draft)

Use this only for non-standard cases the master flow does not cover. Standard steps (including the swapfile) live in MASTER.md. Each entry: **Symptom**, **Check**, **Fix**, **Escalate**.
Every fix is `[UNTESTED]` until it has been run on real hardware in the dry run.
Anything not listed here: stop and call a Super. Do not improvise destructive changes.
Timeouts for parking a laptop: `[OPEN]`.

---

## BitLocker

(B1 is the most common case. If it keeps recurring it moves into MASTER.)

Rule: Windows must stay bootable. Nothing in BIOS, partitions or bootloader changes until Fully Decrypted.

### B1. Encryption still on (BitLocker or Device Encryption)
- **Check:** `manage-bde -status C:` as Administrator. Need Fully Decrypted and 0.0%.
- **Fix:** Windows Home: Settings > Privacy & security > Device encryption > Off. Windows Pro: Control Panel > BitLocker Drive Encryption > Turn off. Laptop stays on, plugged in and awake until 100%. Waiting area has power outlets.
- **Escalate:** Cannot turn off: B3.

### B2. Key verification (OP)
- **Check:** While still encrypted, run `manage-bde -protectors -get C:`. Compare all 48 digits with the student's saved copy.
- **Fix:** Every digit must match before anything changes. `[CONFIRM]` skipped for fully decrypted students.
- **Escalate:** Mismatch or no copy: B4.

### B3. Cannot disable, or it turns itself back on
- **Check:** `manage-bde -status C:` again at the seat.
- **Fix:** Turn off again and re-check. Optional prevention: registry value `PreventDeviceEncryption` = 1 (DWORD) under `HKLM\SYSTEM\CurrentControlSet\Control\BitLocker`. `[OPEN: use it or not]`
- **Escalate:** Still on after a retry: come back another day.

### B4. Recovery key missing or wrong
- **Check:** Student's Microsoft account recovery key page (`aka.ms/myrecoverykey`); match the key ID.
- **Fix:** Retrieve the key. No BIOS, partition or bootloader change until it matches.
- **Escalate:** Cannot retrieve: come back another day.

### B5. Recovery prompt appears (after BIOS change, boot change or install)
- **Fix:** Stop touching anything. Enter the full 48-digit key. Boot Windows. Run `manage-bde -status C:`. Decrypt again if needed. Then continue.
- **Escalate:** Key rejected or Windows will not boot: Super.

---

## Storage Controller

### S1. Internal disk missing in the live session
- **Check:** `lsblk` shows no `nvme0n1`. Confirm: `lspci -nn | grep -iE 'raid|volume management|non-volatile'`.
- **Fix:** "Volume Management Device" or "RAID bus controller" means RST/VMD/RAID mode. In Windows: `msconfig` > Boot > tick Safe boot: Minimal, restart into BIOS, set storage mode to AHCI, save. Windows boots into Safe Mode and installs the driver. In Safe Mode untick Safe boot in `msconfig`, restart normally, and confirm Windows boots. Then retry the USB.
- **Escalate:** No AHCI option in BIOS: out of scope `[CONFIRM]`.

### S2. Windows blue-screens after the mode change (0x7B)
- **Fix:** Set BIOS back to the original mode. Windows boots again. Redo S1 in the correct order (Safe boot flag first).
- **Escalate:** Windows still will not boot: Super and the green Windows recovery USB.

### S3. Several internal drives
- **Check:** `lsblk -f`. Identify the disk holding the NTFS Windows partition by size and model.
- **Escalate:** Not 100% sure which disk: Super.

### S4. Dynamic disk or Storage Spaces
- **Fix:** Out of scope `[CONFIRM]`.

---

## BIOS / Firmware

### F1. Secure Boot cannot be turned off, or supervisor password
- **Check:** Look under Security/Boot menus (names vary by OEM). Some need a password set first.
- **Fix:** Student supplies the password if one exists.
- **Escalate:** Locked with no password: out of scope.

### F2. USB missing from the boot menu
- **Fix:** Fast Boot off, USB boot on, UEFI mode. Try another port or adapter, then another USB stick.
- **Escalate:** Fails after two attempts: park the laptop.

### F3. BIOS settings do not persist
- **Fix:** Retry once and check the clock/battery warning.
- **Escalate:** Still reverts: out of scope.

### F4. Windows installed in Legacy mode
- **Check:** msinfo32 BIOS Mode says Legacy.
- **Escalate:** Out of scope `[CONFIRM]`.

---

## Wi-Fi / Drivers

### W1. No Wi-Fi in the live session
- **Check:** `lspci -nnk | grep -iA3 net` shows the chip and which driver claims it. Try Wi-Fi off/on and airplane-mode key first.
- **Fix:** Driver USB (red), then an external NIC.

### W2. Driver USB (red)
- **Fix:** Plug in, open the stick in the file manager, copy the matching driver folder to the home directory, install from Konsole. The driver team's own sheet gives the exact files and commands. `[UNTESTED]` `[OPEN: write the team sheet]`
- **Escalate:** Stick unreadable: try another red USB.

### W3. External NIC
- **Fix:** Plug in a known-good USB Ethernet or USB Wi-Fi adapter. NetworkManager should pick it up.
- **Escalate:** Not recognized: next fallback.

### W4. Captive portal
- **Fix:** Open Firefox in the live session and log in through the portal page.
- **Escalate:** Portal never appears or rejects the device (MAC-bound?): `[OPEN]`, ask a Super.

### W5. Wi-Fi works live but not after install
- **Fix:** Repeat W2 or W3 on the installed system.

### W6. All fallbacks fail
- **Fix:** Student comes back another day (likely a broken NIC). Out of scope.

---

## Graphics

### G1. Black screen on live boot
- **Fix:** Pick the Safe Graphics entry. If needed, press `e` at GRUB and add `nomodeset` (before `---`).

### G2. Frozen logo with `toram`
- **Check:** Wait and watch the USB activity light; slow copies can take 10+ minutes.
- **Fix:** Press `e`, remove `quiet splash` to see messages. Test `toram` alone and Safe Graphics alone. Confirm `toram` is before `---`.
- **Escalate:** Out-of-memory or copy errors: boot without `toram` (and with the USB staying in).

### G3. NVIDIA / hybrid / AMD issues
- **Fix:** Nouveau/open graphics is enough. Proprietary drivers are out of scope.

### G4. Graphics fail after install
- **Fix:** At GRUB press `e` and add `nomodeset` to boot once. `[OPEN: offline permanent fix]`

### G5. External monitor fails
- **Fix:** Non-blocking; record it.

---

## Boot (EFI / GRUB)

### E1. Boots straight to Windows
- **Check:** `efibootmgr` with no arguments.
- **Fix:** Move Ubuntu first with `sudo efibootmgr -o <ubuntu entry>,<windows entry>`. If it does not stick (Acer is the expected one), set the order in the BIOS directly.

### E2. Windows missing from GRUB
- **Fix:** Add `GRUB_DISABLE_OS_PROBER=false` to `/etc/default/grub`, then `sudo update-grub`. If still missing, turn Windows Fast Startup off, boot Windows once, retry.

### E3. `grub>` prompt after reboot
- **Cause:** Old EFI entry (for example a leftover Debian one) outranks the new Kubuntu entry and has no menu.
- **Fix:** Type `exit`, or use the firmware boot menu and pick Kubuntu or Windows. In Kubuntu: `sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu`, `sudo update-grub`, delete the stale entry with `sudo efibootmgr -b <entry> -B`, then fix the boot order (E1).
- **Escalate:** Cannot find the Kubuntu root: Super.

### E4. Linux only boots through the firmware menu
- **Fix:** Repeat E1 and the BIOS boot order. Record the OEM.

### E5. EFI partition formatted by mistake
- **Escalate:** Immediately to a Super. Windows may need the green recovery USB (`bootrec` and `bcdboot C:\Windows`). Do not improvise.

### E6. EFI full or NVRAM full
- **Check:** `df -h /boot/efi`, `efibootmgr`.
- **Fix:** Delete stale entries with `sudo efibootmgr -b <entry> -B`.

### E7. GRUB gone after a later Windows update
- **Fix:** Tell students at handoff. Repeat E1.

---

## Installer / Partitioning

### P1. Windows will not shrink enough
- **Check:** Disk Management shows far less shrinkable space than free space.
- **Fix:** Admin Command Prompt: `powercfg /h off`. Temporarily disable the pagefile, reboot, shrink, then re-enable it. Fast Startup stays off.
- **Escalate:** Still stuck: park the laptop.

### P2. Filesystem errors or a failing drive
- **Fix:** Run `chkdsk C: /f` from Windows.
- **Escalate:** Bad sectors or SMART warnings: out of scope.

### P3. Layout does not match Disk Management
- **Escalate:** Stop and call a Super. Never press Install.

### P4. Installer will not launch or crashes
- **Fix:** Retry once, reboot the live session, try another USB.

### P5. Partitioning errors
- **Fix:** Retry the step once.
- **Escalate:** Repeats: Super.

### P6. Installation fails or GRUB fails to install
- **Fix:** Note the exact error. Do not reboot.
- **Escalate:** Super.

### P7. USB removed too early or corrupted ISO
- **Fix:** Reboot and redo. Verify the USB checksum, or swap the stick.

---

## Unknown

- Error not listed here, an untested command, or anything destructive: stop and call a Super.
