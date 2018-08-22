### PHP编译安装增加fileinfo扩展

#### 问题

在使用图片上传时，报这个错 ```The fileinfo PHP extension is not installed.```，是fileinfo扩展没有开启或者安装

#### 解决思路

##### 1、先判断是否安装了fileinfo扩展

```php -model | grep fileinfo```

如果没有结果的话，说明就是没有安装。

##### 2、进入php安装目录查看有没有fileinfo的目录

```cd /alidata/server/php-5.6.9/ext/fileinfo``` 

##### 3、找到phpize的目录，我的是在 ```/alidata/server/php/bin/phpize```

##### 4、还是在第二部的fileinfo的安装目录下，执行如下命令，看下结果。

```/alidata/server/php/bin/phpize```

结果：

```
Configuring for:
PHP Api Version:         20131106
Zend Module Api No:      20131226
Zend Extension Api No:   220131226
```

##### 5、执行安装命令

```./configure --with-php-config=/alidata/server/php/bin/php-config```

```make && make install```

返回以下结果，说明安装成功了:

```
Build complete.
Don't forget to run 'make test'.
```

##### 6、找到php.ini的位置，编辑在末尾加上```extension=fileinfo.so```，然后保存。重启php-fpm即可。



注意：你的各种目录可能和我的不一样，本文仅供参考。