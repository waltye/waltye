***linux下别名alias的基本使用。刚好在设置这个东西，记下来以免遗忘。***
###环境
系统: OSX 10.10.3 和 CentOS 7.0 64位，经测试都可以正常运行

###功能说明
功能说明: 设置指令的别名，用来更快的输入指令
语法: alias [别名]=[指令名称]
举例: `alias la='ls -A'`，这样，在命令行中输入`la`就等于输入了`ls -A`
删除别名命令: unalias [别名]

###注意事项
生效时间: ***在命令行中直接运用alias命令来进行处理的话，生效时间仅是该次登录的操作时间。也就是仅本次登录可用，退出了系统再重新进入就没用了。***

###作用域
基本的alias命令只是临时性生效的，一旦用户退出就失效了。
永久生效解决方法：
1. 把命令加入环境变量配置文件中，如:/etc/bashrc或~/.bashrc中，他们的区别在于一个是设置给所有系统用户可用，一个是仅当前用户可用
2. 修改完后记得重载环境配置文件，否则本次使用会没有加载，命令为:source [文件名],如

###最后附上操作实例
```
vim ~./bashrc
echo alias la=\'ls -A\' >> ~/.zshrc
source ~/.zshrc
```
这样，你之后每次输入命令`la`就等于输入`ls -A`了。
其中，~/.zshrc是我的环境配置文件，你应当修改成你自己的。