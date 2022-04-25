# 同一个表下update数据并检测是否重复

<!--more-->

### 要实现的效果

在表 `a` 的 第 `b` 条数据要 `update` 数据的时候，检查这个表中的所有数据中有没有与要更新的数据重复，如果有即不更新，没有即更新，起初是理解错了目的，以为是单一数据不能重复，设置了唯一索引 `unique` 后来和我说不是那样的效果，故改之~~~

![mark](https://pic.yqqy.top/blog/20200111/QhYwX5xHEos6.png)

比如直接写表名，就是这样的错误

![mark](https://pic.yqqy.top/blog/20200111/f0IOHzXgQGyL.png)

### 代码如下
```sql
UPDATE 表名
SET 字段名1 = '字段value1',
 字段名2 = '字段value2',
WHERE
	id = '索引'  # 这里是为了设置索引
AND (
	SELECT
		COUNT(*)
	FROM
		表名
	WHERE
		条件
) = 0 # 等于0 是为了反条件
```
