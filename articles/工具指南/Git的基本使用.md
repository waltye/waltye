**本文档只是对 [史上最浅显易懂的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000) 进行了一个精简，之后有什么遗忘了，可以快速的查阅这里。**
##安装Git
1. 在Linux上安装 `sudo apt-get install git`(Debian、Ubuntu)，`yum -y install git`(CentOS)
2. 在Mac上安装，一般Mac都默认已安装Git，可以敲 `git version` 命令查看是否已经安装，如果没有安装，使用命令 `brew install git` 来安装。

***

##初始配置
* git配置初解
    git的配置有两层，分为用户配置和仓库配置。在实际使用中，仓库配置会覆盖用户配置。
    1. 用户配置
        通过命令 `git config --global` 来进行管理，对应的文件是 `~/.gitconfig` 。也就是说，你通过命令和直接修改文件的效果是一样的。
        用户配置的方便之处在于，只需要配置一次，之后你创建的所有仓库都会使用这个配置。
    2. 仓库配置
        通过命令 `git config` 来进行管理，对应的文件是 `仓库目录/.git/config` 。一般，这个不用配置，但有些特殊情况，会用到这个配置。一旦配置了这个，那么这里配置的信息会覆盖用户配置。
        
* 我们来初始配置一下之后提交会用到的你的个人信息
```
git config --global user.name "Your Name"
git config --global user.email "example@gmail.com"
```

***

##Git版本库基本操作
###创建Git版本仓库
1. 创建并进入一个新目录，或直接进入一个已存在的目录。
2. 执行 `git init` 命令。

###添加文件和提交至版本库
添加文件至Git仓库: `git add 文件名称`
提交添加的文件至本地仓库: `git commit -m '提交说明' 文件名称`

###查看仓库状态和文件更改具体内容
1. 使用命令 `git status` 可以查看当前仓库的状态。一般用来查看当前仓库有哪些文件是被修改过的。
2. 如果有文件被修改了，可以通过 `git diff 文件名称` 来查看具体修改的点。

###版本查看和回退
* 查看提交的版本信息
   `git log` 显示详细的提交日志
   `git log --pretty=oneline` 只显示版本号和版本提交信息
* 回退版本
    1. `git reset --hard HEAD^`，其中，`HEAD`表示当前版本，`HEAD^`表示上一个版本，`HEAD~100`表示上100个版本。HEAD也可以使用具体的版本号或版本号的前面一段，Git会智能的去找对应正确的版本。
* 回到未来版本
  1. `git reflog` 可以查看所有的提交记录，不管是回退过的或者删除了的。所以通过这个命令可以获取所有的版本号。
  2. 再使用 `git reset --hard 版本号` 使当前版本指向该版本号即可。
  
###工作区修改操作
* 丢弃工作区的修改，也就是让这个文件回到最近一次添加或提交前的状态。 `git checkout -- file`
* 丢弃缓存区的修改。先 `git reset HEAD file` 丢弃缓冲区的修改，再按上一步进行操作。
* 丢弃已提交到版本库的修改，`git reset --hard HEAD^`
  
###删除文件
* `git rm 文件`，然后 `git commit`
* 如果直接使用命令 `rm` 删除了，可以使用 `git checkout -- file` 来恢复。
* `git checkout` 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
  
###远端仓库管理
* 如果想使用github的远端仓库，需要先配置ssh加密。
    1. 先查看~/.ssh目录下是否有id_rsa和id_rsa.pub文件，如果没有，则执行 `ssh-keygen -t rsa -C "youremail@example.com"`
    2. `cat id_rsa.pub`,复制得到的结果并添加至github的SSH keys中
      
* 添加远端库
    * `git remote add origin git@github.com:michaelliao/learngit.git`
    * 把本地仓库中的内容推送至远端库 `git push origin master`，如果是第一次推送，需要加上`-u`参数 `git push -u origin master`
    * 把远端库新建到本地库 `git clone git@github.com:waltye/blog.git` 或者 `git clone https://github.com/michaelliao/gitskills.git` 这种形式。
    
###分支管理
* 创建分支: `git branch xxx`
* 切换分支: `git checkout xxx`
* 创建并切换到该分支: `git checkout -b xxx`
* 查看当前分支 `git branch`
* 合并某分支到当前分支 `git merge --no-ff xxx`//使用--no-ff参数能记录分支合并情况
* 删除分支 `git branch -d xxx`
* 查看分支历史图 `git log --graph`

###冲突处理
在合并操作后，可能会产生冲突，这时候只需要修改合并后的文件，然后重新提交即可。

###标签处理
* 查看所有标签 `git tag`
* 在当前分支上打标签 `git tag v1.0`
* 给历史记录打标签 `git tag v1.0 版本号`
* 给标签增加说明文字 `git tag -a v1.0 -m '1.0版本上线'` ，使用 `git show v1.0` 查看说明文字
* 删除标签 `git tag -d v1.0`，如果已经推送至远程: 先删除本地 `git tag -d v1.0`，然后从远端删除 `git push origin :refs/tags/v1.0`
* 推送标签至远程 `git push origin v1.0` 或推送所有未推送的标签 `git push origin --tags`