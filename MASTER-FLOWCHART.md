# Master Flowchart (v2 draft)

Main line runs top to bottom. `──► [DEPT]` is a side exit to a department; the laptop returns at the stated rejoin point.
Departments (`[CONFIRM]`): **BL** BitLocker, **WIFI** Wi-Fi/Drivers, **GPU** Graphics, **STOR** Storage Controller, **BIOS** BIOS/Firmware, **BOOT** EFI/GRUB, **PART** Installer/Partitioning.
**OP** = operator-only step. **SUPER** = call a Super for anything even slightly wrong.

```
==================== DOOR (verbal + msinfo32) ====================
        [ Fill declaration form: self-declared answers + msinfo32 fields ]
        [ Signed line: backed up, proceeding at own risk                 ]
                               │
        [ ARM / no USB port / below min space? ] ──YES──► [ OUT OF SCOPE ]
                               │ NO
        [ Ever had Linux/other OS? ] ──YES──► [ Flag for BOOT early ]
                               │
                               ▼
============================ SEAT ================================
                      [ 1. WINDOWS PREP ]
                               │
        [ manage-bde -status C: → Fully Decrypted, 0.0%? ]
                       │ YES           │ NO
                       │               └──► [BL] decrypt in waiting area ──┐
                       │                    (OP: verify all 48 key digits) │
                       │◄──────────────── rejoin here ─────────────────────┘
        [ Fast Startup off · clean shutdown · Windows boots ]
        [ Shrink C: (100 GB + RAM + 1 GB) · space unallocated? ]
                       │ YES           │ NO ──► [PART] shrink fix ──► rejoin here
                               ▼
                      [ 2. BIOS / UEFI ]
        [ UEFI · Secure Boot OFF · USB boot on ]
                       │               │ locked / odd menu ──► [BIOS] ──► rejoin here
                               ▼
                      [ 3. LIVE BOOT ]
        [ RAM ≥ 6 GB? ]── YES ──► [ toram before --- · yank USB at language screen ]
                       └── NO  ──► [ no toram · USB stays in ]
                       │               │ hangs on logo ──► [GPU] ──► rejoin here
                               ▼
                   [ 4. LIVE HARDWARE CHECK ]
        [ Desktop, keyboard, touchpad OK? ]
        [ lsblk shows nvme0n1? ]── NO ──► [STOR] AHCI flow ──► rejoin at BIOS (2)
        [ Wi-Fi + Firefox captive portal OK? ]
                       │               │ NO ──► [WIFI]: driver USB → external NIC
                       │               │          fail ──► [ COME BACK ANOTHER DAY ]
                       │               └────────────────────► rejoin here on success
                               ▼
                   [ 5. PARTITION / INSTALL ]
        [ OP: manual partitioning in the unallocated block ]
        [ OP: new EFI 1 GB FAT32 "linux-efi" /boot/efi + boot flag ]
        [ OP: root ext4 /  (no swap partition) ]
        [ OP: single-operator review ]── anything wrong ──► [ SUPER ]
                               │ OK
        [ Install ]── error ──► [PART] ──► rejoin here
                               ▼
                    [ 6. FIRST BOOT ]
        [ Create swapfile = RAM ]
        [ GRUB default + Windows listed? ]── NO ──► [BOOT] ──► rejoin here
        [ Power-cycle: GRUB first, Windows boots, no BitLocker prompt? ]
                       │               │ prompt ──► [BL] ──► rejoin here
                               ▼
                   [ 7. MINIMUM HANDOFF VALIDATION ]
        [ Kubuntu reboots · graphics OK · network OK · Windows boots ]
                               ▼
                         [ 8. HANDOFF ]
        [ Tell what was installed + limitations · record issues ]
                               ▼
                       [ SYSTEM COMPLETE ]
```

## Any step

- Unknown problem or destructive step needed: stop and call a **SUPER**.
- Timeout before parking a laptop: `[OPEN]`.
- Each department branch needs its own sub-flowchart and checklist (BitLocker first). `[OPEN]`
