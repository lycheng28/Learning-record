### 使用教程

[TOC]

##### 创建本地库

编号/名字

```
git config --global user.name 'xxx'
```

 邮箱

```
git config --global user.email 'xxx@xxx' 
```

路径/目录

```
cd xxx 
```

返回上一层目录

```
cd ..
```

显示当前目录

```
pwd 
```

创建xxx文件夹

```
mkdir xxx 
```

将当前目录设置为仓库

```
git init 
```



##### 添加修改删除文件

添加xxx文件进仓库（暂存区）一次仅一个

```
git add xxx 
```

提交进仓库 (-m‘xxx’ 本次提交说明) 一次可同步复数文件

```
git commit -m ‘xxx’  
```

查看当前仓库状态

```
git status 
```

查看xxx具体文件修改内容

```
git diff xxx
```

 查看xxx文件，工作区和版本库的最新版本区别

```
git diff HEAD -- xxx 
```

将文件退回上一个版本 HEAD 指向当前版本

```
git reset --hard HEAD
```

将文件退回指定xxx版本（版本号： commit 前几位即可自动寻找）

```
git reset --hard xxx 
```

查看提交历史 版本号

```
git log 
```

查看历史命令 版本号git reflog 

打开xxx文件

```
cat xxx 
```

将工作区提交到暂存区的数据撤销 git add 反向命令

```
git checkout 
```

将暂存区的数据以仓库为基准撤销 git commit 反向命令 退回版本

```
git reset HEAD 
```

提交修改 需先git add 提交至暂存区再Git commit提交至版本库 git add后修改的数据无法直接git commit提交 

流程 先从工作区-git add-暂存区-git commit-版本库（仓库）

丢弃当前xxx文件，工作区的修改

```
git checkout -- file（xxx） 
```

 切换到另一个分支

```
git checkout xxx
```

删除工作区xxx文件

```
rm xxx 
```

 删除版本库xxx文件

```
git rm xxx
```



##### 远程库

 连接远程库

```
git remote add origin xxx(ssh协议(git@github.com:lycheng28/Blog-site-python.git))
```

推送当前分支master到远程库 加上-u可关联本地master和远程库的master 

```
git push -u origin master 
```

第二次推送至远程库 可以不需要-u origin 默认叫法可修改

```
git push origin master 
```

克隆远程库到本地

```
git clone xxx(ssh协议(git@github.com:lycheng28/Blog-site-python.git))
```

创建远程库origin的xxx分支到本地

```
git checkout -b xxx origin/xxx 
```

将最新的提交抓取下来（用于文件修改产生冲突，人工修改）

```
git pull 
```

如git pull 提示no tracking information 则说明本地分支和远程分支的链接关系没有创建，用命令：

```
	git branch --set-upstream-to <branch-name> origin/<branch-name>  
```



##### 分支管理

创建dev分支并切换

```
git checkout -b dev 
```

创建dev分支 

```
git branch dev 
```

 查看所有分支并确定当前所在分支

```
git branch
```

切换至xxx分支

```
git checkout xxx 
```

将xxx分支合并到master主分支

```
git merge xxx 
```

删除xxx分支

```
git branch -d xxx
```

 强制删除没合并过的分支

```
git branch -D xxx 
```



###### 新切换分支指令 git switch

创建并切换到xxx分支

```
git switch -c xxx 
```

 切换到xxx分支

```
git switch xxx
```

查看分支合并图

```
git log --graph 
```

查看历史提交的commit-ID

```
git log --pretty=oneline --abbrev-commit 
```

合并但不删除合并前分支记录

```
git merge --no-ff -m '描述' xxx (xxx文件禁用fast forward模式)
```

存储当前工作现场

```
git stash 
```

还原存储工作现场并清除stash (git stash apply 还原, git stash drop 删除）

```
git stash pop 
```

 查看xxx内容

```
git xxx list
```

（xxx为commit提交的编号）

```
git cherry-pick xxx
```

查看远程库

```
git remote
```

 更加详细的远程库信息

```
git remote -v 
```

 合并分支线

```
git rebase
```



##### 标签管理

 查看所有标签

```
git tag
```

设置当前分支提交的commit标签

```
git tag xxx 
```

设置指定版本的标签

```
git tag xxx <commit-ID>
```

标签信息

```
git show <tagname> 
```

```
git tag -a <tagname> -m '说明文字'
```

删除标签

```
git tag -d <tagname> 
```

推送标签至远程

```
git push origin <tagname> 
```

推送所有标签至远程

```
git push origin --tags 
```

删除远程标签信息

```
git push origin :refs/tags/<tagname> 
```



##### 自定义Git

文件名标注颜色

```
git config --global color.ui true 
```

.gitignore文件 可忽略特定文件 (需要在工作区根目录下创建)

```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa
```

强制添加xxx文件

```
git add -f xxx
```

 查询为何不可添加xxx文件的规则

```
git check-ignore -v xxx.xx 
```



###### 配置别名

表示st等同于status 变量名 

```
git config --global alias. st status 
```

​	--global 针对当前用户起作用，不加针对当前仓库起作用

​	仓库级 配置文件是 .git/config [alias]后面 ，删除别名直接删除对应的行即可

​	用户级 C:\Users\LYC13\.gitconfig

