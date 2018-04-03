# Node Exporter

## Installation using binary

Make a user that will own the installation:

```bash
sudo adduser --no-create-home --disabled-login --shell /bin/false --gecos "Node Exporter User" node_exporter
```

Download binaries, copy utility to `/usr/local/bin` and assign proper ownership:

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v0.15.2/node_exporter-0.15.2.linux-amd64.tar.gz
tar xvzf node_exporter-0.15.2.linux-amd64.tar.gz

sudo cp node_exporter-0.15.2.linux-amd64/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

## Adding `node_exporter` as `systemd` unit

Open a new text file as shown below:

```bash
sudo vi /etc/systemd/system/node_exporter.service
```

And paste the following configuration:

```ini
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

That's it. Enable the daemon:

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

And don't forget to clean things up:

```bash
rm node_exporter-0.15.2.linux-amd64.tar.gz
rm -rf node_exporter-0.15.2.linux-amd64
```
