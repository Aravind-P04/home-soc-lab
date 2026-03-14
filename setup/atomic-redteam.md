# Atomic Red Team Setup

## What is Atomic Red Team
Open source attack simulation framework by Red Canary.
Each atomic test maps to a MITRE ATT&CK technique and runs
a real attack in a controlled way to verify detections.

Free: github.com/redcanaryco/atomic-red-team

## Install on Windows VM

Open PowerShell as Administrator:
```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)

Install-AtomicRedTeam -getAtomics

Import-Module Invoke-AtomicRedTeam
```

## Running tests
```powershell
# see what a test does before running
Invoke-AtomicTest T1059.001 -ShowDetails

# run the test
Invoke-AtomicTest T1059.001

# run and cleanup after
Invoke-AtomicTest T1059.001 -Cleanup
```

## Tests run in this lab

| Test | Command | Purpose |
|---|---|---|
| PowerShell execution | Invoke-AtomicTest T1059.001 | Verify script block logging |
| Credential dumping | Invoke-AtomicTest T1003.001 | Test Sysmon lsass detection |
| Valid account abuse | Invoke-AtomicTest T1078 | Test auth anomaly detection |
| Impair defenses | Invoke-AtomicTest T1562.001 | Test AV disable detection |

## Important notes
- Run ONLY on lab VM — never on work or personal machine
- Disable AV on the lab VM before running tests
- Take a VM snapshot before running so you can restore cleanly
