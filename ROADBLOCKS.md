# Possible Roadblocks / Runbook Candidates

## Windows / Encryption

- **BitLocker enabled** — Kubuntu installer warns that Windows is encrypted or refuses to resize the Windows partition.
- **Windows Device Encryption enabled** — Windows Home reports Device Encryption rather than the traditional BitLocker interface, confusing the preparation step.
- **Cannot disable BitLocker** — Windows refuses to start or complete decryption, or encryption remains active after the volunteer thinks it was disabled.
- **BitLocker recovery key unavailable** — A BIOS, Secure Boot, TPM, or bootloader change causes Windows to request a recovery key the student cannot provide.
- **BitLocker recovery after BIOS change** — Windows suddenly enters BitLocker recovery after changing Secure Boot, boot order, firmware, or storage settings.
- **Windows does not boot normally** — The machine already has a Windows boot/recovery problem before Linux installation begins.
- **Pending Windows update/restart** — Windows reports that an update or restart is pending, leaving the system in an unexpected state before partitioning.

## Windows Filesystem / Partitioning

- **Fast Startup still enabled** — Linux sees the Windows NTFS volume as hibernated, dirty, or unsafe to modify.
- **Windows partition cannot be shrunk** — Windows reports much less shrinkable space than the amount of free space visible in File Explorer.
- **Insufficient shrinkable space despite free space** — Windows reports plenty of free capacity but cannot create the required contiguous unallocated space at the end of the partition.
- **Unmovable files block shrinking** — Page files, shadow copies, or other files near the end of the partition prevent Windows from shrinking it far enough.
- **Windows filesystem errors** — Disk Management or Linux reports filesystem problems when trying to resize or inspect the Windows partition.
- **Bad sectors / failing drive** — The laptop shows disk errors, freezes, SMART warnings, or other signs that the storage device may be unhealthy.
- **Dynamic Disk detected** — Windows reports the disk as Dynamic instead of a normal Basic disk, complicating Linux partitioning and booting.
- **Storage Spaces detected** — Windows is using Storage Spaces and the physical disk layout does not look like a normal Windows installation.
- **Multiple internal drives** — The laptop contains two or more internal disks, making it easy to accidentally install or modify the wrong one.
- **Unusual OEM recovery layout** — The partition table contains several small OEM, recovery, or diagnostic partitions that are easy to misidentify.

## BIOS / UEFI

- **UEFI vs Legacy mismatch** — Windows was installed in UEFI mode but the Kubuntu USB is being booted in Legacy/CSM mode, or vice versa.
- **Secure Boot cannot be disabled** — The Secure Boot option is missing, greyed out, locked, or only appears after changing another firmware setting.
- **BIOS supervisor password required** — The firmware refuses to change Secure Boot, boot order, or other settings without an administrator/supervisor password.
- **BIOS settings do not persist** — A setting appears changed, but after reboot the laptop silently restores the previous configuration.
- **USB boot disabled** — The laptop boots Windows normally but the Kubuntu USB never appears as a bootable device.
- **USB missing from boot menu** — The USB works on other laptops but does not appear in this laptop's one-time boot menu.
- **Fast Boot blocks USB access** — The firmware starts Windows too quickly or skips USB devices unless a firmware setting is changed.
- **Unusual OEM BIOS layout** — The required setting exists but is hidden under an unexpected menu or uses different terminology.
- **Acer firmware lock** — Acer firmware requires a supervisor password or a specific sequence before Secure Boot or boot options can be changed.
- **HP firmware lock** — HP firmware exposes Secure Boot, Legacy Support, or boot options differently from the procedure documented for another machine.
- **Lenovo firmware differences** — Lenovo firmware uses different names or locations for Secure Boot, boot order, or storage-controller settings.
- **ASUS firmware differences** — ASUS firmware uses different names or menu structures for the settings needed by the installer.
- **Dell firmware differences** — Dell firmware uses different names or menu structures for the settings needed by the installer.

## Storage Controller

