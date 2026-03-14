# Suricata IDS Setup

## Install
```bash
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt install suricata -y
sudo suricata-update
sudo systemctl enable --now suricata
```

## Configure interface

Find your network interface:
```bash
ip a
```

Edit /etc/suricata/suricata.yaml:
```yaml
af-packet:
  - interface: ens3   # replace with your interface name

default-log-dir: /var/log/suricata/
```

## Ship Suricata logs to ELK via Filebeat

Edit /etc/filebeat/filebeat.yml:
```yaml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/suricata/eve.json
    json.keys_under_root: true

output.elasticsearch:
  hosts: ["localhost:9200"]
```

## Verify Suricata is detecting
```bash
curl http://testmyids.com
tail -f /var/log/suricata/fast.log
```

## Notes
- eve.json is the main log file — ships cleanly to ELK
- Update rules weekly: sudo suricata-update
- ET Open ruleset is free and covers most common attack signatures
