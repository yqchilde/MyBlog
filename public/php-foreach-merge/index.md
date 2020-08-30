# php在foreach里面合并数组

<!--more-->

### 用到的函数 `array_merge()` `call_user_func_array`

如果直接两个数组合并，那十分简单，如下

```php
$a1=array("red","green");
$a2=array("blue","yellow");
print_r(array_merge($a1,$a2));
// 结果如下
// Array ( [0] => red [1] => green [2] => blue [3] => yellow )
```

### 如何在foreach里面合并相同的 “内容” 为数组

```php
foreach ($array as $k => $v) {
	$array[] = $db->where('classid = ?', array($v['cid']))->fetchAll();
}
$result = call_user_func_array('array_merge', $array);
print_r($result);
// 这样就是有几条就连接起来了
```
