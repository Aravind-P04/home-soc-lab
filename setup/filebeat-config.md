# Filebeat Configuration — Shipping Logs to ELK

## What Filebeat does
Collects logs from endpoints and ships them to Elasticsearch.
Configured to collect Windows Event Logs, Suricata IDS alerts,
and system logs.

## Install Filebeat
```bash
sudo apt install filebeat -y
sudo systemctl enable filebeat
```

## Configure filebeat.yml

Edit /etc/filebeat/filebeat.yml:
```yaml
filebeat.inputs:
  # Suricata IDS logs
  - type: log
    enabled: true
    paths:
      - /var/log/suricata/eve.json
    json.keys_under_root: true
    json.add_error_key: true
    tags: ["suricata"]

  # System logs
  - type: log
    enabled: true
    paths:
      - /var/log/syslog
      - /var/log/auth.log
    tags: ["system"]

output.elasticsearch:
  hosts: ["localhost:9200"]
  index: "filebeat-%{+yyyy.MM.dd}"

setup.kibana:
  host: "localhost:5601"
```

## Apply configuration
```bash
sudo filebeat setup
sudo systemctl start filebeat
sudo systemctl status filebeat
```

## Verify logs are flowing
```bash
# Check Filebeat is connecting to Elasticsearch
curl -X GET "localhost:9200/_cat/indices?v" | grep filebeat

# Check for errors
sudo journalctl -u filebeat -f
```

## Notes
- eve.json is Suricata's JSON output — most useful for IDS alerts
- auth.log captures SSH logins and sudo commands — good for detecting
  unauthorized access attempts
- Restart Filebeat after any config change: sudo systemctl restart filebeat
