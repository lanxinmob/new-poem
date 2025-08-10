---
author: 'lanxinmob' 
postSlug: 'sql sd table'
title: 'sql and tables and python'
tags:
  - '笔记'
  - 'database'
  - 'sql'
description: ''
pubDatetime: 2025-08-10T09:00:00Z
---
## Create Table
```SQL
CREATE TABLE numbers (n, note);
```
- 最基础的建表命令,创建了一个名为 `numbers` 的新表，包含两个列：一个名为 `n`，另一个名为 `note`。


```SQL
CREATE TABLE numbers (n UNIQUE, note);
```
- 这行命令在创建 `n` 列时，增加了一个 `UNIQUE` 约束，意味着n的值是不能重复的。

```SQL
CREATE TABLE numbers (n, note DEFAULT "No comment");
```
- 这行命令在创建 `note` 列时，增加了一个 `DEFAULT` ,意味着在插入新数据时，如果没有给 note 列提供具体的值，数据库会自动将 `"No comment"` 这个字符串作为该列的值。
## Drop Table
```SQL
drop table if exists table_name;
```
## Modifing Table
### INSERT
```SQL
CREATE TABLE primes (n UNIQUE, prime DEFAULT 1);
INSERT INTO primes VALUES (2,1),(3,1);

INSERT INTO primes(n) VALUES (4),(5),(6),(7);
```
### UPDATE
```SQL
UPDATE primes SET prime = 0 WHERE n>2 AND n%2=0; 
```
### DELETE
```SQL
DELETE FROM primes WHERE prime=0;
```

### In Python
```python
import sqlite3

db = sqlite3.Connection("n.db")
db.execute("CREATE TABLE nums AS SELECT 2 UNION SELECT 3;")
db.execute("INSERT INTO nums VALUES (?), (?), (?);", range(4, 7))
print(db.execute("SELECT * FROM nums;").fetchall())
#[(2,), (3,), (4,), (5,), (6,)]
db.commit()
```
- `.fetchall()` 方法会获取查询结果的所有行，并将它们作为一个列表返回。列表中的每一行是一个元组。

```python
import mysql.connector
from mysql.connector import errorcode

try:
    db = mysql.connector.connect(
        user="user",
        password="your_password",
        host="127.0.0.1",
        database="test_db" 
    )
except mysql.connector.Error as err:
    if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
        print("用户名或密码错误")
    elif err.errno == errorcode.ER_BAD_DB_ERROR:
        print("数据库不存在")
    else:
        print(err)
    exit()

cursor = db.cursor()

cursor.execute("DROP TABLE IF EXISTS nums;")
cursor.execute("CREATE TABLE nums AS SELECT 2 AS n UNION SELECT 3;")

data_to_insert = [(4,), (5,), (6,)]
cursor.executemany("INSERT INTO nums (n) VALUES (%s);", data_to_insert)
cursor.execute("SELECT * FROM nums;")
print(cursor.fetchall()) 

db.commit()   
cursor.close()
db.close()    
```
- 使用 `MySQL`需要连接到一个服务器，执行指令时先创建一个游标对象 `db.cursor()`，然后通过游标来执行所有SQL命令。
- 使用 `MySQL`需要先有一个数据库
```bash
sudo mysql
"""在Linux上使用 auth_socket 插件来验证 root 用户
mysql -u root -p 
"""使用这个需要先设置密码
```
- 进入mysql后创建用户设置密码授予权限
```SQL
CREATE USER 'user'@'localhost' IDENTIFIED BY 'passward';
GRANT ALL PRIVILEGES ON test_db.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
CREATE DATABASE my_db;
SHOW DATABASES;
USE my_db;
```