
![alt text](image.jpg)


Udemy Course >> Cloud Monitoring with Grafana Dashboard and Prometheus Part1

How to Monitor Servers VPS Cloud with Grafana Dashboard and Prometheus - Network/System Administration and Monitoring P1


**********

Tips/Tricks/Notes/Commands URL Link: https://github.com/nimaxnimax/Udemy_Monitoring_System_and_Network/tree/main/Cloud_Monitoring_Grafana_Dashboard_Prometheus/Part1

Instructor & Courses >> https://www.udemy.com/user/adrian-fischer-infotech/


**********

How to Install and Configure - Ubuntu 20 and 22

Prerequisites

Following are system requirements on each node
- Minimal install Ubuntu 20 or 22
- Minimum 1GB RAM or more
- Minimum 1 CPU core / or 1 vCPU
- 20 GB free disk space
- Sudo user with admin rights
- Internet connectivity on each node


**********

Ubuntu 20 and 22 - Grafana Installation and Config >>>

```bash
sudo apt update -y
sudo apt install -y software-properties-common
sudo apt install ca-certificates -y
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 963FA27710458545
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo sed -i 's/deb https:\/\/packages.grafana.com\/oss\/deb stable main/deb [arch=amd64] https:\/\/packages.grafana.com\/oss\/deb stable main/' /etc/apt/sources.list
sudo apt update -y
sudo apt install grafana -y
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
sudo ufw allow 3000/tcp
```

Any problem with gpg >>

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
gpg --keyserver keyserver.ubuntu.com --recv-keys 963FA27710458545
gpg --export --armor 963FA27710458545 | sudo apt-key add -
```

To change port number >>

```bash
sudo systemctl stop grafana-server
sudo vi /etc/grafana/grafana.ini
```

```bash
[server]
http_port = 3000
```

```bash
sudo systemctl restart grafana-server
```

Dashboards >> 
https://grafana.com/grafana/dashboards/


**********

Ubuntu 20 and 22 - Prometheus Installation and Config >>>

Download Link >> 
https://prometheus.io/download/#prometheus


```bash
sudo apt update -y
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar vxf prometheus*.tar.gz
cd prometheus*/
sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
sudo cat /etc/prometheus/prometheus.yml
cd ..
sudo vi /etc/systemd/system/prometheus.service
```

```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus
sudo ufw allow 9090/tcp
cat /etc/prometheus/prometheus.yml
vi /etc/prometheus/prometheus.yml
```

```bash
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      # - targets: ["localhost:9090"]
      - targets: ["127.0.0.1:9100"]
```

```bash
sudo systemctl restart prometheus
```
How to check?! >> http://localhost:9090 or http://SERVER_IP_ADDRESS:9090

Prometheus Commands to Test

```bash
up
process_cpu_seconds_total
process_virtual_memory_bytes
node_network_transmit_queue_length
node_network_transmit_errs_total
```

```bash
cat /etc/prometheus/prometheus.yml
sudo vi /etc/prometheus/prometheus.yml
```

```bash
    static_configs:
            - targets: ["1.220.204.223:9100","1.233.162.12:9100"]

```bash
sudo systemctl restart prometheus
```


**********

Ubuntu 20 and 22 - Node Exporter Installation and Config >>>

Latest Version >> 
https://prometheus.io/download/#node_exporter

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar xvf node_exporter-1.6.1.linux-amd64.tar.gz
cd node_exporter-1.6.1.linux-amd64
sudo cp node_exporter /usr/local/bin
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
ls -ltr /usr/local/bin/node_exporter
cd ..
ls -anp
pwd 
sudo vi /etc/systemd/system/node_exporter.service
```

```bash
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

```bash
sudo cat /etc/systemd/system/node_exporter.service
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
sudo systemctl status node_exporter
sudo ufw allow 9100
sudo iptables -I INPUT -p tcp -m tcp --dport 9100 -j ACCEPT
```


**********

Dashboards >> 

https://grafana.com/grafana/dashboards/

https://grafana.com/grafana/dashboards/?search=prom

https://grafana.com/grafana/dashboards/1860-node-exporter-full/

https://grafana.com/grafana/dashboards/15172-node-exporter-for-prometheus-dashboard-based-on-11074/


