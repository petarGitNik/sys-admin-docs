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

You may need to drop and recreate database before you try to restore it. Inspect SQL file to see if it has `DROP IF EXISTS` statements.

For more information check out [this][1][^1][^2] link.

## Get database size

```mysql
SELECT table_schema AS "DB Name",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) "DB Size in MB"
FROM information_schema.tables
GROUP BY table_schema;
```

## Check character encoding of a database

Sometimes you need to check whether the database/table is encoded with `UTF-8`. This information is of value if you need to backup database with funky letters. Use this to find out character encoding of a table[^3][^4][^5]:

```mysql
SELECT
c.character_set_name
FROM information_schema.tables AS t,
     information_schema.collation_character_set_applicability AS c
WHERE c.collation_name = t.table_collation
AND t.table_schema = "your_db"
AND t.table_name = "table_name";
```

[^1]: <https://web.archive.org/web/20180419141817/http://webcheatsheet.com/sql/mysql_backup_restore.php>
[^2]: <http://archive.is/Ehepe>
[^3]: <https://stackoverflow.com/questions/6103595/how-to-check-if-mysql-table-is-utf-8-and-has-storageengine-innodb>
[^4]: <https://web.archive.org/web/20180522095730/https://stackoverflow.com/questions/6103595/how-to-check-if-mysql-table-is-utf-8-and-has-storageengine-innodb>
[^5]: <http://archive.is/wcQIt>

[1]: http://webcheatsheet.com/sql/mysql_backup_restore.php
