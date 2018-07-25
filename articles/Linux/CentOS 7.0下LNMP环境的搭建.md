**配过多次LNMP，但一直没有留下操作手册，这次详细的记录下来，以免每次都做重复的劳动。**
***
###环境
* 服务器: aliyun服务器/1核CPU/1G内存。
* 系统: CentOS 7.0 64位

###系统准备工作
1. screen的安装，由于是远程安装，避免突然断线导致安装失败，所以这里使用screen，安装命令为: `yum install screen`。安装后，新建screen进程`screen -R XXX`，之后，都在这个进程中操作即可。

###安装开发包和安装软件版本
1. 安装开发包及依赖包： `yum --skip-broken install make apr* autoconf automake curl curl-devel gcc gcc-c++ gtk+-devel zlib-devel openssl openssl-devel pcre-devel gd kernel keyutils patch perl kernel-headers compat*  cpp glibc libgomp libstdc++-devel keyutils-libs-devel libsepol-devel libselinux-devel krb5-devel  libXpm* freetype freetype-devel freetype* fontconfig fontconfig-devel  libjpeg* libpng* php-common php-gd gettext gettext-devel ncurses* libtool* libxml2 libxml2-devel patch policycoreutils bison`
2. 安装的软件版本：
先进入下载包的存储目录：`cd ~/download`，然后执行包下载命令。
* nginx 1.9.2，包下载命令为：`wget http://nginx.org/download/nginx-1.9.2.tar.gz`
* php 5.6.10，包下载命令为：`wget -O php-5.6.10.tar.gz http://cn2.php.net/get/php-5.6.10.tar.gz/from/this/mirror`
* MariaDB 10.0.20，包下载命令为：`wget  ftp://mirrors.fe.up.pt/pub/mariadb/mariadb-10.0.20/source/mariadb-10.0.20.tar.gz` *(2015-06-20 16:46:52 官方的下载地址报404，所以我找到了这个源。建议还是使用官方下载地址)*

###nginx的安装
1. **设置www用户**
```
groupadd www
useradd -g www www
mkdir -p /wwwroot
chmod +w /wwwroot
chown www:www /wwwroot -R
```
2. **安装nginx**
```
#进入压缩包所在目录
cd ~/download
#解压
tar -zvxf nginx-1.9.2.tar.gz
#进入解压后的nginx目录
cd nginx-1.9.2
#编译安装，默认安装到/usr/local/nginx
./configure --user=www --group=www \
--with-http_ssl_module \
--with-http_stub_status_module \
--with-http_gzip_static_module \
--with-mail \
--with-mail_ssl_module
make & make install
```
3. **启动nginx**
```
cd /opt/nginx/sbin
./nginx
```
4. **测试nginx**
```
elinks http://127.0.0.1
#如果系统中没有elinks需要安装
yum install elinks* -y
```
**如果能看到"Welcome to nginx!"字样，说明就安装成功了。我的安装目录是:`/usr/local/nginx`,你可以自定义你自己的安装目录。**


