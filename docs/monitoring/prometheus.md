# Prometheus

## Installation using binary

First make a user that will own the application:

```bash
sudo adduser --no-create-home --disabled-login --shell /bin/false --gecos "Prometheus Monitoring User" prometheus
```

Then, make directories and dummy configuration files that you'll need for Prometheus.

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo touch /etc/prometheus/prometheus.yml
sudo touch /etc/prometheus/prometheus.rules.yml
```

Be sure that owner of the directories and files is `prometheus` user:

```bash
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

Next you need to download and copy all the utilities to their respective directories:

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.1.0/prometheus-2.1.1.linux-amd64.tar.gz
tar xvzf prometheus-2.1.1.linux-amd64.tar.gz

sudo cp prometheus-2.1.1.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.1.1.linux-amd64/promtool /usr/local/bin/
sudo cp -r prometheus-2.1.1.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-2.1.1.linux-amd64/console_libraries /etc/prometheus
```

Now you need to assign the proper ownership to those tools:

```bash
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

## Configuration files

You'll need two configuration files. One for general Prometheus settings, and one that will contain scraping rules.

```bash
sudo vi /etc/prometheus/prometheus.yml
```

Paste this into the configuration file:

```yaml
global:
  scrape_interval: 15s

rule_files:
  - 'prometheus.rules.yml'

scrape_configs:

  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'blackbox_exporter'
    scrape_interval: 5s
    static_configs:
      - targets:
        - localhost:9115
        labels:
          group: 'production'

  - job_name: 'grafana'
    scrape_interval: 5s
    static_configs:
      - targets:
        - localhost:3000

alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost:9093
```

And finally, we will add an example rule to the rules file:

```bash
sudo vi /etc/prometheus/prometheus.rules.yml
```

```yaml
groups:
  - name: example_alert
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 5m
        labels:
          severity: page
        annotations:
          summary: "Instance {{ $labels.instance }} down"
          description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."
```

## Adding Prometheus as `systemd` unit

Open a new text file as shown below:

```bash
sudo vi /etc/systemd/system/prometheus.service
```

And paste the following configuration:

```ini
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

That's it. Enable the daemon:

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

And don't forget to clean things up:

```bash
rm prometheus-2.1.1.linux-amd64.tar.gz
rm -rf prometheus-2.1.1.linux-amd64
```
