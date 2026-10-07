# Possible Roadblocks / Runbook Candidates

Every roadblock has an owner department (see each section header). Fixes live in RUNBOOK.md. Unlisted problem: stop and call a Super.
New entries from the dry run and interview are at the bottom.

Status tags: `[FIX: <runbook entry>]` has a fix in RUNBOOK.md (or a MASTER step). `[NO FIX YET]` needs one. `[OUT OF SCOPE]` is screened out. `[ESCALATE: Super]` has no documented fix.
All fixes count as untested until the dry run; change a tag to `[TESTED]` by hand once it has been run on real hardware.

## Windows / Encryption — Dept: BitLocker

- **BitLocker enabled** `[FIX: B1]` — Kubuntu installer warns that Windows is encrypted or refuses to resize the Windows partition.
- **Windows Device Encryption enabled** `[FIX: B1]` — Windows Home reports Device Encryption rather than the traditional BitLocker interface, confusing the preparation step.
- **Cannot disable BitLocker** `[FIX: B3]` — Windows refuses to start or complete decryption, or encryption remains active after the volunteer thinks it was disabled.
- **BitLocker recovery key unavailable** `[FIX: B4]` — A BIOS, Secure Boot, TPM, or bootloader change causes Windows to request a recovery key the student cannot provide.
- **BitLocker recovery after BIOS change** `[FIX: B5]` — Windows suddenly enters BitLocker recovery after changing Secure Boot, boot order, firmware, or storage settings.
- **Windows does not boot normally** `[NO FIX YET]` — The machine already has a Windows boot/recovery problem before Linux installation begins.
- **Pending Windows update/restart** `[NO FIX YET]` — Windows reports that an update or restart is pending, leaving the system in an unexpected state before partitioning.

## Windows Filesystem / Partitioning — Dept: Installer/Partitioning

- **Fast Startup still enabled** `[FIX: MASTER 1]` — Linux sees the Windows NTFS volume as hibernated, dirty, or unsafe to modify.
- **Windows partition cannot be shrunk** `[FIX: P1]` — Windows reports much less shrinkable space than the amount of free space visible in File Explorer.
- **Insufficient shrinkable space despite free space** `[FIX: P1]` — Windows reports plenty of free capacity but cannot create the required contiguous unallocated space at the end of the partition.
- **Unmovable files block shrinking** `[FIX: P1]` — Page files, shadow copies, or other files near the end of the partition prevent Windows from shrinking it far enough.
- **Windows filesystem errors** `[FIX: P2]` — Disk Management or Linux reports filesystem problems when trying to resize or inspect the Windows partition.
- **Bad sectors / failing drive** `[OUT OF SCOPE (P2)]` — The laptop shows disk errors, freezes, SMART warnings, or other signs that the storage device may be unhealthy.
- **Dynamic Disk detected** `[OUT OF SCOPE (S4)]` — Windows reports the disk as Dynamic instead of a normal Basic disk, complicating Linux partitioning and booting.
- **Storage Spaces detected** `[OUT OF SCOPE (S4)]` — Windows is using Storage Spaces and the physical disk layout does not look like a normal Windows installation.
- **Multiple internal drives** `[FIX: S3]` — The laptop contains two or more internal disks, making it easy to accidentally install or modify the wrong one.
- **Unusual OEM recovery layout** `[FIX: P3]` — The partition table contains several small OEM, recovery, or diagnostic partitions that are easy to misidentify.

## BIOS / UEFI — Dept: BIOS/Firmware

