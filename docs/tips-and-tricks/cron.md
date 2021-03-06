# Working with CRON

Use `crontab.guru` to check if your cron expression does what you think it should: [crontab.guru][1].

Open crontab for current user:

```bash
crontab -e
```

List cron jobs for current user:

```bash
crontab -l
```

Open and edit crontab as different user (e.g. `www-data`):

```bash
sudo -u www-data crontab -e
```

List cron jobs for a specific user:

```bash
sudo -u www-data crontab -l
```

Delete user's jobs:

```bash
sudo -u www-data crontab -r
```

[1]: https://crontab.guru/
