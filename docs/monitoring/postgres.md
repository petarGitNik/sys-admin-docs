# `postgres_exporter`

Go to release tab on [official github repository][1] for this exporter and wget latest binaries.

```bash
wget https://github.com/wrouesnel/postgres_exporter/releases/download/v0.4.5/postgres_exporter_v0.4.5_linux-amd64.tar.gz
tar xvzf postgres_exporter_v0.4.5_linux-amd64.tar.gz
```

Copy the binaries and assign proper ownership:

```bash
sudo cp postgres_exporter_v0.4.5_linux-amd64/postgres_exporter  /usr/local/bin/
sudo chown postgres:postgres /usr/local/bin/postgres_exporter
```

All you need to do now is set `systemd` unit file.

!!! info
    This exporter will be owned by `postgres` user. That is the default user of PostgreSQL rdms, so you do not have to set a new user. In case you do want to set up new user, follow the instruction below, and the instructions in the official repo.

## In case you need to create a user

To set the current user as postgres superuser do the following:

```bash
sudo -u postgres createuser $USER
sudo -u postgres psql -c "alter user $USER with superuser" postgres
```

Try to log in by using `psql -U <user> -W`. If you receive `psql: FATAL: database “<user>” does not exist` error message, execute `createdb`.

## Set `systemd` unit

```bash
sudo vi /etc/systemd/system/postgres_exporter.service
```

```ini
[Unit]
Description=Prometheus PostgreSQL Exporter
After=network.target

[Service]
User=postgres
Group=postgres
Type=simple
Environment="DATA_SOURCE_NAME=user=postgres host=/var/run/postgresql/ sslmode=disable"
ExecStart=/usr/local/bin/postgres_exporter
Restart=always

[Install]
WantedBy=multi-user.target
```

And enable the daemon:

```bash
sudo systemctl daemon-reload
sudo systemctl enable postgres_exporter
sudo systemctl start postgres_exporter
```

[1]: https://github.com/wrouesnel/postgres_exporter
