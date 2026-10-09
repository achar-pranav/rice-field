# Kubuntu Dual-Boot — Master Flow (v3)

Tags: `[OPEN]` undecided. `[CONFIRM]` assumed. `[UNTESTED]` not run on hardware.

---

## Roles

- **Participant:** the student. Does the steps, raises a hand when stuck.
- **Operator:** reads this aloud, takes over steps marked **OP**. Owns 3 participants. Anything slightly wrong: raise hand for a Super.
- **Super:** escalation. Walks the floor. Signs off on partitioning.
- **Departments:** specialist tables. BitLocker, Wi-Fi/Drivers, Graphics, Storage Controller, BIOS/Firmware, Boot (EFI/GRUB), Installer/Partitioning. Staffed from one pool of higher-skilled volunteers.

Setup: one Kubuntu 26.04.1 ISO, checksummed per USB. No backup station. Hibernate out of scope. Swapfile if installer creates it; if not, out of scope.

**Rule for everyone: if a step does not match what you see, STOP. Raise your hand. Do not guess.**

---

## 0. Door

Participant fills the **declaration form**. It stays with them all event.

### 0.1 Verbal yes/no (operator asks)

- [ ] System Type is x64? (msinfo32 → System Type). **ARM = out of scope.**
- [ ] Backed up what you cannot lose? Signed line. **No backup = out of scope.**
- [ ] No pending Windows update? If pending: install, restart, come back. **Pending = out of scope for now.**
- [ ] BitLocker / Device Encryption turned off? (If unsure, we verify at the seat.)
- [ ] BitLocker recovery key saved if one exists? (If unsure, we verify at the seat.)
- [ ] Laptop charged, charger with you? **No charger = out of scope.**
- [ ] USB-A port, or USB-C to USB-A adapter? **No usable port = out of scope.**
- [ ] Ever had Linux or another OS? **Yes = flag for Boot department early.**

### 0.2 From msinfo32 (operator writes on form)

- [ ] BIOS Mode: UEFI or Legacy. **Legacy = out of scope.**
- [ ] Secure Boot State
- [ ] Installed RAM (GB)
- [ ] System Model (for drivers)
- [ ] Free space and unallocated space on C: (need ≥ 100 GB)
- [ ] Wi-Fi chip
- [ ] GPU(s)

**Out of scope at door:** ARM, no usable USB port, below 100 GB, no backup, no charger, Legacy BIOS.

Send participant to a seat. Assign operator.

---

## 1. Windows preparation (seat)

### 1.1 BitLocker / Device Encryption — verify and disarm

This is the most important step. Do it before anything else. **No BIOS, no partition, no bootloader change until this is done.**

**The participant should have done the home guide. This section verifies, then finishes disarm at the venue.**

#### 1.1.1 Verify BitLocker is off

Open Command Prompt as Administrator:

- Press **Start**, type `cmd`, right-click **Command Prompt**, choose **Run as administrator**.
- Type:

```
manage-bde -status C:
```

- Look for two lines:
  - `Conversion Status:` must say **Fully Decrypted**
  - `Percentage Encrypted:` must say **0.0%**

**If both match → BitLocker is off. Go to 1.1.4.**

**If anything else → continue to 1.1.2.**

#### 1.1.2 Save the 48-digit key if one exists

- Type:

```
manage-bde -protectors -get C: -type numerical
```

- If a 48-digit key appears (eight groups of six digits), save it:
  - Write it on the declaration form.
  - Take a photo on phone.
  - Text it to yourself.
- If **no** 48-digit key appears, the key is only in the cloud or does not exist. Try `aka.ms/myrecoverykey` with the student's Microsoft account. Match the Key ID shown on the recovery screen.
- If no key can be obtained and the volume is encrypted: **STOP. Raise hand for BitLocker department.**

#### 1.1.3 Turn off BitLocker (only if still encrypted)

- Windows Pro: Control Panel → System and Security → BitLocker Drive Encryption → **Turn off BitLocker**.
- Windows Home: Settings → Privacy & security → Device encryption → **Off**.
- If neither appears, type in admin Command Prompt:

```
manage-bde -off C:
```

- Keep laptop **plugged in and awake**. Decryption takes 30–120 minutes.
- Re-run `manage-bde -status C:` every 15 minutes until **Fully Decrypted, 0.0%**.
- While waiting: do not close lid, do not sleep, do not reboot.

#### 1.1.4 Clear TPM (OP)

- Type:

```
manage-bde -tpm -clear
```

- If that fails, open PowerShell as Administrator and run:

```
Clear-Tpm
```

- Verify:

```
manage-bde -tpm -status
```

#### 1.1.5 Disable BitLocker service and prevent re-enable (OP)

- In admin Command Prompt:

```
reg add "HKLM\SYSTEM\CurrentControlSet\Control\BitLocker" /v PreventDeviceEncryption /t REG_DWORD /d 1 /f
sc config BDESVC start= disabled
sc stop BDESVC
```

- Verify:

```
sc query BDESVC
```

State should say **STOPPED**.