- **Intel VMD enabled** — Kubuntu boots normally but the installer cannot see the internal NVMe drive because Intel VMD is controlling it.
- **Intel RST enabled** — Ubuntu/Kubuntu cannot access the Windows disk because the storage controller is configured for Intel Rapid Storage Technology.
- **RAID mode enabled** — The laptop presents its NVMe storage through a RAID-style controller even though the student is not intentionally using RAID.
- **NVMe missing in installer** — Windows can see the SSD but the Kubuntu installer shows no usable internal disk.
- **Changing VMD/RST breaks Windows** — After changing the storage-controller mode, Windows stops booting or enters recovery.
- **Unknown storage-controller configuration** — The firmware exposes a storage mode but it is unclear whether Windows depends on it.
- **Multiple NVMe drives cause confusion** — Several internal NVMe devices appear in the installer and it is unclear which one contains Windows.

## USB / Kubuntu Live Boot

- **ISO is corrupted** — The Kubuntu image fails to boot, crashes, or behaves differently across otherwise identical USB sticks.
- **Bad USB stick** — The installer repeatedly fails or crashes from one USB device but works from another.
- **USB boots on one laptop but not another** — The same installer media behaves differently across machines due to firmware or hardware compatibility.
- **Kubuntu live environment hangs** — The machine boots the USB but freezes before reaching the desktop or language-selection screen.
- **`toram` fails** — The system cannot copy the live environment into RAM and does not reach the normal live-session interface.
- **Low RAM prevents `toram` from completing** — The live system cannot fully load into memory on a machine with insufficient available RAM.
- **USB removed too early** — Removing the USB before the live system has finished loading causes repeated boot errors or a broken session.
- **Keyboard/input broken in live session** — Kubuntu reaches the live environment but keyboard, touchpad, or other input devices do not work correctly.

## Graphics

- **Black screen during live boot** — The Kubuntu USB starts loading but the display turns black before the desktop appears.
- **NVIDIA graphics problem** — A machine with NVIDIA graphics hangs, glitches, or fails to display correctly during the live boot or installation.
- **AMD graphics problem** — The live environment or installed system shows a black screen, graphical corruption, or boot failure on an AMD GPU.
- **Intel graphics problem** — The laptop boots into Kubuntu but graphics initialization fails or the display becomes unusable.
- **Hybrid graphics problem** — A laptop with integrated and discrete GPUs behaves differently depending on which GPU the firmware or Linux uses.
- **`nomodeset` required** — Kubuntu only reaches the desktop after using `nomodeset` or another fallback graphics boot parameter.
- **Safe Graphics required** — The normal Kubuntu live boot fails but the installer works using the fallback/Safe Graphics option.
- **Graphics work live but fail after install** — The live USB displays correctly, but the installed Kubuntu system boots to a black screen or broken graphics.
- **External monitor failure** — HDMI, DisplayPort, USB-C display output, or a dock works in Windows but not in Kubuntu.

## Networking

- **Wi-Fi missing completely** — Kubuntu boots normally but no wireless networks or wireless adapter are detected.
- **Broadcom Wi-Fi driver missing** — The laptop's Broadcom wireless adapter is present in hardware but has no usable Linux driver or firmware.
- **Realtek Wi-Fi driver/firmware missing** — The laptop's Realtek adapter is detected poorly or does not connect because the required firmware/driver is unavailable.
- **Intel Wi-Fi firmware issue** — The wireless adapter is detected but firmware loading fails or the interface repeatedly disconnects.
- **Wi-Fi works in Windows but not Kubuntu** — The hardware is known-good in Windows but Linux does not expose a working wireless interface.
- **No Ethernet available** — The laptop has no working Ethernet port or adapter, leaving Wi-Fi as the only network path.
- **USB tethering fails** — A phone is available as an emergency network connection but Kubuntu does not recognize the USB tether.
- **Network works live but not after install** — The live USB has working networking, but the installed Kubuntu system loses the adapter or firmware.
- **No network for driver installation** — A graphics or Wi-Fi problem requires downloading packages, but the machine has no working network connection.

## Installer / Calamares

