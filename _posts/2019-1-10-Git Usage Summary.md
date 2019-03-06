---
layout: post
title: Git Usage Summary
categories: [git]
tags: [git]
---

* content
{:toc}


# 一、安装

## Linux

通过`sudo apt-get install git`安装。如果是较老的Linux版本或者Debian，试试`sudo apt-get install git-core`。

## Mac OS X 

第一种方法是安装homebrew，然后通过homebrew 安装Git.参考[http://brew.sh/](http://note.youdao.com/)。
第二种方法（推荐方法）是从AppStore安装XCode，XCode->Preferences->Downloads->Command Line Tools->install。

## Winsdows

从官网下载程序安装。

## 配置

因为Git是分布式版本控制系统，所以必须提供机器的信息。

    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"

`--global`表示在这台机器上的所有repository都使用这个配置。

# 二、创建repository

创建一个空目录，或者利用已有的目录，在这个目录下执行`git init`。那么这个repository就创建好了，该目录下会生成一个隐藏的.git目录，用于管理该repository。

**注意，使用Windows时，目录不能包含中文。**

# 三、版本管理

- `git status`：查看状态，比如有哪些修改了，哪些正待提交。add之前提示是“Changes not staged for commit:”，而add之后提示是“Changes to be committed:”。
- `git diff readme.txt`：查看readme.txt上进行了哪些更改。
- `git add readme.txt`：添加到暂存区。添加目录下的所有文件是`git add .`。
- `git commit -m "information of this commit"`：将暂存区的内容全部提交。`-m` 的意思是添加这次提交的message。
- `git log`：查看从近到远的所有提交日志，内容包括版本号、作者、时间和提交信息。
- `git reset --hard HEAD^`：将版本回退到上个版本。HEAD表示当前版本，HEAD^^表示上上个版本。HEAD~100表示往上一百个版本。这里也可以指定版本号来回退，比如有一次提交后版本号为3428614ad34........，那么指令可以写成`git reset --hard 3428614`，写前几位就行了，Git会自动找。所有版本都可以通过版本号来找到，即使是回退过后。
- `git reflog`：查看以前每次操作和当时所在的版本号，可以看到**所有**版本号。
- `git checkout -- readme.txt`：丢弃工作区的修改，返回到最近一次add或commit的状态。实际上是用版本库里的版本替换工作区的版本。
- `git rm readme.txt`：删除文件，要提交到分支还需要commit。




**工作区和暂存区：在建立版本库的那个目录下，`.git`目录中的内容是版本库，包含暂存区stage和分支；`.git`以外的区域为工作区，我们对文件的直接修改都是在工作区内的。`git add`后修改的内容会提交到暂存区中，`git commit`后暂存区中的内容会提交到分支。**

# 四、远程仓库

## SSH Key

SSH是个协议，用于计算机之间的加密登录。Git中使用SSH主要是为了免密登录。使用公钥登录的工作流程为：
![HOW DOES THE SSH PROTOCAL WORK](/blog/assets/ssh.png)

远程主机上存放公钥，用户自己保存私钥，**公钥可以解密私钥加密后的信息，反之亦成立。**用户在登录远程主机的时候，远程主机需要确定该用户身份。这一步其实有很多方法可以实现，比如远程主机随便发送一段信息给用户，用户用私钥加密后传回，然后远程主机一解密便能核对该用户是否可信。

生成SSH Key的方法为：

    ssh-keygen -t rsa -C "email@example.com"

然后将公钥添加到github上即可。

## 添加到远程库

    git remote add origin git@github.com:accountname/repo_name.git  #连接到远程库
    git push -u origin master   #第一次推送master分支的所有内容
    git push origin master  #之后推送master分支的所有内容

`-u`表示第一次推送master分支，因为远程库是空的，所以Git不但会把本地的master分支内容推送到origin master分支，还会把本地的master分支和origin master分支关联起来。

## 从远程库克隆

    git clone git@github.com:accoundname/repo_name.git

也可以将git...替换成http://格式，在github上能直接复制这个地址，但是速度较慢。

# 五、分支管理

## 创建分支

创建分支dev：

    git branch dev

创建分支dev之前master指向最近一次提交。head指向master，因而此时的操作都是基于master分支的。
此时创建dev分支，**因为head指向的是master，所以会创建一个从master分出来的分支。**当然，因为dev分支没有改变项目内容，所以dev也指向最近一次提交（也就是master指向的内容）。

将head指针指向dev分支：

    git checkout dev

将head指针指向dev，表示现在是基于dev分支操作。新的提交会添加到dev分支上。

上面两句命令可以用一条命令代替：
    
    git checkout -b dev

**注意：分支也可以有分支，这取决于创建分支的时候基于那个分支操作（即head指针所指）**

## 分支合并

合并分支：

    git merge dev

假如dev是从master分出来的，这条命令则表示将dev和master分支合并。

如果master分支在之前没有新的提交，则此时master指针会直接指向dev指向的提交。

如果master分支在之前有新的提交，即master和dev同时有新的提交，则合并可能会**发生冲突**。
    

通常合并分支时，如果可能，Git会用Fast forward模式，但在这种模式下，删除分支后，会丢掉分支信息。**如果要强制禁止用fast forward，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。使用"--no-ff"参数来禁用fast forward：

    git merge --no-ff -m "merge with no-ff" dev
    

## 删除分支
## 删除分支

删除dev分支：

    git branch -d dev

一般合并后就可以删除原来的分支了。

## 分支管理策略

在实际开发中，应该按照以下几个原则管理分支：

- master分支应该是非常稳定的，仅用来发布新版本 ，平时不能再上面干活。
- 平时就在dev分支上干活。
- 团队中每个人都有一个基于dev分支的分支，提交的时候提交到dev分支上就行了。

# 标签管理

一般会给每次commit打上标签，如：

    git tag v0.9 62249

后面的62249是某一次提交的id。如果不加这个参数，默认是给最近一次commit打标签。

查看所有标签：

    git tag

查看标签详细信息：

    git show v0.9

创建带有说明的标签，-a指定标签名，-m指定说明名字：

    git tag -a v0.1 -m "version 0.1 released" 62249

删除标签：

    git tag -d v0.1