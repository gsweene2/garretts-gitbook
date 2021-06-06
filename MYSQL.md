# MySql

<!-- toc -->

## Local Installation, Start, Connect

Homebrew Install & Upgrade

```
brew install mysql
brew upgrade mysql
```

Start MySql
```
brew services start mysql
```

Connect with username `root`
```
mysql -u root
```

## Show Databases (Schemas)

```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.05 sec)
```

OR, `SHOW SCHEMAS;`

## Create Database

```
mysql> CREATE DATABASE IF NOT EXISTS garrett;
Query OK, 1 row affected (0.00 sec)
```

## Use Database

```
mysql> USE garrett;
Database changed
```

## Create Table

```
create table round_tbl(
   round_id INT NOT NULL AUTO_INCREMENT,
   score INT NOT NULL,
   course VARCHAR(100) NOT NULL,
   weather VARCHAR(40) NOT NULL,
   round_date DATE,
   PRIMARY KEY ( round_id )
);
```

## Insert Row

### Generic
```
INSERT INTO <table>(<col_name>,<col_name>,<col_name>) VALUES (<col_value>,<col_value>,<col_value>);
```

### Example
```
mysql> INSERT INTO round_tbl(score,course,weather,round_date) VALUES (78,"Oak Hill","Windy",NOW());
Query OK, 1 row affected, 1 warning (0.03 sec)
```

## Query

```
mysql> SELECT * FROM round_tbl;
+----------+-------+----------+---------+------------+
| round_id | score | course   | weather | round_date |
+----------+-------+----------+---------+------------+
|        1 |    78 | Oak Hill | Windy   | 2021-05-30 |
+----------+-------+----------+---------+------------+
1 row in set (0.00 sec)
```