- **UEFI vs Legacy mismatch** `[FIX: F4]` — Windows was installed in UEFI mode but the Kubuntu USB is being booted in Legacy/CSM mode, or vice versa.
- **Secure Boot cannot be disabled** `[FIX: F1]` — The Secure Boot option is missing, greyed out, locked, or only appears after changing another firmware setting.
- **BIOS supervisor password required** `[FIX: F1]` — The firmware refuses to change Secure Boot, boot order, or other settings without an administrator/supervisor password.
- **BIOS settings do not persist** `[FIX: F3]` — A setting appears changed, but after reboot the laptop silently restores the previous configuration.
- **USB boot disabled** `[FIX: F2]` — The laptop boots Windows normally but the Kubuntu USB never appears as a bootable device.
- **USB missing from boot menu** `[FIX: F2]` — The USB works on other laptops but does not appear in this laptop's one-time boot menu.
- **Fast Boot blocks USB access** `[FIX: F2]` — The firmware starts Windows too quickly or skips USB devices unless a firmware setting is changed.
- **Unusual OEM BIOS layout** `[NO FIX YET]` — The required setting exists but is hidden under an unexpected menu or uses different terminology.
- **Acer firmware lock** `[NO FIX YET]` — Acer firmware requires a supervisor password or a specific sequence before Secure Boot or boot options can be changed.
- **HP firmware lock** `[NO FIX YET]` — HP firmware exposes Secure Boot, Legacy Support, or boot options differently from the procedure documented for another machine.
- **Lenovo firmware differences** `[NO FIX YET]` — Lenovo firmware uses different names or locations for Secure Boot, boot order, or storage-controller settings.
- **ASUS firmware differences** `[NO FIX YET]` — ASUS firmware uses different names or menu structures for the settings needed by the installer.
- **Dell firmware differences** `[NO FIX YET]` — Dell firmware uses different names or menu structures for the settings needed by the installer.

## Storage Controller — Dept: Storage Controller

- **Intel VMD enabled** `[FIX: S1]` — Kubuntu boots normally but the installer cannot see the internal NVMe drive because Intel VMD is controlling it.
- **Intel RST enabled** `[FIX: S1]` — Ubuntu/Kubuntu cannot access the Windows disk because the storage controller is configured for Intel Rapid Storage Technology.
- **RAID mode enabled** `[FIX: S1]` — The laptop presents its NVMe storage through a RAID-style controller even though the student is not intentionally using RAID.
- **NVMe missing in installer** `[FIX: S1]` — Windows can see the SSD but the Kubuntu installer shows no usable internal disk.
- **Changing VMD/RST breaks Windows** `[FIX: S2]` — After changing the storage-controller mode, Windows stops booting or enters recovery.
- **Unknown storage-controller configuration** `[NO FIX YET]` — The firmware exposes a storage mode but it is unclear whether Windows depends on it.
- **Multiple NVMe drives cause confusion** `[FIX: S3]` — Several internal NVMe devices appear in the installer and it is unclear which one contains Windows.

## USB / Kubuntu Live Boot — Dept: BIOS/Firmware (USB boot), Graphics (hangs), Installer (bad media)

- **ISO is corrupted** `[FIX: P7]` — The Kubuntu image fails to boot, crashes, or behaves differently across otherwise identical USB sticks.
- **Bad USB stick** `[FIX: P7]` — The installer repeatedly fails or crashes from one USB device but works from another.
- **USB boots on one laptop but not another** `[NO FIX YET]` — The same installer media behaves differently across machines due to firmware or hardware compatibility.
- **Kubuntu live environment hangs** `[FIX: G2]` — The machine boots the USB but freezes before reaching the desktop or language-selection screen.
- **`toram` fails** `[FIX: G2]` — The system cannot copy the live environment into RAM and does not reach the normal live-session interface.
- **Low RAM prevents `toram` from completing** `[FIX: G2]` — The live system cannot fully load into memory on a machine with insufficient available RAM.
- **USB removed too early** `[FIX: P7]` — Removing the USB before the live system has finished loading causes repeated boot errors or a broken session.
- **Keyboard/input broken in live session** `[NO FIX YET]` — Kubuntu reaches the live environment but keyboard, touchpad, or other input devices do not work correctly.

## Graphics — Dept: Graphics

