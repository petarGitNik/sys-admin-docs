# `mysqld_exporter`

## Should I install from source or use a binary?

You have two options. One option is to setup `go` environment and to compile the binary yourself. Second option is to download the already compiled binary. In any case, you will have to visit the official [repo][1]. **I recommend you to use binary**.

## Making a binary from source

In case you want to compile it yourself, you will have to set up the `go` language environment. To set it up, follow [this][2][^1][^2] tutorial (skip to the section named: _Set up golang environment_).

When you are done with it, you can compile the binary like this:

```bash
go get github.com/prometheus/mysqld_exporter
```

The binary is now located in your `go` directory, for example `$HOME/go/bin/mysqld_exporter`.

## Downloading a binary

If you just want to download binary, go to the official repo, and locate the version you want under the [releases][7] tab. Then all you need to do is to untar the archive:

```bash
wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.10.0/mysqld_exporter-0.10.0.linux-amd64.tar.gz
tar xvzf mysqld_exporter-0.10.0.linux-amd64.tar.gz
```

The binary is located in the extracted directory, and it is named `mysqld_exporter`.

## Installation

First, create a separate user that will be used to run this application:

```bash
sudo adduser --no-create-home --disabled-login --shell /bin/false --gecos "MySQL exporter user" mysql_exporter
```

Next, we will create necessary directories and dummy configuration files. Also, it is necessary to give ownership of these directories and files to the newly created `mysql_exporter` user.

```bash
sudo mkdir /etc/mysql_exporter
sudo touch /etc/mysql_exporter/.my.cnf

sudo chown -R mysql_exporter:mysql_exporter /etc/mysql_exporter
sudo chmod 600 /etc/mysql_exporter/.my.cnf
```

[.my.cnf][8][^3][^4] file will contain user name and password configuration for you MySQL database. For safety reasons we make this file readable only to the owner.

Next, copy your binary to appropriate directory in filesystem hierarchy. For this part of guide, I will assume that you have download binary.

```bash
sudo cp mysqld_exporter-0.10.0.linux-amd64/mysqld_exporter /usr/local/bin/
sudo chown mysql_exporter:mysql_exporter /usr/local/bin/mysqld_exporter
```

Now, we have to configure three files.

## Configure `.my.cnf`

Open the file:

```bash
sudo vi /etc/mysql_exporter/.my.cnf
```

Then paste this, and replace with your user name and password.

```ini
[client]
user=REPLACE_WITH_YOUR_USER
password=REPLACE_WITH_YOUR_PASSWORD
```

## Add MySQL Exporter as `systemd` unit

Open with editor a new `systemd` service as:

```bash
sudo vi /etc/systemd/system/mysql_exporter.service
```

Copy and paste the following configuration:

```ini
[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
User=mysql_exporter
Group=mysql_exporter
Type=simple
ExecStart=/usr/local/bin/mysqld_exporter \
    --config.my-cnf="/etc/mysql_exporter/.my.cnf"
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable the new service on startup, reload daemons and start the exporter service.

```bash
sudo systemctl daemon-reload
sudo systemctl enable mysql_exporter
sudo systemctl start mysql_exporter
```

## Configure `prometheus.yml`

You have to notify Prometheus to scrape the newly installed exporter. Paste this under `scrape_configs` tag, and modify the location of the server according to your needs:

```yaml
scrape_configs:
  - job_name: 'mysql_exporter'
    scrape_interval: 5s
    static_configs:
      - targets:
        - localhost:9104
```

Do not forget to restart Prometheus daemon.

[^1]: <https://web.archive.org/web/20180402122352/https://machineperson.github.io/monitoring/2016/01/04/exporting-apache-metrics-to-prometheus.html>
[^2]: <http://archive.is/MNes3>
[^3]: <https://web.archive.org/web/20180402124930/https://easyengine.io/tutorials/mysql/mycnf-preference/>
[^4]: <http://archive.is/OXvlS>

[1]: https://github.com/prometheus/mysqld_exporter
[2]: https://machineperson.github.io/monitoring/2016/01/04/exporting-apache-metrics-to-prometheus.html
[7]: https://github.com/prometheus/mysqld_exporter/releases
[8]: https://easyengine.io/tutorials/mysql/mycnf-preference/