###MariaDB的安装
1. 安装cmake
`yum install -y cmake`
2. 安装MariaDB
```
#添加MariaDB数据库使用的用户组mysql
groupadd mysql
#创建mysql使用的账户mysql并加入mysql组，不允许msyql用户直接登录系统
useradd -g mysql mysql -s /bin/false
#创建MariaDB存放数据库的目录
mkdir -p /data/mysql
#设置数据库目录权限
chown -R mysql:mysql /data/mysql
#创建MariaDB的安装目录
mkdir -p /usr/local/mysql
#进入MariaDB压缩包的存放目录
cd ~/download
#解压
tar zxvf mariadb-10.0.20.tar.gz
#进入解压后的文件目录
cd mariadb-10.0.20
#配置
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql  -DMYSQL_DATADIR=/data/mysql  -DSYSCONFDIR=/etc
#编译(如果报错，请看文章结尾的报错处理)
make
#安装
make install
```
3. 配置MariaDB
安装完毕如果没有报错，就是安装好了，下面进行配置。
```
cd /usr/local/mysql
#拷贝配置文件(如果/etc/my.cnf已经存在，直接覆盖即可)
cp ./support-files/my-huge.cnf /etc/my.cnf
#编辑配置文件,在`[mysqld]`部分增加MariaDB数据库路径
vim /etc/my.cnf
datadir = /data/mysql
#生成数据库的初始数据
./scripts/mysql_install_db --user-mysql
#把MariaDB加入系统自启动
cp ./support-files/mysql.server  /etc/rc.d/init.d/mysqld
#赋予执行权限
chmod 755 /etc/init.d/mysqld
#加入开机启动
chkconfig mysqld on
#编辑，设置MariaDB程序安装路径和数据库存放路径
vim /etc/rc.d/init.d/mysqld
basedir = /usr/local/mysql
datadir = /data/mysql
#启动MariaDB
service mysqld start
#把MariaDB服务加入系统环境变量，在最后添加如下新的一行
vim /etc/profile
export PATH=$PATH:/usr/local/mysql/bin
#把MariaDB的库文件链接到系统默认的位置，这样在编译类似PHP等软件时可以不用指定MariaDB的库文件地址
ln -s /usr/local/mysql/lib/mysql /usr/lib/mysql
ln -s /usr/local/mysql/bin/mysql_config /usr/local/bin/mysql_config
ln -s /usr/local/mysql/include/mysql /usr/include/mysql
#重启后登陆上继续操作
shutdown -r now
#设置MariaDB数据库root账号密码，可以使用下面的命令进行逐步定义，自己看着提示操作就可以，默认密码为空
mysql_secure_installation
#也可以直接使用命令设置root账号密码
/usr/local/mysql/bin/mysqladmin -u root -p password "123456"
#重启MariaDB服务
service mysqld restart
#使用root用户登陆MariaDB
mysql -u root -p
```
登陆成功后图示：
![登陆成功示意图](http://7s1t08.com1.z0.glb.clouddn.com/mariaDB-loginSuccess.png)


###PHP的安装
1. 解压并安装
```
#切换到下载包的存储目录
cd ~/download
#解压
tar -zxvf php-5.6.10.tar.gz
#进入php目录
cd php-5.6.10
#编译
./configure --prefix=/usr/local/php --with-fpm-user=www --with-fpm-group=www --with-config-file-path=/usr/local/php/etc --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/bin/mysql_config --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-discard-path --enable-magic-quotes --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --with-curlwrappers --enable-mbregex --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --enable-mbstring --with-mcrypt --enable-ftp --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --with-mime-magic
#安装
make && make install
```
2. 配置
由于我们使用的是nginx，所以只需要配置php-fpm.conf即可
```
#使php命令在命令行中可以直接使用
cp /usr/local/php/bin/php /usr/sbin
#新建php-fpm配置文件
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
#配置nginx,开启php-fpm,把 [server] 按照以下修改即可。
vim /usr/local/nginx/conf/nginx.conf
server {
        listen       80;
        server_name  localhost;
        charset utf-8;
        root /wwwroot;
        index index.php index.html index.htm;

        location / {
        }
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  SCRIPT_NAME      $fastcgi_script_name;
            include        fastcgi_params;
        }
    }
```
3. 重启nginx和php-fpm
```
/usr/local/nginx/sbin/nginx
/usr/local/php/sbin/php-fpm
#如果提示已经在运行无法重启的话，可以手动杀死进程。先使用 ps -ef | grep [php-fpm或nginx]获取运行中的进程id,然后kill -9 [进程id],嫌麻烦可以直接重启服务器，然后重新启动nginx和php-fpm即可
```
重启完毕后，可以测试一下。
```
cd /wwwroot
vim i.php
#插入的代码
<?php
    phpinfo();
?>
```
然后，可以访问服务器来查看是否配置成功了。如：h ttp://121.42.25.XXX/i.php，成功的话，就可以看到亲切的php页面啦。如下图:
![phpinfo](http://7s1t08.com1.z0.glb.clouddn.com/phpinfo.png)

**LNMP的安装配置就到这里了，下面是一些安装过程中遇见的问题解决。**
***
###报错处理
1. 我在编译MariaDB时，会报这个错`cc: Internal error: Killed (program cc1)`，基本意思就是进程被干掉了。查了下资料，应该是内存不足导致的，解决方法要么是加内存，要么是增加swap分区大小，我发现aliyun的系统默认是不分配swap的，所以我这里给swap增加了2GB,然后编译就可以通过了，具体方法如下：
```
#查看当前分区情况，也就是内存和swap分区的使用情况,单位:MB
free -m
#增加交换分区文件,我这里增加爱2G
dd if=/dev/zero of=/home/swap bs=1024 count=2048000
#设置交换文件
mkswap /home/swap
#立即启用
swapon /home/swap
#如果要在引导时自动启用，则编辑 /etc/fstab 文件，添加行:
/home/swap swap swap defaults 0 0
```