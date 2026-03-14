# ELK Stack Setup — Ubuntu 22.04

## Infrastructure
Oracle Cloud Always Free VM — 4 OCPUs, 24GB RAM, Ubuntu 22.04
Cost: $0/month permanently

## Install Elasticsearch
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update && sudo apt install elasticsearch -y
sudo systemctl enable --now elasticsearch
```

## Install Kibana
```bash
sudo apt install kibana -y
sudo systemctl enable --now kibana
```

Edit /etc/kibana/kibana.yml:
```
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
```

## Verify everything is running
```bash
sudo systemctl status elasticsearch
sudo systemctl status kibana
curl -X GET "localhost:9200"
```

## Notes
- Kibana accessible at http://your-vm-ip:5601
- Open port 5601 in Oracle Cloud security list
- Default Elasticsearch port 9200 — keep internal only
