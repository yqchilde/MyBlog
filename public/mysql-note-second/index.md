# MySQL复习笔记二

<!--more-->

MySQL复习笔记一： [http://www.yqqy.top/notes/311](http://www.yqqy.top/notes/311)

### 创建存储过程

```sql
DELIMITER //
CREATE PROCEDURE p_stu1(IN
	student_name char(10),IN
	course_name char(20))
BEGIN
	SELECT Sname, Cname, Grade
	FROM xs s JOIN xk ON s.sno = xk.sno
	INNER JOIN kc c ON c.cno = xk.cno
	WHERE sname = student_name
	AND cname = course_name;
END //
DELIMITER;
```

![mark](https://pic.yqqy.top/blog/20200111/uNYFhQTsm6j5.png)

### 调用存储过程

`call p_stu1("zhangsan", "mysql");`

![mark](https://pic.yqqy.top/blog/20200111/jNIFq3jhSLtX.png)

### 创建数据库用户

`CREATE USER 'yqchilde' @ '%' IDENTFIND BY PASSWORD 'root'`
