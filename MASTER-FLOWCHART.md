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
