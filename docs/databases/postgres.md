# PostgreSQL

Switch to `postgres` user:

```bash
sudo su - postgres
```

Connect to a database:

```bash
psql -d postgres
```

## Commands

| Commands | Meaning |
| -------: | :------ |
| \du | List all users |
| \l | Show all databases |
| \c DBNAME | Connect to database |
| \dt | Show all tables |
| \d+ TABLE_NAME | Show table schema |
| \q | Quit |
