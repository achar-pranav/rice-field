# Linux Install Fest: Do This Before You Come

Do these at home, ideally the day before. Don't skip any. Each one protects your files.

## 1. Back up what you can't replace
- [ ] Copy your photos, videos, projects and documents to a pendrive, external drive, or cloud storage.
- [ ] Check the files actually open from the backup.
- [ ] We can't do this for you, and we aren't responsible for lost data. You'll sign a line saying so at the door.

## 2. Turn off drive encryption (this one takes the longest)
- [ ] Click Start, type `cmd`, right-click Command Prompt, choose **Run as administrator**.
- [ ] Type: `manage-bde -status C:`
- [ ] You need to see **Fully Decrypted** and **0.0%**. If it says anything else, turn encryption off:
  - Windows Home: Settings > Privacy & security > Device encryption > Off.
  - Windows Pro: Control Panel > BitLocker Drive Encryption > Turn off.
- [ ] Keep the laptop **plugged in and awake** until it reaches 100%. This can take an hour or more.
- [ ] Run the check again and take a screenshot.

## 3. Save your recovery key
- [ ] Go to `aka.ms/myrecoverykey`, sign in, and save or print the long 48-digit key.

## 4. Make space for Linux
- [ ] Press **Windows key + X**, open **Disk Management**.
- [ ] Right-click `C:`, choose **Shrink Volume**, and shrink by at least **[X] GB** `[OPEN: number to be confirmed]`.
- [ ] Leave the new space as **Unallocated**. Don't format it or give it a letter.
- [ ] Turn off Fast Startup: Control Panel > Power Options > Choose what the power buttons do > untick **Turn on fast startup**.
- [ ] Shut down fully, and don't leave a Windows update half-finished.

## 5. Bring
- [ ] Laptop **fully charged**, plus the charger.
- [ ] A USB-C to USB-A adapter if your laptop has no normal USB port.
- [ ] A USB Wi-Fi or Ethernet adapter, if you already own one.

## 6. Know your laptop
- [ ] How much memory (RAM) it has: Settings > System > About.
- [ ] Whether it has ever had Linux or another operating system on it.
- [ ] Laptops with Snapdragon (ARM) processors can't be installed at this event.

## At the door
We'll ask a few quick questions and have you open the System Information app (`msinfo32`), so have your laptop on. Then we'll find you a seat. You do the simple steps, and a volunteer helps with the tricky ones.