- This blocks Windows from re-arming BitLocker on this install. It does **not** block firmware from re-arming TPM. Warn at handoff.

#### 1.1.6 Final verify

- Re-run `manage-bde -status C:`.
- Must say **Protection Off, Fully Decrypted, 0.0%**.
- If not, **STOP. Raise hand for BitLocker department.**

### 1.2 Fast Startup off

- In admin Command Prompt:

```
powercfg /h off
```

- Verify:

```
powercfg /a
```

Hibernation should say **not available**. Fast Startup is now off.

### 1.3 Pending updates and clean shutdown

- Check for pending updates: Settings → Windows Update. If it says **Restart required**, do it now, boot back to Windows, then return here.
- Check dirty bit:

```
fsutil dirty query C:
```

Must say **NOT Dirty**. If **Dirty**, **STOP. Raise hand.**

- Full shutdown:

```
shutdown /s /t 0
```

- Do **not** use Restart. Do **not** use Fast Startup.

### 1.4 Confirm Windows boots

- Power on. Windows must boot normally.
- If it does not, **STOP. Raise hand for Boot department.**

### 1.5 Shrink C:

**OP explains, participant decides size.**

- Press **Windows key + X**, choose **Disk Management**.
- Right-click `C:` → **Shrink Volume**.
- Rule for size: **minimum 100 GB + RAM + 1 GB.**
  - Example: 16 GB RAM → 100 + 16 + 1 = **117 GB minimum.**
- Enter the number in MB (1 GB = 1024 MB). Example: 117 GB = 119808 MB.
- Click **Shrink**.
- The new space must show as **Unallocated**. Do not format it. Do not assign a letter.
- If the shrinkable amount is far less than free space, **STOP. Raise hand for Storage/Partitioning department.**

### 1.6 Record and proceed to BIOS

- Write on the form: BitLocker status, unallocated size in GB, storage mode, whether Windows boots.
- Only then proceed to section 2.

---

## 2. BIOS / UEFI

### 2.1 How to enter BIOS (per OEM)

Shut down fully first. Then power on and tap the key repeatedly:

| Brand | Key |
|---|---|
| Dell | F2 |
| HP | F10 (or Esc, then F10) |
| Lenovo | F1 (ThinkPad) or F2 (IdeaPad) |
| Acer | F2 |
| ASUS | F2 or Del |
| MSI | Del |
| Samsung | F2 |
| Microsoft Surface | Volume Up + Power |

If the key does not work, try **F12**, **Esc**, or **Del**.

### 2.2 What to change

Menu names vary. Look for these:

- **Boot mode:** UEFI (not Legacy/CSM).
- **Secure Boot:** set to **Disabled**.
- **USB boot:** **Enabled**.
- **Boot order:** USB highest, then Windows Boot Manager, then others.
- **Storage mode:** note it. If **AHCI**, leave it. If **VMD/RST/RAID**, do **not** change yet — go to section 2.3.

### 2.3 If storage mode is VMD/RST/RAID

Do **not** flip it blindly. Windows will not boot.

1. Boot back into Windows.
2. In admin Command Prompt:

```
bcdedit /set {current} safeboot minimal
```

3. Shut down fully.
4. Enter BIOS again. Change storage mode to **AHCI**. Save and exit.
5. Windows boots into Safe Mode. Log in.
6. In admin Command Prompt:

```
bcdedit /deletevalue {current} safeboot
```

7. Restart normally. Confirm Windows boots clean.
8. Only then continue.

### 2.4 Save and exit

- Press **F10** (or the on-screen key) to **Save and Exit**.
- Confirm **Yes**.
- Laptop reboots.

### 2.5 Record

- Write on the form: Secure Boot state, storage mode, boot order, anything unusual.
- If a BIOS setting is locked, greyed out, or needs a supervisor password the student does not have: **STOP. Raise hand for BIOS/Firmware department.**

---

## 3. Boot the live USB

### 3.1 Insert USB

- Plug the blue Kubuntu USB into a USB-A port. Use the adapter if needed.
- Power on. If it does not boot from USB, enter the one-time boot menu (usually **F12**, **F9**, or **Esc**) and pick the USB.

### 3.2 Edit the boot line for `toram`

- At the GRUB menu, highlight **Try or Install Kubuntu**.
- Press **e** to edit.
- Find the line starting with `linux`.
- Find the `---` near the end of that line.
- If RAM ≥ 8 GB: type `toram ` **before** the `---`. Result:

```
... quiet splash toram ---
```

- If RAM < 8 GB: do **not** add `toram`. Leave the USB plugged in.
- Press **F10** or **Ctrl+X** to boot.

### 3.3 Wait for the language screen

- If RAM ≥ 8 GB and `toram` was added: wait until the language screen appears, then **remove the USB**.
- If RAM < 8 GB: leave the USB in.

### 3.4 If it hangs on the logo

- Wait 5 minutes. Watch the USB activity light.
- If still hung: **STOP. Raise hand for Graphics department.**

---

## 4. Live hardware check

