 ╭──────────────────────────────────────────────────────────╮
 │  rice-field  ·  Linux Install Fest — Ops Snapshot        │
 ╰──────────────────────────────────────────────────────────╯

 OS .............. Ubuntu Server 24.04 LTS → KDE Plasma (SDDM)
 Mirror ........... in.archive.ubuntu.com (India)
 Tooling .......... captive-bypass (nmcli + curl + sudo)
 Post-install ..... interactive script (WIP)
 Docs ............. BRIEF (SOP) · FLOWCHART · RUNBOOK · STATS

 PIPELINE
 ════════
 prep → BIOS/toram → dual-TL partition → captive bypass
 → mirror → KDE desktop → post-install (WIP) → QA dual-boot

 ESTIMATES  (cynical)
 ══════════
 LOW  ..... ~18 hands-on actions .......... ~40 min
 MED  ..... ~25 hands-on actions .......... ~1 h 20 m
 HIGH ..... ~32 hands-on actions .......... ~2 h 30 m
   +1 rescue trip · +2 BIOS re-entries · +15 min queue wait
   (assume the portal flakes, the mirror lags, the USB vanishes)

 CRITICAL AREAS
 ═══════════════
 1  partition commit ......... irreversible · dual-TL sign-off
 2  EFI partition ............. never format · /boot/efi only
 3  BitLocker / Device Enc .... must be 100% decrypted
 4  AHCI / RST switch ......... verify Windows boots first
 5  toram yank ................ pass the USB before the queue dies
 6  captive portal ............ single point of network failure

 ATTENDEE REALITY  (cynical)
 ══════════════════
 ~40%  BitLocker / Device Encryption still on
 ~30%  C: shrink not done → Rescue Station
 ~25%  no backup → forced backup or reject
 ~15%  arrive with no BIOS key noted down

 USB YIELD
 ════════
 1 toram USB serves unlimited ≥6 GB machines (yank & pass)
 <6 GB machines hold the USB for the whole install (queue-bound)

 RESCUE LOAD  (forecast)
 ════════════
 ~1 in 4 laptops visits the Rescue Station at least once
 top causes: shrink refusal, missing Wi-Fi driver, BSOD after AHCI
