## git 简介

linux 花了两周时间自己用c 写了一个分布式版本控制系统, 这就是git, 

安装完git 后第一步是设置用户名和邮箱

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

### 创建版本库

```shell
mkdir learngit
cd learngit
pwd
git init
```



```shell
echo "# MachineLearning-InAction" >> README.md
git add README.md
git commit -m "first commit"
```

为什么git 添加文件需要add, commit 一共有两步, 因为commit 可以一次提交跟多文件, 所以可以多次add 不同的文件

```shell
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

## 时光机穿梭

每当你觉得文件修改到一定程度的时候, 就可以保存一个快照, 这个快照在git 中被称为commit, 

使用 **git log** 显示从最近到最远的提交日志,  

```shell
git log  --pretty=oneline
```

#### commit id

这个是版本号, 用SHA1 计算出来的一个非常大的数字, 用十六进制表示, 因为git 是分布式版本控制系统, 

### 返回到先前版本

首先, git 需要知道当前版本是哪个版本, 用HEAD 表示当前版本, 上一个版本是**HEAD^**, 上上个版本是**HEAD^^**,当然往上100个版本写100个^, 不好写, 就写成 **HEAD~100**

```shell
git reset --hard HEAD^  # 回到上一个版本
git log # 看看现在版本库中的状态
```

如果又想回去, 就像从21世纪坐时光穿梭机到了19世纪, 现在想回去怎么办?

如果命令行窗口没有被关掉, 可以找到那个commit id 是什么, 然后使用命令

```shell
git reset --hard [commit id]
```

git 的版本回退很快, 因为git在内部有一个指向当前版本的HEAD 指针, 当回退版本时候, git 仅仅把HEAD 从指向当前 commit 改为指向 上一个commit, 然后顺便把工作区的文件更新了, 所以HEAD 指向哪个版本号, 就可以把当前版本定位在哪.

使用

```shell
git reflog 
```

可以用来记录你的每一条命令

### 工作区和暂存区

#### 工作区

就是电脑上能看到的目录

#### 版本库

工作区中有一个隐藏目录.git, git 的版本库中存了很多东西, 最重要的就是stage 暂存区, 还有git 为我们自动创建的第一个分支master,以及指向master 的一个指针HEAD, 

#### 往版本库中添加

前面讲了我们把文件往git 版本库中添加的时候,分为两步

1. 使用 git add 把文件添加进去, 实际上就是把文件修改添加到暂存区
2. 用git commit 提交更改, 就是把暂存区的所有内容提交到当前分支

#### 管理修改

git 管理的是修改, 当用git add 命令后, 在工作区的第一次修改被放入暂存区, 但是, 在工作区的第二次修改并没有放入暂存区, 所以, 用git commit 只负责把暂存区的修改提交了, 就是第一次的修改提交了, 第二次的修改不会被提交

可以使用 **git diff HEAD -- README.TXT** 查看工作区和版本库里面最新版本的区别

#### 撤销修改

**git checkout -- readme.txt** 把这个文件回到最近一次 git commit 或者git add 时的状态

-- 很重要, 如果没有,就变成 切换到另一个分支

撤销缓存区中的内容: **git reset HEAD file**, 也就是把add 过的内容重新没有add 过(放在工作区), 然后可以使用 **git checkout -- file** 丢弃工作区的修改

如果从暂存区提交到了版本库, 那么使用版本回退, 可以回退到上个版本.不过, 这也是有条件的, 就是还没有把自己的本地版本推送到远程

#### 删除文件

如果在文件管理器中使用 **rm **把文件删了, 那么git 知道你删除了文件, 因此, 工作区和版本库就不一致了, git status 命令立即告诉哪些文件被删除了,

现在有两个选择,一个是从版本库中彻底删除文件, 使用 **git rm**, 并且 **git commit**

```
git rm text.txt
git commit -m 'remvoe text.txt'
```

现在文件就从版本库中删除了,

如果想恢复, **git checkout -- test.txt**

### 远程仓库

#### 添加远程库

在github 上创建名字相同的项目

`git remote add origin https://github.com/QingqiLei/MachineLearning-InAction.git`

`git push -u origin master`

#### 从远程库克隆

`git clone https://github.com/QingqiLei/MachineLearning-InAction.git`

