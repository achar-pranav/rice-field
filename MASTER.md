# Kubuntu Dual-Boot — Base Flow

## 0. Student Intake / Safety Gate

- [ ] Student understands Linux will be installed alongside Windows
- [ ] Important personal files identified
- [ ] Photos / videos / projects / work copied to designated physical backup location
- [ ] Backup has been spot-checked
- [ ] BitLocker / Device Encryption status checked
- [ ] BitLocker recovery key obtained and accessible
- [ ] **Hard gate: do not proceed with Windows, BIOS, partition, or bootloader changes if backup or recovery key is missing**

## 1. Windows Preparation

- [ ] Disable BitLocker / Device Encryption as required
- [ ] Wait for decryption to complete
- [ ] Disable Windows Fast Startup
- [ ] Ensure Windows is fully shut down
- [ ] Check for pending Windows restart/update state
- [ ] Confirm Windows boots normally before continuing
- [ ] Manually shrink the Windows partition from inside Windows
- [ ] Confirm the intended Linux installation space is now unallocated

## 2. BIOS / UEFI Preparation

- [ ] Set boot mode to UEFI
- [ ] Set Secure Boot to OFF
- [ ] USB boot is enabled / available
- [ ] Boot menu / boot priority understood
- [ ] Storage controller mode identified
- [ ] Check for Intel VMD / RST / RAID
- [ ] Confirm internal Windows disk is visible
- [ ] Check for BIOS/admin/supervisor locks
- [ ] Record unusual OEM-specific BIOS behavior

## 3. Boot Kubuntu Live Environment

- [ ] Boot Kubuntu USB
- [ ] Use `toram`
- [ ] Wait for language-selection screen
- [ ] Confirm live environment is stable
- [ ] Remove/reuse USB only after the visual checkpoint

## 4. Live Environment Hardware Sanity Check

- [ ] Kubuntu desktop loads without crashing
- [ ] Graphics work without emergency graphics parameters
- [ ] Wi-Fi/network works OR a known fallback path is available
- [ ] Proprietary GPU driver installation is **not** required for event acceptance; usable Nouveau/open graphics is sufficient
- [ ] Keyboard works
- [ ] Touchpad/mouse works
- [ ] Internal storage is visible

## 5. Partition / Installation

- [ ] Correct physical disk identified
- [ ] Windows partitions identified
- [ ] Windows EFI / recovery partitions preserved
- [ ] Windows partition has sufficient shrinkable space
- [ ] Linux installation space identified
- [ ] **Manual partitioning only — do not use Alongside / automatic partitioning**
- [ ] Create separate Linux EFI partition
- [ ] Create swap according to the event RAM-sizing rule
- [ ] Create Linux root partition
- [ ] Install Kubuntu
- [ ] Installation completes without bootloader error

## 6. First Boot

- [ ] System reboots successfully
- [ ] Kubuntu boots
- [ ] GRUB appears as the normal default boot path
- [ ] If Linux only boots via F12 / firmware boot selection, record as a bootloader/firmware roadblock
- [ ] Windows boots
- [ ] No unexpected BitLocker recovery prompt

## 7. Minimum Handoff Validation

- [ ] Kubuntu boots reliably
- [ ] Graphics work normally
- [ ] Wi-Fi/network works
- [ ] Windows still boots
- [ ] Kubuntu can reboot successfully
- [ ] Any non-blocking hardware quirks recorded

## 8. Handoff

- [ ] Student told what was installed
- [ ] Student told about any known limitations
- [ ] Any unresolved issue recorded
- [ ] Any new roadblock added to the master notes

---

## 9. Dry Run Policy / Exceptions

- [ ] **4 GB RAM:** Do not use `toram`; perform a normal USB live boot/install workflow.
- [ ] **Minimum free space:** The event will define a minimum required amount of free Windows disk space before installation. Current provisional target: **~100 GB**.
- [ ] **Below minimum space:** If the laptop does not meet the event's minimum free-space requirement, do not proceed with installation; move the student out of the installation workflow.
- [ ] **Power:** Students are instructed to arrive with the laptop sufficiently charged. Charging points are provided, but power loss caused by the student's device/battery is not considered an installation failure.
- [ ] **Backup responsibility:** The student is responsible for creating and verifying their own backup. Volunteers provide instructions and safety guidance but do not guarantee or perform the student's backup.
- [ ] **Cloud-synced files:** Students must verify that important cloud-synced files (for example OneDrive/Google Drive files) are actually available locally and have been copied to the designated physical backup location.
- [ ] **USB / port failure:** If the laptop cannot reliably boot or operate from the available USB ports/adapters, attempt reasonable alternatives; otherwise move the machine to a timeout/escalation state rather than spending excessive event time on hardware-specific problems.
- [ ] **Graphics scope:** Working, usable graphics are required for handoff. Proprietary GPU performance drivers are outside event scope; usable open-source graphics (including Nouveau where applicable) is sufficient.
- [ ] **Wi-Fi scope:** Working Wi-Fi/network access, or a known viable fallback path, is required for handoff.
- [ ] **Non-blocking hardware quirks:** Sleep/hibernate behavior, Bluetooth, fingerprint readers, battery life, brightness/function keys, webcam, audio quirks, and similar QoL issues are logged but do not block handoff unless they prevent the machine from being reasonably usable.
- [ ] **Unknown hardware-specific problems:** Attempt reasonable documented procedures, then escalate/timeout rather than improvising destructive or untested changes.
