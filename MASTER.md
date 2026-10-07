# Kubuntu Dual-Boot — Master Flow (v2 draft)

Tags: `[OPEN]` undecided. `[CONFIRM]` assumed, needs a yes. `[UNTESTED]` not yet run on real hardware.

## Roles

- **Participant:** does the simple steps and asks for help when stuck.
- **Operator** (about 10 to 20, each owns 3 participants): reads this checklist aloud and takes over steps marked **OP**. Never works with other operators. Anything even slightly wrong: stop and call a **Super**.
- **Super:** escalation point. [OPEN: name two or three]
- **Departments** (specialist teams): take a laptop that hits a roadblock, fix it, and send it back to the stated rejoin point. `[CONFIRM]` my guess at the 7: BitLocker, Wi-Fi/Drivers, Graphics, Storage Controller (AHCI), BIOS/Firmware, Boot (EFI/GRUB), Installer/Partitioning.

Setup: one stock Kubuntu 26.04.1 ISO for everyone, checksummed on every USB. Laptops run in waves. No backup station. Hibernate is out of scope.

---

## 0. Door (verbal + msinfo32 only)

- [ ] Student fills the **declaration form**. It stays with the student all event and is the machine's source of truth.

Verbal, written on the form:
- [ ] Backed up what I can't lose; proceeding at my own risk (signed line)
- [ ] BitLocker / Device Encryption turned off at home
- [ ] BitLocker recovery key saved
- [ ] Has USB-A port, or brought a USB-C to USB-A adapter
- [ ] Laptop charged, charger with them
- [ ] Has this laptop ever had Linux or another OS on it? Yes: flag for the Boot department early.

From msinfo32, copied onto the form:
- [ ] System Type x64. **ARM (Snapdragon) is out of scope.**
- [ ] BIOS Mode UEFI; Secure Boot State
- [ ] Installed RAM (sets swapfile size and `toram`)
- [ ] System Model
- [ ] Free space on C: (Components > Storage > Drives) vs minimum
- [ ] Wi-Fi chip (Components > Network > Adapter); GPU(s) (Components > Display)

Out of scope at the door: ARM, no usable USB port, below minimum free space.
Then direct the student to a seat.

## 1. Windows preparation (seat)

- [ ] Run `manage-bde -status C:` as Administrator. Need **Fully Decrypted, 0.0%**.
- [ ] Not decrypted: **BitLocker department** (waiting area with power, laptop stays awake). Rejoin here.
- [ ] **OP:** for students still encrypted, compare all 48 digits of the recovery key with the machine's own copy (`manage-bde -protectors -get C:`). Every digit must match before anything changes. `[CONFIRM]` skipped for fully decrypted students, since the key unlocks nothing then.
- [ ] **Hard gate:** no BIOS, partition or bootloader change until decrypted.
- [ ] Disable Windows Fast Startup; shut down fully; no pending update or restart
- [ ] Confirm Windows boots normally
- [ ] Shrink C: from inside Windows. Target = 100 GB + RAM + 1 GB `[OPEN: fixed number vs formula; whether the swapfile counts inside the 100 GB]`
- [ ] Confirm the space is unallocated. Cannot shrink: **Installer/Partitioning department.**

## 2. BIOS / UEFI

- [ ] UEFI mode, Secure Boot OFF, USB boot enabled
- [ ] Do **not** change the storage mode unless the internal disk is missing in section 4
- [ ] Supervisor password, locked options, or odd menus: **BIOS/Firmware department.** Rejoin here.
- [ ] Record unusual OEM behavior

## 3. Boot the live USB

- [ ] 6 GB RAM or more: add `toram` on the `linux` line **before the `---`**, wait for the language screen, then remove the USB.
- [ ] 4 GB RAM: no `toram`; USB stays in.
- [ ] Hangs on the logo: **Graphics department** (wait, check USB activity, test `toram` and safe graphics separately). Rejoin here.

