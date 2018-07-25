去年用EC2部署过Yii2的环境，不过是发布在CSDN上，所以这次迁移过来。其实本文主要还是在介绍通过git和github配合来安装Yii2框架。

**环境：RedHat6.5 PHP5.5.12**
**注意：Yii2需要php5.4.0 以上的环境来运行**

近来准备玩玩Yii2框架，先是用我自己电脑开虚拟机搞，奈何电脑太差，运行起来比较慢，没办法，就寻思着搞云主机得了。由于自己有一个已经备案了的域名，所以信心满满的去搞了个阿里云主机，结果2天不到，又无法访问，说是不在它阿里那里备的案。于是我尝试搞了个亚马逊的免费一年的云主机，现在只需要你有张信用卡就可以申请了哟，不管你是银联还是visa。虽说速度慢点吧，但至少想干嘛干嘛呀。

现在Yii2的布设方式推荐使用composer来进行，所以我这里是先安装LNMP环境（见我的其他文章），再安装composer，再安装git，最后布设Yii2

###一、安装composer
1.cd到安装目录，我这里是安装到了`/www/composer`
2.安装：`curl -s http://getcomposer.org/installer | php`
3.查看安装是否成功，`php composer.phar`
4.简化命令：`sudo mv composer.phar /usr/bin/composer`
5.这样，你就可以全局使用composer命令了。如：`composer about`

###二、安装 git
首先，安装git
1.cd到软件包目录，我这里是/www/tmp
2.`wget http://kernel.org/pub/software/scm/git/git-1.9.3.tar.gz`，这里版本可以自己访问该网站进行查看，我使用的是最新版的1.9.3。
3.解压安装包：`tar -zxvf git-1.9.3.tar.gz`
4.cd到安装目录。`cd git-1.9.3`
5.生成编译文件 `./configure` ，这里编译之前可以先 `./configure --help` 查看附加选项。
6.编译 `make && make install`
7.完成后可以查看是否安装成功。`whereis git` 查看安装在哪里。`git --version` 查看当前git版本。如果能正常显示，是最好的。但是我在这一步使用`git --version` 命令时，提示
> git: error while loading shared libraries: libcharset.so.2: cannot open shared object file: No such file or directory

一番查资料后找到解决方法，链接为：http://blog.chinaunix.net/uid-26212859-id-3256667.html

**到这里已经安装成功了。下面将进行git的配置.**

8.`cd ~/.ssh` ,如果之前存在了key，则需要先备份。我这里默认你是第一次安装并使用git。
9.创建一个新的ssh key，命令为：`ssh-keygen -t rsa -C "xxxx@xxx.com"` ,这里的邮箱地址填写你自己的github邮箱地址。
10.中间会提示2次让你输入密码短语，这里可以直接回车。最后你可以看到这样的信息，就是创建成功。
![ec2_1](http://7s1t08.com1.z0.glb.clouddn.com/ec2_1.jpg)

11.查看ssh key并复制，`cat ./id_rsa.pub` 然后访问 [https://github.com](https://github.com)，点击右上角的扳手图标（Account Settings）--->SSH Keys------>Add SSH key，如图所示：
![ec2_1](http://7s1t08.com1.z0.glb.clouddn.com/ec2_2.jpg)

12.随意输入title和刚才从linux中复制的SSH key后点击增加即可。
13.测试能否正常连接到github，命令为：`ssh -T git@github.com`
![ec2_1](http://7s1t08.com1.z0.glb.clouddn.com/ec2_3.jpg)

输入yes后，显示：Hi XXXXXX! You've successfully authenticated, but GitHub does not provide shell access.表示连接成功。此时你的github.com里面刚才添加的keys应该就是亮的了。

###三、git先搞到这里，下面来布设Yii2框架
14.进入/目录，然后运行以下命令：
`php composer.phar create-project --prefer-dist --stability=dev yiisoft/yii2-app-basic /www/wwwroot`
如果按照我的第一步设置composer后，可以直接使用
`composer create-project --prefer-dist --stability=dev yiisoft/yii2-app-basic /www/wwwroot`
，这里的/www/wwwroot目录就是你的web根目录(注意上面的命令不要在这个根目录下面执行，否则布设Yii2会报错。)。
15.最后，ls该目录，如果出现Yii2文件的话，就成功了。如下图：
![ec2_1](http://7s1t08.com1.z0.glb.clouddn.com/ec2_4.jpg)
**到此，布设Yii2完毕。**

