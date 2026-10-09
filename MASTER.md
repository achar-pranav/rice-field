# Kubuntu Dual-Boot — Master Flow (v2 draft)

Tags: `[OPEN]` undecided. `[CONFIRM]` assumed, needs a yes. `[UNTESTED]` not yet run on real hardware.

## Roles

- **Participant:** does the simple steps and asks for help when stuck.
- **Operator** (about 10 to 20, each owns 3 participants): reads this checklist aloud and takes over steps marked **OP**. Never works with other operators. Anything even slightly wrong: stop and call a **Super**.
- **Super:** escalation point. [OPEN: name two or three]
- **Departments** (specialist teams): take a laptop that hits a roadblock, fix it, and send it back to the stated rejoin point. `[CONFIRM]` BitLocker, Wi-Fi/Drivers, Graphics, Storage Controller (AHCI/RAID), BIOS/Firmware, Boot (EFI/GRUB), Installer/Partitioning.

Setup: one stock Kubuntu 26.04.1 ISO for everyone, checksummed on every USB. Installs happen asychronously per operator's team. No backup station. Hibernate is out of scope. Swapfile as defaults not swap partition.  

---

## 0. Door (verbal + msinfo32 only)

- [ ] Student fills the **declaration form**. It stays with the student all event and is the machine's source of truth.

Verbal, written on the form:
- [ ] System Type x64. **ARM (Snapdragon/apple) is out of scope.**
- [ ] Backed up what I can't lose; proceeding at my own risk (signed line)
- [ ] No pending windows updates. Install updates. THEN restart.
- [ ] BitLocker / Device Encryption turned off at home
- [ ] BitLocker recovery key saved
- [ ] Laptop charged, charger with them
- [ ] Has USB-A port, or brought a USB-C to USB-A adapter
- [ ] Has this laptop ever had Linux or another OS on it? Yes: flag for the Boot department early.

From msinfo32, copied onto the form:
- [ ] BIOS Mode UEFI; Secure Boot State
- [ ] Installed RAM (affects `toram`)
- [ ] System Model : for drivers
- [ ] unallocated/Free space on C: (Components > Storage > Drives) vs minimum (100 GB)
- [ ] Wi-Fi chip (Components > Network > Adapter;
- [ ] GPU(s) (Components > Display)

Out of scope at the door: ARM/Silicon Macbooks, no usable USB port, below minimum free space, hasnt backed up data.
Then direct the student to a seat.

## 1. Windows preparation (seat)

- [ ] Run `manage-bde -status C:` in `cmd` as Administrator. Need **Fully Decrypted, 0.0%**.
- [ ] Not decrypted: **BitLocker department** (waiting area with power, laptop stays awake). Rejoin here.
- [ ] **OP:** for students still encrypted, compare all 48 digits of the recovery key with the machine's own copy (`manage-bde -protectors -get C:`). Every digit must match before anything changes. **OP** must also check for unecrypted devices in case a windows update re-triggers encryption. If a bitlocker key can be obtained it must.
- [ ] **Hard gate:** no BIOS, bootloader, major changes until decrypted and key in hand.
- [ ] Disable Windows Fast Startup; shut down fully; no pending update or restart
- [ ] Confirm Windows boots normally
- [ ] Shrink C: from inside Windows. Target = 100 GB + RAM + 1 GB `[OPEN: fixed number vs formula; whether the swapfile counts inside the 100 GB]`
- [ ] Confirm the space is unallocated. Cannot shrink: **Storage Department escalation.**

## 2. BIOS / UEFI

- [ ] Confirm UEFI mode
- [ ] Secure Boot OFF
- [ ] USB boot enabled and set to highest priority
- [ ] Verify Storage Mode as AHCI unless the internal disk is missing in section 4
- [ ] Supervisor password, locked options, or odd menus: **BIOS/Firmware department.** Rejoin here.
- [ ] Record unusual OEM behavior

## 3. Boot the live USB

- [ ] 8 GB RAM or more: add `toram` on the `linux` line **before the `---`**, wait for the language screen, then remove the USB.
- [ ] <8 GB RAM: no `toram`; USB stays in. USB is given at end when toram install are complete. Ideally bring your own usb.
- [ ] Hangs on the logo: **Graphics department** (wait, check USB activity, test `toram` and safe graphics separately). Rejoin here.

## 4. Live hardware check

- [ ] Desktop loads; graphics usable (Nouveau/open drivers are fine)
- [ ] Keyboard and touchpad work
- [ ] **Internal disk visible:** `lsblk` shows `nvme0n1`. Missing: **Storage Controller department.** Rejoin at section 2.
- [ ] **Network:** Wi-Fi connects; Firefox in the live session handles the captive portal. No Wi-Fi: **Wi-Fi/Drivers department** (driver USB, then external NIC). Rejoin here. Any checks in this section fail: come back another day.

## 5. Partition / install
PARTITONING TO BE DONE BY OP ONLY
- [ ] Correct disk identified; Windows partitions preserved
- [ ] **Manual partitioning only** inside the unallocated block. Never Alongside or automatic.
- [ ] **OP:** create the new EFI partition: 1 GB, FAT32, name `linux-efi`, mount `/boot/efi`, boot flag set. The Windows EFI is never selected or formatted.
- [ ] **OP:** create root: ext4, mount `/`. No swap partition.
- [ ] **OP review before Install (one operator):** layout matches the Disk Management screenshot; Windows partitions untouched; Windows EFI not mounted or formatted; exactly the two new partitions. Any mismatch: **stop and call a Super.**
- [ ] Installation completes without a bootloader error. Errors: **BIOS department**

## 6. First boot

- [ ] check GRUB menu appears by default and lists Windows
- [ ] Windows missing: **Boot department** (enable os-prober).If Boots straight to Windows or lands at `grub>`: **Boot department.**
- [ ] Power-cycle test: GRUB by default (not only via the firmware boot menu)
- [ ] Windows boots with no BitLocker prompt. Prompt appears: **BitLocker department** (stop, enter key, confirm Windows boots clean).

## 7. Minimum handoff validation

- [ ] Kubuntu boots and reboots; graphics normal; network works
- [ ] Windows still boots
- [ ] Non-blocking quirks recorded (sleep, Bluetooth, fingerprint, webcam, audio, brightness keys) might require subtle work. outside of scope.

## 8. Handoff

- [ ] Unresolved issues and new roadblocks recorded
- [ ] Student told NOT to re-enable BitLocker `[CONFIRM]`
- [ ] Gamers (for example Valorant or other kernel anticheat): This is out of scope. You will need a guide to re-enable secureboot without issues. [TODO: find and test a good guide]
- [ ] Collect the participant's **problem log**. what went wrong, what was done, anything they need to do later.

## 9. Policy

- Backup is the student's job. Volunteers give instructions only.
- Charging points provided; student-caused power loss is not an install failure.
- USB or port failure: try reasonable alternatives, then escalate or park.
- Graphics scope: usable open-source graphics is enough.
- Unknown problem: stop, call a Super, no destructive improvising.

## Open decisions

- `[OPEN]` No AHCI option in BIOS: out of scope? `[CONFIRM]`
- `[OPEN]` Secure Boot re-enable for gamers: test one dry-run laptop

## Dry-run findings

