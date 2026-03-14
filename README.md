# Home SOC Lab — ELK SIEM

A virtualized SOC environment built to practice threat detection,
log analysis, and incident response outside of work. Uses open source
tools only — no paid licenses needed.

Currently a work in progress — setup docs and detection notes added
as the lab evolves.

## Architecture
```
Windows VM (target)
    ↓ Winlogbeat (Windows Event Logs)
Ubuntu VM (ELK server)
    ├── Elasticsearch — log storage and indexing
    ├── Kibana — dashboards and detection queries
    └── Logstash — log parsing and enrichment
Suricata IDS — network traffic analysis
    ↓ Filebeat → ELK Stack
```

## Stack

| Tool | Role |
|---|---|
| Elasticsearch | Log storage and indexing |
| Kibana | SIEM dashboards and KQL queries |
| Logstash | Log parsing and enrichment |
| Filebeat | Log shipping from endpoints |
| Winlogbeat | Windows Event Log collection |
| Suricata IDS | Network intrusion detection |
| Atomic Red Team | Attack simulation framework |

## Attack simulations run

| Technique | MITRE ID | Detected? | Detection method |
|---|---|---|---|
| PowerShell execution | T1059.001 | ✅ Yes | Winlogbeat → Kibana (Event ID 4104) |
| Credential dumping | T1003.001 | ✅ Yes | Sysmon Event ID 10 — lsass access |
| Valid account abuse | T1078 | ✅ Yes | Failed then successful auth pattern |
| Defense evasion | T1562 | ⚠️ Partial | Service stop detected, process kill missed |
| Lateral movement | T1021 | 🔄 In progress | SMB traffic analysis via Suricata |

## Setup guides

- [ELK Stack installation](setup/elk-setup.md)
- [Suricata IDS configuration](setup/suricata-setup.md)
- [Filebeat + Winlogbeat config](setup/filebeat-config.md)
- [Atomic Red Team setup](setup/atomic-redteam.md)

## Detection notes

See [detections/detections.md](detections/detections.md) for what each
simulated attack looks like in Kibana — queries, screenshots, and
analyst notes.

## Infrastructure

- ELK server: Oracle Cloud Always Free VM (Ubuntu 22.04, 4 OCPUs, 24GB RAM)
- Target: Windows Server 2022 Evaluation (free 180-day license)
- Cost: $0/month
