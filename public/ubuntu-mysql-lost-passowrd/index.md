# Ubuntu安装MySQL后没有初始密码之解决方案


<!--more-->

在Ubuntu系统中安装了MySQL之后，根据操作看是有一个交互需要设置密码的，但是并没有出现设置密码的环节，压根登录不了，尝试查看过 `var/log/mysql/error.log`，看到的内容显示密码为空，还是没有用。

解决方案如下：

1. 在终端输入 `sudo cat/etc/mysql/debian.cnf`，如下图

![image-20201110235553156](https://pic.yqqy.top/blog/20201110235555.png?imageMogr2/format/webp/interlace/1 "debian.cnf")

2. 用上面的`user` 和 `password` 登录mysql，如图

![image-20201110235713406](https://pic.yqqy.top/blog/20201110235714.png?imageMogr2/format/webp/interlace/1 "登录成功")

3. 登录进来之后，就可以修改mysql的密码了，使用到的命令如下，也可以参考之前的 [MySQL5.7修改root密码](https://yqqy.top/mysql5.7-update-password/)

```mysql
1. 选择mysql库
use mysql

2. 更新密码
update mysql.user set authentication_string=password('这里设置新密码') where user='root' and Host ='localhost'; 

3. 更新plugin
update user set plugin="mysql_native_password";

4. 刷新
flush privileges;

5. 退出
quit;
```

操作如图所示：

![image-20201111080453889](https://pic.yqqy.top/blog/20201111080455.png?imageMogr2/format/webp/interlace/1 "修改密码")

4. 重启MySQL服务

```mysql
sudo service mysql restart
```

以上就是全部，再次登录使用root和设置的密码就可以登录

## 参考文章

* [在Ubuntu 18.04 下安装mysql，没有初始密码，重设root密码](https://www.cnblogs.com/williamjie/p/11126486.html)
