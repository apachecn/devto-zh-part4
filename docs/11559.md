# MySQL 基础教程

> 原文：<https://dev.to/unameyang/a-basic-mysql-tutorial-1j6n>

> MySQL is a relational database management system, developed by MySQL AB company in Sweden, and currently belongs to the products of Oracle. MySQL is one of the most popular relational database management systems. In WEB application, MySQL is the best RDBMS (Relational Database Management System) application software.

## 1\. How to install MySQL

Ubuntu:

```
sudo apt install mysql-server 
```

Centos:

```
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm
sudo yum install mysql-server 
```

## 2\. How to connect to Mysql

Command line to connect to the database by using the following command after successfully installing MySQL:

```
mysql -u root -p 
```

After entering the password, you can start operating the database.
There are two points to note:

1.  All MySQL commands end with a semicolon, and the command line does not execute the input instruction.
2.  Generally, the keywords of MySQL commands are capitalized to better distinguish and understand SQL commands, but the command line is not case sensitive.

## 2\. Database creation and deletion

All databases listed for users:

```
SHOW DATABASES; 
```

The command line will print something like:

```
 mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.01 sec) 
```

Create database:

```
 CREATE DATABASE database name; 
```

It can be seen that the database named `events` was created later:

```
 mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| events             |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec) 
```

Delete database:

```
 DROP DATABASE database name; 
```

## 3\. Creation and operation of tables

First, you need to switch to a specified database, such as [T0】: 【T1]

```
 USE events; 
```

Like listing all available databases, use `SHOW` to list all table names in the database:

```
SHOW tables; 
```

The operation of the table is similar to the SQL of other relational databases. Create a table named `potluck`:

```
CREATE TABLE potluck (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, 
name VARCHAR(20),
food VARCHAR(30),
confirmed CHAR(1),
signup_date DATE); 
```

At this time, execute the `SHOW tables` command again to view the output result:

```
 mysql> SHOW TABLES;
+------------------+
| Tables_in_events |
+------------------+
| potluck          |
+------------------+
1 row in set (0.01 sec) 
```

Use the `DESCRIBE` command to view the table structure:

```
 mysql>DESCRIBE potluck;
+-------------+-------------+------+-----+---------+----------------+
| Field       | Type        | Null | Key | Default | Extra          |
+-------------+-------------+------+-----+---------+----------------+
| id          | int(11)     | NO   | PRI | NULL    | auto_increment |
| name        | varchar(20) | YES  |     | NULL    |                |
| food        | varchar(30) | YES  |     | NULL    |                |
| confirmed   | char(1)     | YES  |     | NULL    |                |
| signup_date | date        | YES  |     | NULL    |                |
+-------------+-------------+------+-----+---------+----------------+
5 rows in set (0.01 sec) 
```

## 4\. Add, delete and check data.

Example of data insertion:

```
INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "John", "Casserole","Y", '2012-04-11'); 
```

There will be feedback after successful execution

```
 Query OK, 1 row affected (0.00 sec) 
```

Insert more data again:

```
INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "Sandy", "Key Lime Tarts","N", '2012-04-14');
INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "Tom", "BBQ","Y", '2012-04-18');
INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "Tina", "Salad","Y", '2012-04-10'); 
```

`SELECT` is used to query data:

```
mysql> SELECT * FROM potluck;
+----+-------+----------------+-----------+-------------+
| id | name  | food           | confirmed | signup_date |
+----+-------+----------------+-----------+-------------+
|  1 | John  | Casserole      | Y         | 2012-04-11  |
|  2 | Sandy | Key Lime Tarts | N         | 2012-04-14  |
|  3 | Tom   | BBQ            | Y         | 2012-04-18  |
|  4 | Tina  | Salad          | Y         | 2012-04-10  |
+----+-------+----------------+-----------+-------------+
4 rows in set (0.00 sec) 
```

`UPDATE` is used to query data:

```
UPDATE `potluck`
SET
`confirmed` = 'Y'
WHERE `potluck`.`name` ='Sandy'; 
```

Use the following command to delete a row of records:

```
DELETE from [table name] where [column name]=[field text]; 
```

```
mysql> DELETE from potluck  where name='Sandy';
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM potluck;
+----+------+-----------+-----------+-------------+
| id | name | food      | confirmed | signup_date |
+----+------+-----------+-----------+-------------+
|  1 | John | Casserole | Y         | 2012-04-11  |
|  3 | Tom  | BBQ       | Y         | 2012-04-18  |
|  4 | Tina | Salad     | Y         | 2012-04-10  |
+----+------+-----------+-----------+-------------+
3 rows in set (0.00 sec) 
```