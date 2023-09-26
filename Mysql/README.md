## Mysql

### 集群
[主从配置](./主从配置.md)

### 浏览
```sql
SHOW DATABASES;
SHOW TABLES;
SHOW FIELDS FROM table / DESCRIBE table;
SHOW CREATE TABLE table;
SHOW PROCESSLIST;
KILL process_number;
```

### 条件/Conditions
```sql
field1 = value1
field1 <> value1
field1 LIKE 'value _ %'
field1 IS NULL
field1 IS NOT NULL
field1 IS IN (value1, value2)
field1 IS NOT IN (value1, value2)
condition1 AND condition2
condition1 OR condition2
```

### 删除/DELETE
```sql
//快速清空表并复位自增ID
TRUNCATE TABLE table1;

//清空表table2并复位自增ID
DELETE FROM table2;
ALTER TABLE table2 AUTO_INCREMENT=1;
```


### 备份与恢复
```sql
mysqldump -u Username -p dbNameYouWant > databasename_backup.sql

mysql -u Username -p dbNameYouWant < databasename_backup.sql;
```




