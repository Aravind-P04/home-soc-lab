# Winlogbeat Configuration — Windows Event Log Collection

## What Winlogbeat does
Ships Windows Event Logs from the target Windows VM
into Elasticsearch so they appear in Kibana alongside
Suricata IDS alerts.

## Install on Windows VM

Download from elastic.co/downloads/beats/winlogbeat
Run PowerShell as Administrator:
```powershell
cd "C:\Program Files\Winlogbeat"
.\install-service-winlogbeat.ps1
```

## Configure winlogbeat.yml
```yaml
winlogbeat.event_logs:
  - name: Application
  - name: System
  - name: Security
    event_id: 4624, 4625, 4648, 4688, 4698, 4720, 7036
  - name: Microsoft-Windows-PowerShell/Operational
    event_id: 4103, 4104

output.elasticsearch:
  hosts: ["YOUR_ELK_SERVER_IP:9200"]

setup.kibana:
  host: "YOUR_ELK_SERVER_IP:5601"
```

## Key Event IDs to collect

| Event ID | What it captures |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon — brute force detection |
| 4648 | Logon with explicit credentials — lateral movement |
| 4688 | New process created — execution detection |
| 4698 | Scheduled task created — persistence detection |
| 4720 | User account created — persistence detection |
| 4104 | PowerShell script block logging — execution detection |
| 7036 | Service stopped/started — defense evasion detection |

## Start Winlogbeat
```powershell
Start-Service winlogbeat
Get-Service winlogbeat
```

## Verify logs in Kibana
Kibana → Discover → filter by agent.type: winlogbeat
You should see Windows Security events flowing in.

## Notes
- Event ID 4104 requires PowerShell script block logging
  enabled via Group Policy
- Collect Security log minimum — most detections rely on it
- Replace YOUR_ELK_SERVER_IP with your Oracle Cloud VM IP