## 4. Live hardware check

- [ ] Desktop loads; graphics usable (Nouveau/open drivers are fine)
- [ ] Keyboard and touchpad work
- [ ] **Internal disk visible:** `lsblk` shows `nvme0n1`. Missing: **Storage Controller department.** Rejoin at section 2.
- [ ] **Network:** Wi-Fi connects; Firefox in the live session handles the captive portal. No Wi-Fi: **Wi-Fi/Drivers department** (driver USB, then external NIC). Rejoin here. All fail: come back another day.

## 5. Partition / install

- [ ] Correct disk identified; Windows partitions preserved
- [ ] **Manual partitioning only** inside the unallocated block. Never Alongside or automatic.
- [ ] **OP:** create the new EFI partition: 1 GB, FAT32, name `linux-efi`, mount `/boot/efi`, boot flag set. The Windows EFI is never selected or formatted.
- [ ] **OP:** create root: ext4, mount `/`. No swap partition.
- [ ] **OP review before Install (one operator):** layout matches the Disk Management screenshot; Windows partitions untouched; Windows EFI not mounted or formatted; exactly the two new partitions. Any mismatch: **stop and call a Super.**
- [ ] Installation completes without a bootloader error. Errors: **Installer/Partitioning department.**

## 6. First boot

- [ ] Create the swapfile inside `/`, **size equal to RAM** (mandatory) `[UNTESTED]`. Example for 16 GB: `sudo fallocate -l 16G /swapfile`, `sudo chmod 600 /swapfile`, `sudo mkswap /swapfile`, `sudo swapon /swapfile`, then `echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab`
- [ ] GRUB menu appears by default and lists Windows
- [ ] Windows missing: **Boot department** (enable os-prober). Boots straight to Windows or lands at `grub>`: **Boot department.**
- [ ] Power-cycle test: GRUB by default (not only via the firmware boot menu)
- [ ] Windows boots with no BitLocker prompt. Prompt appears: **BitLocker department** (stop, enter key, confirm Windows boots clean).

## 7. Minimum handoff validation

- [ ] Kubuntu boots and reboots; graphics normal; network works
- [ ] Windows still boots
- [ ] Non-blocking quirks recorded (sleep, Bluetooth, fingerprint, webcam, audio, brightness keys)

## 8. Handoff

- [ ] Student told what was installed and any known limitations
- [ ] Unresolved issues and new roadblocks recorded
- [ ] Student told not to re-enable BitLocker `[CONFIRM]`
- [ ] Gamers (for example Valorant or other kernel anticheat): re-enable Secure Boot in the BIOS and confirm Kubuntu still boots. Drivers that need key enrollment (such as a Broadcom Wi-Fi driver) may block this. `[UNTESTED]`
- [ ] Collect the participant's **problem slip** (see PROBLEM-SLIP.md): what went wrong, what was done, anything they need to do later.

## 9. Policy

- Backup is the student's job. Volunteers give instructions only.
- Charging points provided; student-caused power loss is not an install failure.
- USB or port failure: try reasonable alternatives, then escalate or park.
- Graphics scope: usable open-source graphics is enough.
- Unknown problem: stop, call a Super, no destructive improvising.
- Timeouts: `[OPEN]` minutes before a laptop is parked, and who decides.

## Open decisions

- `[OPEN]` Shrink number on the preflight; swapfile inside or on top of 100 GB
- `[OPEN]` Exception for 64 GB+ RAM laptops
- `[OPEN]` Final list of the 7 departments; Super names
- `[OPEN]` Timeouts; install time per laptop (STATS after the dry run)
- `[OPEN]` No AHCI option in BIOS: out of scope? `[CONFIRM]`
- `[OPEN]` Secure Boot re-enable for gamers: test one dry-run laptop

## Dry-run findings

- `toram` hung on the logo on an MSI safe-graphics entry.
- Reusing an old Debian EFI left a stale boot entry and dropped to `grub>`.
