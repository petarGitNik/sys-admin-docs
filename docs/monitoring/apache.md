# Apache Exporter

The official `apache_exporter` can be found [here][1].

Create `apache_exporter` user, then grab the binaries from the official repo, untar them, and copy the utitlity to `/usr/local/bin` (and don't forget to assign ownership):

```bash
sudo adduser --no-create-home --disabled-login --shell /bin/false --gecos "Apache exporter user" apache_exporter

wget https://github.com/Lusitaniae/apache_exporter/releases/download/v0.5.0/apache_exporter-0.5.0.linux-amd64.tar.gz
tar xvzf apache_exporter-0.5.0.linux-amd64.tar.gz

sudo cp apache_exporter-0.5.0.linux-amd64/apache_exporter /usr/local/bin/
sudo chown apache_exporter:apache_exporter /usr/local/bin/apache_exporter
```

Apache exporter uses [`mod_status`][2]. [Check][3][^1][^2] if the `mod_status` is enabled using:

```bash
apache2ctl -M | grep status
```

Do not forget to add systemd unit and do daemon-reload.

!!! note "Not related to production use"
    Note that `-scrape_uri` flag has to use HTTP not HTTPS. This might cause trouble such as `apache_up = 0` despite apache being on-line. Port 443 could refuse the connection. The alternative is to use HTTP with authentication as the official exporter documentation suggests: `http://user:password@localhost/server-status?auto`.

## Setting `systemd` unit files

!!! warning "Related to production use"
    In production use you will use the full URL in `-scrape_uri` flag.

```bash
sudo vi /etc/systemd/system/apache_exporter.service
```

```ini
[Unit]
Description=Prometheus Apache2 Exporter
Wants=network-online.target
After=network.target

[Service]
User=apache_exporter
Group=apache_exporter
Type=simple
ExecStart=/usr/local/bin/apache_exporter \
    -scrape_uri https://example.com/server-status/?auto \
    -telemetry.address "example.com:9117"
Restart=always

[Install]
WantedBy=multi-user.target
```

And then enable the daemon:

```bash
sudo systemctl daemon-reload
sudo systemctl enable apache_exporter
sudo systemctl start apache_exporter
```

[^1]: <https://web.archive.org/web/20180403092855/https://support.plesk.com/hc/en-us/articles/213922425-How-to-enable-Apache-mod-status>
[^2]: <http://archive.is/YfzUp>

[1]: https://github.com/Lusitaniae/apache_exporter
[2]: https://httpd.apache.org/docs/2.4/mod/mod_status.html
[3]: https://support.plesk.com/hc/en-us/articles/213922425-How-to-enable-Apache-mod-status
