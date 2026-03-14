# Detection Notes

What each simulated attack looks like in Kibana — queries used,
what fired, what didn't, and why.

---

## T1059.001 — PowerShell Execution

**Simulation:** Atomic Red Team T1059.001
```powershell
Invoke-AtomicTest T1059.001
```

**What appeared in Kibana:**
- Event ID 4104 (PowerShell script block logging)
- Event ID 4688 (new process — powershell.exe)
- Parent process: cmd.exe spawning powershell.exe

**KQL query used:**
```
event.code: "4104" and powershell.file.script_block_text: *Invoke*
```

**Result:** ✅ Detected — alert fired within 30 seconds

**Notes:**
Script block logging must be enabled on the Windows VM or Event ID 4104
won't appear. Enable via Group Policy:
Computer Configuration → Administrative Templates → Windows Components
→ Windows PowerShell → Turn on Script Block Logging

---

## T1003.001 — Credential Dumping (LSASS)

**Simulation:** Atomic Red Team T1003.001
```powershell
Invoke-AtomicTest T1003.001
```

**What appeared in Kibana:**
- Sysmon Event ID 10 — process accessing lsass.exe
- Source process: rundll32.exe → lsass.exe
- GrantedAccess: 0x1010 (read memory)

**KQL query used:**
```
event.code: "10" and winlog.event_data.TargetImage: *lsass*
```

**Result:** ✅ Detected — Sysmon caught the lsass memory access

**Notes:**
Requires Sysmon installed on Windows VM with a config that enables
Event ID 10. Used SwiftOnSecurity Sysmon config as baseline.

---

## T1078 — Valid Account Abuse

**Simulation:** Manual — created test account, failed login 5x,
then succeeded from different IP using VPN.

**What appeared in Kibana:**
- Multiple Event ID 4625 (failed logon) from IP-A
- Event ID 4624 (successful logon) from IP-B
- Same username, 4 minute gap

**KQL query used:**
```
event.code: "4625" and winlog.event_data.TargetUserName: "testuser"
```

**Result:** ✅ Detected — impossible travel pattern visible in timeline

---

## T1562 — Impair Defenses

**Simulation:** Atomic Red Team T1562.001 — disable Windows Defender

**What appeared in Kibana:**
- Event ID 7036 — Windows Defender service stopped
- Event ID 4689 — process terminated

**Result:** ⚠️ Partial — service stop detected, direct process kill missed

**Notes:**
Need to add Sysmon Event ID 1 (process creation) rule to catch
the direct kill variant. Current gap in detection coverage.

---

## T1021 — Lateral Movement via SMB

**Status:** 🔄 In progress

Setting up second Windows VM to simulate SMB lateral movement.
Detection via Suricata SMB rules + Windows Event ID 4648
(logon with explicit credentials).
