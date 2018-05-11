# PostgreSQL

Switch to `postgres` user:

```bash
sudo su - postgres
```

Connect to a database:

```bash
psql -d postgres
```

Set current OS user to be also user in postgres, and give it superuser privileges:

```bash
sudo -u postgres createuser $USER
sudo -u postgres psql -c "alter user $USER with superuser" postgres
```

## Commands

| Commands | Meaning |
| -------: | :------ |
| \du | List all users |
| \l | Show all databases |
| \l+ | Show all databases, and display their size also |
| \c DBNAME | Connect to database |
| \dt | Show all tables |
| \d+ TABLE_NAME | Show table schema |
| \? | Help |
| \q | Quit |