- **Kubuntu installer will not launch** — Clicking the installer does nothing, returns to the live desktop, or exits unexpectedly.
- **Installer crashes** — Calamares starts but crashes during preparation, partitioning, copying files, or configuration.
- **Installer cannot see disk** — The installer opens normally but no internal disk or Windows partition is available to select.
- **Installer cannot resize Windows** — The automated alongside-install option refuses to shrink the Windows partition or reports that the disk is unsafe.
- **Unexpected partition layout** — The installer shows partitions that do not match what the volunteer expected from Windows Disk Management.
- **Partitioning fails** — Creating, deleting, resizing, or formatting a partition produces an error during installation.
- **Installation fails near completion** — Kubuntu copies almost everything successfully and then fails during the final configuration stage.
- **GRUB installation fails** — The installation reaches the end but reports that the bootloader could not be installed.
- **`efibootmgr` / NVRAM full** — The installer reports an EFI variable or “no space left on device” error while creating the boot entry.
- **EFI System Partition problem** — The installer cannot use the existing EFI partition or reports that the EFI partition is too small or unsuitable.
- **Installation succeeds but Linux will not boot** — Files appear to have installed correctly but the laptop does not present a usable Linux boot option afterward.

## GRUB / EFI / Boot

- **System boots directly to Windows** — Kubuntu installed successfully but the firmware continues launching Windows Boot Manager first.
- **Linux boots only through F12** — The Kubuntu installation works when manually selected from the firmware boot menu, but the machine does not use GRUB as the normal default boot path.
- **GRUB does not appear** — The machine boots directly into an operating system without ever showing the expected GRUB menu.
- **Windows missing from GRUB** — Linux boots and GRUB works, but no Windows option appears in the menu.
- **Kubuntu missing from firmware boot menu** — The Linux bootloader exists on disk but the UEFI firmware does not show a Kubuntu/GRUB entry.
- **Windows Boot Manager remains first** — A GRUB entry exists but firmware boot priority keeps selecting Windows.
- **GRUB installed to wrong disk** — The bootloader was written to a different physical drive than the one intended by the volunteer.
- **Wrong EFI partition selected** — The installer or manual repair uses the wrong FAT32 EFI System Partition.
- **EFI partition accidentally modified** — Windows or another operating system stops booting after the EFI partition was reformatted or changed.
- **UEFI NVRAM full** — Linux cannot create another EFI boot variable even though the disk itself has plenty of free space.
- **EFI System Partition too small** — The bootloader cannot fit or update because the EFI filesystem has insufficient free space.
- **Separate Linux EFI behaves inconsistently across OEMs** — The Linux EFI partition exists and contains the bootloader, but firmware on some laptops does not register, expose, or prefer its boot entry consistently.
- **Windows bootloader damaged** — Windows no longer starts after an installation or bootloader change.
- **Windows enters BitLocker recovery after installation** — Windows requests the recovery key after the Linux boot path, firmware state, or Secure Boot state changes.

## Post-Install Blockers

- **Kubuntu boots but graphics are unusable** — The installed desktop is black, corrupted, extremely low-resolution, or otherwise impractical to use.
- **Wi-Fi missing after installation** — Networking worked differently or not at all after the installed system replaced the live environment.
- **Network works temporarily then disappears** — Wi-Fi or Ethernet works after installation but breaks after reboot, update, or suspend.
- **Reboot breaks graphics or networking** — The first session works, but the next boot exposes a driver or firmware problem.
- **Windows no longer boots** — Kubuntu starts but the Windows installation is inaccessible or fails to boot.
- **Kubuntu no longer boots** — Windows still works but the Linux boot entry or Linux installation is no longer usable.
- **GRUB disappears after Windows update** — A later Windows update or firmware change restores Windows as the default boot path or removes the expected GRUB experience.

## Unknown / Hardware-Specific

- **Laptop behaves differently from documented procedure** — The expected BIOS, storage, boot, or installer behavior does not match what the volunteer sees.
- **BIOS option has an unexpected name** — The required firmware setting appears to exist but uses unfamiliar OEM-specific terminology.
- **Expected disk or partition is missing** — A disk or partition visible in Windows is absent or represented differently in the Linux installer.
- **Fix produces an unexpected Windows result** — A supposedly safe change causes Windows to behave differently from the expected result.
- **Fix works on one laptop but not another** — The same procedure produces different results on different OEMs or hardware generations.
- **Unknown error message** — The machine produces an error that does not match an existing roadblock entry.
- **Untested command or destructive action required** — Resolving the problem appears to require a command or change that has not yet been validated by the team.