### 分支管理

#### 创建和合并分支

每次提交, git 都把他们串成一条时间线, 如果只有一条时间线,在git 里, 这个分支叫主分支, 即master 分支, HEAD 严格来说不是指向提交, 而是指向master, master 指向提交

```shell
git checkout -b dev   # 加上-b 表示创建并切换
# git branch dev, 
# git checkout dev
git branch # 查看分支
echo "from dev"  >> README.md
git add README.md
git commit -m 'branch test'
git checkout master  # 打开 master 分支
git merge dev  # 合并, 在查看README.md 的内容, 可以看到已经合并了
git branch -d dev # 可以放心删除了
```

#### 解决冲突

```shell
git checkout -b feature1 # 以当前分支创建新分支
vim README.md # 修改最后一行为  fi
git add README.md  # 
git commit -m  "de" 提交
git checkout master # 切换分支
vim README.md # 修改为firs
git add README.md
git commit -m "simple"
git merge feature1 # 会有冲突

# <<<<<<< HEAD
# firs
# =======
# fi
# >>>>>>> feature1

# 改为first
git add README.md
git commit -m 'conflick fixed'
git log --graph --pretty=online --abbrev-commit # 显示图
git branch -d feature1 # 删除feature1 分支
```

#### 分支管理策略

使用--no--ff 参数, 表示禁用fast formard

`git merge --no-ff -m 'merge with no-ff' dev`

这个和直接merge 不一样, 因为master 不是指向dev , 而是有一个新的master 的commit, dev 下一个是master

#### stash

使用stash 将工作现场存储起来, 等以后恢复现场后继续工作

```shell
git status
git stash
git checkout master
git checkout -b issue-101
git add readme.txt 
git commit -m "fix bug 101"
git checkout master
git merge --no-ff -m "merged bug fix 101" issue-101
git checkout dev
git status
git stash list
git stash pop # 恢复同时删除
git stash apply # 不删除
```

#### 删除未被合并的分支 -D

```shell
git checkout -b feature-vulcan
git add vulcan.py
git status
git commit -m "add feature vulcan"
git checkout dev
# git branch -d feature-vulcan # 错误, 因为分支还没有被合并
git branch -D feature-vulcan
```

#### 推送到远程

```shell
git remote
git remote -v
git push origin master # 推送master 到origin 上面
git push origin dev # 推送其他分支, 比如dev, 就改成这个

git clone git@github.com:michaelliao/learngit.git # 另一个人下载项目, 下载master 分支
git branch
git checkout -b dev origin/dev  # 创建远程origin 的dev 分支到本地
```

指定本地dev分支与远程origin/dev分支的链接, 根据提示, 设置dev 和origin/dev的链接

`git branch --set-upstream-to=origin/dev dev`

多人协作的工作过程通常是这样的:

1. 首先, 可以尝试用  `git push origin <branch-name> 推送自己的修改`
2. 如果推送失败, 则因为远程分支比你的本地更新, 需要先使用git pull, 尝试合并
3. 如果合并有冲突, 解决冲突, 并在本地提交
4. ''



#### git rebase

多人协作, 后push的要先pull, 这样才能成功.但是这样时间线就很乱, 因为有很多merge 操作

```shell
git log --graph --pretty=oneline --abbrev-commit # 在origin 前面有两个 commit
git push origin master # 失败, 因为有人先于我们推送了远程分支
git pull
git log --graph --pretty=oneline --abbrev-commit
git rebase
git log --graph --pretty=oneline --abbrev-commit
git push origin master 
```

### 标签管理

#### 创建标签

```shell
git checkout master # 切换到需要打标签的分支上
git tag v1.0 # 打标签
git tag #查看标签
git tag v0.9 f52c633 #　指定commit 打标签
git tag -a v0.1 -m "version 0.1 released" 1094adb # commit 的时候, 使用 -a
```

#### 删除标签

```shell
git tag -d v0.1 # 标签只存储在本地, 不会自动push 到远程
git push origin v1.0 # push 某个标签到远程
git push origin --tags # 一次性push 全部尚未推送到远程的本地标签库
git tag -d v0.9  # 删除远程标签
git push origin :refs/tags/v0.9 # 删除远程标签
```

















