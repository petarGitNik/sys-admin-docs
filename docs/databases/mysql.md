# MySQL

Login to the mysql shell:

```bash
mysql -u <user> -p
```

## Commands

| Commands | Meaning |
| -------: | :------ |
| SHOW DATABASES; | Self explanatory |
| USE db_name | Self explanatory |
| SELECT DATABASE(); | Check which database is currently selected |
| SHOW TABLES; | Self explanatory |
| DROP DATABASE db_name | Self explanatory |

## Backup and restoration

Basic command to make a backup is:

```bash
mysqldump <DB_NAME> > filename.sql
```

You can restore database with:

```bash
mysql <DB_NAME> < filename.sql
```

This implies that you have `.my.cnf` file in your home directory. Alternatively, you can restore as:

```bash
mysql -u <DB_USER> -p <DB_NAME> < filename.sql
```

You may need to drop and recreate database before you try to restore. Inspect SQL file to see if it has `DROP IF EXISTS` statements.

For more information check out [this][1][^1][^2] link.

## Get database size

```mysql
SELECT table_schema AS "DB Name",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) "DB Size in MB"
FROM information_schema.tables
GROUP BY table_schema;
```

[^1]: <https://web.archive.org/web/20180419141817/http://webcheatsheet.com/sql/mysql_backup_restore.php>
[^2]: <http://archive.is/Ehepe>

[1]: http://webcheatsheet.com/sql/mysql_backup_restore.php