- **Black screen during live boot** `[FIX: G1]` — The Kubuntu USB starts loading but the display turns black before the desktop appears.
- **NVIDIA graphics problem** `[FIX: G3]` — A machine with NVIDIA graphics hangs, glitches, or fails to display correctly during the live boot or installation.
- **AMD graphics problem** `[FIX: G3]` — The live environment or installed system shows a black screen, graphical corruption, or boot failure on an AMD GPU.
- **Intel graphics problem** `[FIX: G3]` — The laptop boots into Kubuntu but graphics initialization fails or the display becomes unusable.
- **Hybrid graphics problem** `[FIX: G3]` — A laptop with integrated and discrete GPUs behaves differently depending on which GPU the firmware or Linux uses.
- **`nomodeset` required** `[FIX: G1]` — Kubuntu only reaches the desktop after using `nomodeset` or another fallback graphics boot parameter.
- **Safe Graphics required** `[FIX: G1]` — The normal Kubuntu live boot fails but the installer works using the fallback/Safe Graphics option.
- **Graphics work live but fail after install** `[NO FIX YET]` — The live USB displays correctly, but the installed Kubuntu system boots to a black screen or broken graphics.
- **External monitor failure** `[FIX: G5]` — HDMI, DisplayPort, USB-C display output, or a dock works in Windows but not in Kubuntu.

## Networking — Dept: Wi-Fi/Drivers

- **Wi-Fi missing completely** `[FIX: W1]` — Kubuntu boots normally but no wireless networks or wireless adapter are detected.
- **Broadcom Wi-Fi driver missing** `[FIX: W2]` — The laptop's Broadcom wireless adapter is present in hardware but has no usable Linux driver or firmware.
- **Realtek Wi-Fi driver/firmware missing** `[FIX: W2]` — The laptop's Realtek adapter is detected poorly or does not connect because the required firmware/driver is unavailable.
- **Intel Wi-Fi firmware issue** `[FIX: W1]` — The wireless adapter is detected but firmware loading fails or the interface repeatedly disconnects.
- **Wi-Fi works in Windows but not Kubuntu** `[FIX: W1]` — The hardware is known-good in Windows but Linux does not expose a working wireless interface.
- **No Ethernet available** `[FIX: W3]` — The laptop has no working Ethernet port or adapter, leaving Wi-Fi as the only network path.
- **USB tethering fails** `[NO FIX YET]` — A phone is available as an emergency network connection but Kubuntu does not recognize the USB tether.
- **Network works live but not after install** `[FIX: W5]` — The live USB has working networking, but the installed Kubuntu system loses the adapter or firmware.
- **No network for driver installation** `[FIX: W3]` — A graphics or Wi-Fi problem requires downloading packages, but the machine has no working network connection.

## Installer / Calamares — Dept: Installer/Partitioning

- **Kubuntu installer will not launch** `[FIX: P4]` — Clicking the installer does nothing, returns to the live desktop, or exits unexpectedly.
- **Installer crashes** `[FIX: P4]` — Calamares starts but crashes during preparation, partitioning, copying files, or configuration.
- **Installer cannot see disk** `[FIX: S1]` — The installer opens normally but no internal disk or Windows partition is available to select.
- **Installer cannot resize Windows** `[FIX: P1]` — The automated alongside-install option refuses to shrink the Windows partition or reports that the disk is unsafe.
- **Unexpected partition layout** `[FIX: P3]` — The installer shows partitions that do not match what the volunteer expected from Windows Disk Management.
- **Partitioning fails** `[FIX: P5]` — Creating, deleting, resizing, or formatting a partition produces an error during installation.
- **Installation fails near completion** `[FIX: P6]` — Kubuntu copies almost everything successfully and then fails during the final configuration stage.
- **GRUB installation fails** `[FIX: P6]` — The installation reaches the end but reports that the bootloader could not be installed.
- **`efibootmgr` / NVRAM full** `[FIX: E6]` — The installer reports an EFI variable or “no space left on device” error while creating the boot entry.
- **EFI System Partition problem** `[FIX: E6]` — The installer cannot use the existing EFI partition or reports that the EFI partition is too small or unsuitable.
- **Installation succeeds but Linux will not boot** `[FIX: E3]` — Files appear to have installed correctly but the laptop does not present a usable Linux boot option afterward.

