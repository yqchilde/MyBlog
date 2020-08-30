# php利用foreach给数组指定键值添加内容

<!--more-->

### 栗子如下

```php
$jsonArr = array();
foreach ($res as $value) {
	array_push($jsonArr, array('classId' => $value['classid'], 'className' => $value['classname']));
}
$jsonArr = json_encode($jsonArr, true);
exit(print_r($jsonArr));
```
