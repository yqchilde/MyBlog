# MySQL5.7修改root密码

<!--more-->

## 在网上看到这行命令并使用
{{indent}}`select host,user,password from mysql.user;`

{{indent}}然后报错了

{{indent}}`ERROR 1054 (42S22): Unknown column 'password' in 'field list'`

---

{{indent}}错误的原因是5.7版本下的mysql数据库下已经没有password这个字段了，password字段改成了authentication_string

{{indent}}正确操作如下：

```sql
mysql> use mysql
Database changed
mysql> select User from user;
+-----------+
| User      |
+-----------+
| mysql.sys |
| root      |
+-----------+
2 rows in set (0.00 sec)

mysql> update mysql.user set authentication_string=password('root') where user='root';
Query OK, 0 rows affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 0  Warnings: 1

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> quit
Bye
```