## GRUB / EFI / Boot — Dept: Boot (EFI/GRUB)

- **System boots directly to Windows** `[FIX: E1]` — Kubuntu installed successfully but the firmware continues launching Windows Boot Manager first.
- **Linux boots only through F12** `[FIX: E4]` — The Kubuntu installation works when manually selected from the firmware boot menu, but the machine does not use GRUB as the normal default boot path.
- **GRUB does not appear** `[FIX: E1]` — The machine boots directly into an operating system without ever showing the expected GRUB menu.
- **Windows missing from GRUB** `[FIX: E2]` — Linux boots and GRUB works, but no Windows option appears in the menu.
- **Kubuntu missing from firmware boot menu** `[FIX: E3]` — The Linux bootloader exists on disk but the UEFI firmware does not show a Kubuntu/GRUB entry.
- **Windows Boot Manager remains first** `[FIX: E1]` — A GRUB entry exists but firmware boot priority keeps selecting Windows.
- **GRUB installed to wrong disk** `[NO FIX YET]` — The bootloader was written to a different physical drive than the one intended by the volunteer.
- **Wrong EFI partition selected** `[FIX: E3]` — The installer or manual repair uses the wrong FAT32 EFI System Partition.
- **EFI partition accidentally modified** `[FIX: E5]` — Windows or another operating system stops booting after the EFI partition was reformatted or changed.
- **UEFI NVRAM full** `[FIX: E6]` — Linux cannot create another EFI boot variable even though the disk itself has plenty of free space.
- **EFI System Partition too small** `[FIX: E6]` — The bootloader cannot fit or update because the EFI filesystem has insufficient free space.
- **Separate Linux EFI behaves inconsistently across OEMs** `[FIX: E4]` — The Linux EFI partition exists and contains the bootloader, but firmware on some laptops does not register, expose, or prefer its boot entry consistently.
- **Windows bootloader damaged** `[FIX: E5]` — Windows no longer starts after an installation or bootloader change.
- **Windows enters BitLocker recovery after installation** `[FIX: B5]` — Windows requests the recovery key after the Linux boot path, firmware state, or Secure Boot state changes.

## Post-Install Blockers — Dept: Graphics, Wi-Fi/Drivers, Boot (by symptom)

- **Kubuntu boots but graphics are unusable** `[NO FIX YET]` — The installed desktop is black, corrupted, extremely low-resolution, or otherwise impractical to use.
- **Wi-Fi missing after installation** `[FIX: W5]` — Networking worked differently or not at all after the installed system replaced the live environment.
- **Network works temporarily then disappears** `[NO FIX YET]` — Wi-Fi or Ethernet works after installation but breaks after reboot, update, or suspend.
- **Reboot breaks graphics or networking** `[NO FIX YET]` — The first session works, but the next boot exposes a driver or firmware problem.
- **Windows no longer boots** `[FIX: E5]` — Kubuntu starts but the Windows installation is inaccessible or fails to boot.
- **Kubuntu no longer boots** `[FIX: E3]` — Windows still works but the Linux boot entry or Linux installation is no longer usable.
- **GRUB disappears after Windows update** `[FIX: E7]` — A later Windows update or firmware change restores Windows as the default boot path or removes the expected GRUB experience.

## Unknown / Hardware-Specific — Dept: none. Stop and call a Super

- **Laptop behaves differently from documented procedure** `[ESCALATE: Super]` — The expected BIOS, storage, boot, or installer behavior does not match what the volunteer sees.
- **BIOS option has an unexpected name** `[ESCALATE: Super]` — The required firmware setting appears to exist but uses unfamiliar OEM-specific terminology.
- **Expected disk or partition is missing** `[ESCALATE: Super]` — A disk or partition visible in Windows is absent or represented differently in the Linux installer.
- **Fix produces an unexpected Windows result** `[ESCALATE: Super]` — A supposedly safe change causes Windows to behave differently from the expected result.
- **Fix works on one laptop but not another** `[ESCALATE: Super]` — The same procedure produces different results on different OEMs or hardware generations.
- **Unknown error message** `[ESCALATE: Super]` — The machine produces an error that does not match an existing roadblock entry.
- **Untested command or destructive action required** `[ESCALATE: Super]` — Resolving the problem appears to require a command or change that has not yet been validated by the team.

