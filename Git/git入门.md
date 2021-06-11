**1、初始化本地仓库**

```tex
git init
```

注意，初始化后出现`.git`隐藏目录，里面存放着与本地库相关的子目录和文件，不要删除，也不要胡乱修改。

**2、设置签名**

作用：区分不同开发人员的身份

辨析：这里设置的签名和登录代码托管中心的用户名、密码没有任何关系

签名分为项目级别（仓库级别）和系统级别

```tex
项目级别：
git config user.name 用户名
git config user.email 邮箱

系统级别（一般设置这个）：
git config --global user.name 用户名
git config --global user.email 邮箱
```

设置的项目级别的签名信息可以在`.git/config`文件中查看；系统级别的签名信息在`~/.gitconfig`中。

**3、常用命令**

查看工作区、暂存区状态

```tex
git status
```

添加“新建/修改”文件到暂存区

```tex
git add 文件名
```

从暂存区撤回

```tex
git rm --cached 文件名
```

将暂存区文件提交到本地仓库

```tex
git commit -m '提交信息'
```

查看提交的历史记录

```tex
git log
```

查看历史记录，一行显示一条提交记录

```tex
git log --pretty=oneline
```

查看历史记录，一行显示一条，显示文件哈希值的一部分

```tex
git log --oneline
```

查看历史记录，比上述命令多了指针移动的步数信息

```tex
git reflog
```

回退版本

```tex
git reset --hard 索引值

使用^后退一步，几个^就后退几步
git reset --hard HEAD^^  后退2步

使用~可以指明后退几步
git reset --hard HEAD~3   后退3步

备注：reset的三个参数——soft、mixed、hard
soft：只在本地仓库移动HEAD指针
mixed：在本地仓库移动HEAD指针、重置暂存区
hard：在本地仓库移动HEAD指针、重置暂存区和工作区
```

删除文件并找回

```tex
前提：删除前，文件存在时的状态提交到了本地仓库
操作：git reset --hard 指针位置
	若删除操作提交到了本地仓库，指针位置指向历史记录
	若删除操作没有提交到本地仓库，指针位置使用HEAD
```

比较文件差异

```tex
git diff 文件名：将工作区中的文件和暂存区进行比较
git diff 本地仓库历史版本 文件名：将工作区中的文件和本地库历史记录比较
git diff HEAD：不带文件名比较多个文件
```

分支

```tex
git branch -v：查看本地仓库所有分支
git branch 分支名：创建分支
git checkout 分支名：切换分支
git merge 分支名：将指定分支合并到当前所在分支

合并过程冲突的解决：
1、编辑发生冲突的文件，删除特殊符号
2、把文件修改到满意的程度，保存
3、git add 文件名
4、git commit -m "日志信息" （后面不加文件名）
```

将本地仓库推送到远程仓库

```tex
第一次推送，用origin作为仓库地址的别名
git remote add origin 仓库地址
推送到origin仓库的master分支
git push origin master
```

克隆远程仓库到本地

```tex
git clone 远程仓库地址
```

拉取

```tex
git pull 远程仓库名/远程分支名

备注：pull相当于fetch+merge。fetch是从远程仓库拉取下来，但没有下载到本地，merge是将远程仓库合并到本地
```

让`Git`忽略`SSL`证书认证

```tex
git config --global http.sslVerify 'false'
```