- [ ] Desktop loads. Graphics usable.
- [ ] Keyboard and touchpad work.
- [ ] Open a terminal. Type:

```
lsblk
```

You must see `nvme0n1` (or `sda`). If missing: **STOP. Raise hand for Storage Controller department.**

- [ ] Wi-Fi connects. Open Firefox, log into the captive portal if one appears.
- [ ] If no Wi-Fi: **STOP. Raise hand for Wi-Fi/Drivers department.**
- [ ] If any check fails and cannot be fixed: **come back another day.**

---

## 5. Partition / install (OP only)

**Participant does not do this. Operator does. Super signs off before Install.**

- [ ] Identify the correct disk. Match size and model with the form.
- [ ] **Manual partitioning only.** Never Alongside, never automatic.
- [ ] Create **EFI partition**: 1 GB, FAT32, name `linux-efi`, mount `/boot/efi`, boot flag set. Do not touch the Windows EFI.
- [ ] Create **root**: ext4, mount `/`. No swap partition.
- [ ] No other new partitions.
- [ ] **Super review before Install:**
  - Layout matches the Disk Management screenshot.
  - Windows partitions untouched.
  - Windows EFI not mounted, not formatted.
  - Exactly two new partitions.
  - Any mismatch: **STOP. Super decides.**
- [ ] Press Install.
- [ ] Installation completes without a bootloader error. Error: **STOP. Raise hand for BIOS/Boot department.**

---

## 6. First boot

- [ ] GRUB menu appears by default.
- [ ] GRUB lists Windows.
- [ ] Windows missing: **Boot department.**
- [ ] Boots straight to Windows or lands at `grub>`: **Boot department.**
- [ ] Power-cycle test: shut down, power on. GRUB must appear by default, not only via the firmware boot menu.
- [ ] Boot Windows from GRUB. It must come up with **no BitLocker prompt**.
- [ ] Prompt appears: **STOP. BitLocker department. Enter key, confirm Windows boots clean.**
- [ ] Fix the clock: in Kubuntu, open a terminal and run:

```
timedatectl set-local-rtc 1 --adjust-system-clock
```

- [ ] The command prints a warning about local RTC. **This is expected. Ignore it. It is safe for dual-boot.** This keeps Windows and Linux showing the same time.

---

## 7. Minimum handoff validation

- [ ] Kubuntu boots and reboots. Graphics normal. Network works.
- [ ] Windows still boots.
- [ ] Quirks recorded (sleep, Bluetooth, fingerprint, webcam, audio, brightness, external monitor). Non-blocking.

---

## 8. Handoff

- [ ] Tell the student what was installed and any limitations.
- [ ] **Do not re-enable BitLocker. Do not re-enable Fast Startup.**
- [ ] **Do not update firmware (BIOS) without reading the warning sheet.** It can re-arm BitLocker, drop GRUB, or reset storage mode.
- [ ] **After any major Windows update or BIOS update, if Windows asks for a BitLocker key: STOP. Do not factory reset. Get help.**
- [ ] **Your Windows PIN or fingerprint may stop working because boot settings changed. Sign in with your password and re-set them. This is normal.**
- [ ] Gamers (Valorant, kernel anticheat): **Secure Boot re-enable is out of scope at this event.** You need a separate guide. `[TODO: find and test]`
- [ ] Collect the participant's **problem log**.
- [ ] Record unresolved issues.

---

## 9. Policy

- Backup is the student's job. Volunteers give instructions only.
- Charging points provided. Student-caused power loss is not an install failure.
- USB or port failure: try reasonable alternatives, then escalate or park.
- Graphics scope: usable open-source graphics is enough.
- Unknown problem: **stop, raise hand for a Super, no destructive improvising.**

---

## Open decisions

- `[OPEN]` No AHCI option in BIOS: out of scope? `[CONFIRM]`
- `[OPEN]` Secure Boot re-enable for gamers: needs its own guide.
- `[OPEN]` BitLocker disarm: confirm `reg add` + `sc config` + `sc stop` is enough, or add more steps.

## Dry-run findings

- `toram` hung on the logo on an MSI safe-graphics entry.
- Reusing an old Debian EFI left a stale boot entry and dropped to `grub>`.

---

## What changed from v2

1. BitLocker section is now self-contained and exportable. It includes local key retrieval (`manage-bde -protectors -get C: -type numerical`), MSA fallback (`aka.ms/myrecoverykey`), clear TPM, registry key, and service stop.
2. Home vs venue split is explicit. Decryption runs at home; verification and disarm-at-venue are fast.
3. Fast Startup command is written out.
4. Shrink has a worked example and a size rule.
5. BIOS has a key table, menu names, save/exit, and the VMD/RST safe-mode sub-flow with `bcdedit`.
6. `toram` has exact edit instructions.
7. Clock fix added at first boot (`timedatectl set-local-rtc 1`), with the warning explained.
8. Handoff adds firmware-update warning, PIN/fingerprint reset warning, and the “do not factory reset” line.
9. Every “raise hand” names a department.
10. Hard stops are bolded.