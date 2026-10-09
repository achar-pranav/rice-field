# Linux Install Fest: Do This Before You Come

Do these at home, ideally the day before. Don't skip any. Each one protects your files.

**Time needed:** about 2 hours, mostly waiting while your laptop decrypts.

**Bring this document with you to the event.** The operators will ask you to repeat some of these checks at the door.

---

## 1. Back up what you can't replace

- [ ] Copy your photos, videos, projects and documents to a pendrive, external drive, or cloud storage.
- [ ] Check the files actually open from the backup.
- [ ] We can't do this for you, and we aren't responsible for lost data. You'll sign a line saying so at the door.

**This is the most important step.** If your data is not backed up and something goes wrong, it is gone. We do not have a backup station at the event.

---

## 2. Turn off drive encryption

This is the longest step. It can take 30 minutes to 2 hours. You start it, then let it run in the background while you do other things.

**Plug your laptop into the charger and keep it plugged in for the whole step. Do not close the lid. Do not let it sleep. Do not shut it down.**

### 2.1 Open Command Prompt as Administrator

1. Press the **Start** button.
2. Type the three letters: `cmd`
3. Right-click on **Command Prompt** in the results.
4. Click **Run as administrator**.
5. A blue window opens asking "Do you want to allow this app to make changes?" Click **Yes**.

You now have a black or blue window with a blinking cursor. Every command below goes in this window.

### 2.2 Check if encryption is on

Copy this, paste it into the window, press **Enter**:

```
manage-bde -status C:
```

Look for two lines:

- **Conversion Status:** — should say `Fully Decrypted`
- **Percentage Encrypted:** — should say `0.0%`

**If both say that:** encryption is already off. Skip to Step 2.5.

**If anything else:** encryption is on. Continue to 2.3.

### 2.3 Save your 48-digit recovery key

Copy this, paste it, press **Enter**:

```
manage-bde -protectors -get C: -type numerical
```

Look for a line that says **Password:** followed by 48 digits, in 8 groups of 6, separated by dashes:

```
123456-789012-345678-901234-567890-123456-789012-345678
```

Save it in at least two places:

1. Take a photo of the screen with your phone.
2. Write it on paper and put it in your bag.
3. Optional: text it to yourself.

**Do not save it only on the laptop.** If the laptop is locked, you cannot get the key from the laptop.

**If no 48-digit number appears:** the key may only be online. Go to `aka.ms/myrecoverykey` on your phone, sign in with the same Microsoft account you use on this laptop, find your laptop by name, and save the 48-digit key shown there.

**If you cannot find the key anywhere: STOP. Do not continue. Contact the event organizers before the event.**

### 2.4 Turn off encryption

**Windows Home:**

1. Click **Start** → **Settings**.
2. Click **Privacy & security** on the left.
3. Click **Device encryption**.
4. Turn the switch to **Off**.

**Windows Pro:**

1. Click **Start**, type `Control Panel`, open it.
2. Click **System and Security**.
3. Click **BitLocker Drive Encryption**.
4. Next to `C:`, click **Turn off BitLocker**.
5. Confirm.

**If neither option works**, back in the Command Prompt window:

```
manage-bde -off C:
```

### 2.5 Wait for decryption

Decryption runs in the background. Every 15 minutes, run this in the Command Prompt window:

```
manage-bde -status C:
```

Wait until you see:

```
Conversion Status:    Fully Decrypted
Percentage Encrypted: 0.0%
```

**Keep the laptop plugged in. Keep it awake. Do not close the lid. Do not shut down or restart until it says 0.0%.**

### 2.6 Clear the TPM

The TPM is a small security chip that stores a copy of the encryption key. We are wiping it so the chip stops holding onto your disk.

In the Command Prompt window:

```
manage-bde -tpm -clear
```

If that fails, open PowerShell as Administrator (Start → type `PowerShell` → right-click → Run as administrator) and run:

```
Clear-Tpm
```

Verify:

```
manage-bde -tpm -status
```

---

## 3. Turn off Fast Startup

This is a Windows feature that keeps part of Windows loaded even after shutdown. Linux needs it off.

In the Command Prompt window (still as Administrator):

```
powercfg /h off
```

Verify:

```
powercfg /a
```

Hibernation should say **not available**. That means Fast Startup is off.

---

## 4. Install all Windows updates

1. Click **Start** → **Windows Update**.
2. Click **Check for updates**.
3. Install everything it offers.
4. **If it says "Restart required," restart the laptop.**
5. After restart, open Windows Update again and check again.
6. Repeat until there are no more updates and no more pending restarts.

**Why this matters:** if Windows has a half-installed update when you arrive at the event, the install cannot proceed.

---

## 5. Make space for Linux

**Do this at the event.** The operator will help you. Do not shrink your disk at home unless an operator tells you to.

Skip to Step 6.

---

## 6. Shut down fully

In the Command Prompt window:

```
shutdown /s /t 0
```

The laptop shuts down completely. **Do not use the normal Start menu Shut down button** — use this command.

**Do not restart. Do not use Fast Startup.**

---

## 7. Verify before you come

Power on the laptop. Log in to Windows. Open Command Prompt as Administrator again (see 2.1). Run:

```
manage-bde -status C:
```

Confirm it says:

- **Conversion Status: Fully Decrypted**
- **Percentage Encrypted: 0.0%**

If it says anything else, repeat Step 2.

---

## 8. What to bring

- [ ] Laptop **fully charged**, plus the charger.
- [ ] A USB-C to USB-A adapter if your laptop has no normal USB port.
- [ ] A USB Wi-Fi or Ethernet adapter, if you already own one.
- [ ] The 48-digit recovery key, on your phone and on paper.
- [ ] This document.

---

## 9. Know your laptop

- [ ] How much memory (RAM) it has: Settings → System → About.
- [ ] Whether it has ever had Linux or another operating system on it.
- [ ] Laptops with Snapdragon (ARM) processors cannot be installed at this event.

---

## Checklist before you leave home

- [ ] Backed up everything you cannot lose, and checked the backup opens.
- [ ] BitLocker is **Fully Decrypted, 0.0%**.
- [ ] 48-digit recovery key saved on phone and on paper.
- [ ] TPM cleared.
- [ ] Fast Startup off.
- [ ] All Windows updates installed, no restart pending.
- [ ] Laptop shut down fully.
- [ ] Laptop fully charged, charger packed.
- [ ] USB-C to USB-A adapter packed if needed.
- [ ] This document packed.

---

## If something goes wrong

**"manage-bde is not recognized"** — you are not in Command Prompt as Administrator. Close the window and redo Step 2.1.

**"Access denied"** — same. You are not running as Administrator.

**Decryption stuck** — restart the laptop, open Command Prompt as Administrator, run `manage-bde -status C:` again. It resumes.

**BitLocker recovery screen appears** — enter the 48-digit key you saved. If you did not save it, go to `aka.ms/myrecoverykey` on your phone and sign in.

**Cannot find recovery key anywhere** — **stop, do not continue.** Bring the laptop to the event and tell the operators at the door.

---

## At the door

We'll ask a few quick questions and have you open the System Information app (`msinfo32`), so have your laptop on. Then we'll find you a seat. You do the simple steps, and a volunteer helps with the tricky ones.