## New entries (dry run + interview)

### Intake / Scope — Dept: Door / Super
- **ARM laptop (Snapdragon)** `[OUT OF SCOPE]` — msinfo32 System Type is ARM, so the x64 Kubuntu ISO will not boot. Out of scope, screened at the door.
- **No usable USB port** `[OUT OF SCOPE]` — The laptop has only USB-C and the student has no OTG adapter, or the port is flaky. Out of scope if no workaround.
- **Below minimum free space** `[OUT OF SCOPE]` — C: free space is below 100 GB + RAM + 1 GB. Out of scope [OPEN: final number].
- **Laptop previously had Linux or another OS** `[FIX: E3 (flag at door)]` — Old EFI entries and partitions can break the clean-EFI assumption. Flag at the door and send to the Boot department early.
- **Student arrives with no backup** `[FIX: MASTER 0]` — No backup station exists. Student signs the declaration line and proceeds at own risk.

### BitLocker — Dept: BitLocker
- **Student arrives mid-decryption** `[FIX: B1]` — Decryption needs hours. Waiting area needs power outlets and the laptop must stay awake.
- **BitLocker or Device Encryption re-enables itself** `[FIX: B3]` — Windows 11 turns it back on after sign-in or an update. Re-check with `manage-bde -status C:` at the seat.
- **Recovery key digits do not match** `[FIX: B2/B4]` — The student's saved 48 digits differ from what `manage-bde -protectors -get C:` shows. Do not proceed until resolved.
- **Recovery prompt appears mid-event** `[FIX: B5]` — After a BIOS or boot change. Stop, enter the key, confirm Windows boots clean.

### Boot (EFI/GRUB) — Dept: Boot
- **`grub>` prompt after reboot** `[FIX: E3]` — Reused old EFI left a stale firmware entry pointing at a Debian GRUB stub with no menu.
- **Stale firmware entry stays first** `[FIX: E3]` — An old distro entry outranks the new Kubuntu entry in the boot order.
- **Reusing an existing EFI partition** `[FIX: E3]` — Installer pointed at an old ESP with another distro's leftovers. Always create the new `linux-efi` partition.

### Live Boot / Graphics — Dept: Graphics, BIOS/Firmware
- **`toram` hangs on the logo** `[FIX: G2]` — Seen on an MSI on the safe-graphics entry. Splash hides progress; may be a slow copy, low RAM, or `toram` placed after `---`.
- **`toram` typed in the wrong place** `[FIX: G2]` — Text after `---` on the kernel line is not applied.
- **4 GB RAM machine** `[FIX: MASTER 3]` — No `toram`; USB stays plugged in; queue-bound.

### Wi-Fi / Drivers — Dept: Wi-Fi/Drivers
- **Driver USB (red) cannot be mounted or read** `[FIX: W2]` — Wrong filesystem, bad stick, or file layout problem.
- **External NIC not recognized** `[FIX: W3]` — The adapter's chip has no in-kernel driver.
- **Captive portal login fails in live Firefox** `[FIX: W4]` — Portal may bind to a MAC address or never redirect.
- **All fallbacks fail** `[FIX: W6]` — Student is told to come on a later day (likely a broken NIC).

### Installer / Partitioning — Dept: Installer/Partitioning
- **Swapfile missing after install** `[FIX: MASTER 6]` — Manual partitioning creates no swap. Create a swapfile equal to RAM at first boot [UNTESTED].
- **Operator sees a layout that looks even slightly wrong** `[FIX: P3]` — Stop and call a Super before pressing Install.
- **Windows Fast Startup re-enabled by an update** `[FIX: E2]` — Linux sees NTFS as hibernated; os-prober skips it.
