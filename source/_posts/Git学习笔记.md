---
title: "Git学习笔记"
date: 2016-11-18 20:24
author: 冰路梦
tag:
    - Linux
    - Git
categories:
    - Linux
---
# Git学习笔记

<!-- TOC -->

- [Git学习笔记](#git学习笔记)
    - [1、Git简介](#1git简介)
        - [Git与SVN区别](#git与svn区别)
    - [2、Git安装配置](#2git安装配置)
        - [1)、Git配置](#1git配置)
        - [2)、用户信息](#2用户信息)
        - [3)、文本编辑器](#3文本编辑器)
        - [4)、差异对比工具](#4差异对比工具)
        - [5)、查看配置信息](#5查看配置信息)
    - [2、Git 工作流程](#2git-工作流程)
    - [3、Git工作区、暂存区和版本库](#3git工作区暂存区和版本库)
        - [基本概念：](#基本概念)
    - [5、Git创建仓库](#5git创建仓库)
    - [6、Git基本操作](#6git基本操作)
    - [7、Git分支管理](#7git分支管理)
    - [8、Git查看提交历史](#8git查看提交历史)
    - [9、Git标签](#9git标签)
    - [10、Git远程仓库](#10git远程仓库)
    - [11、Git服务器搭建](#11git服务器搭建)

<!-- /TOC -->

## 1、Git简介

Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

### Git与SVN区别

GIT不仅仅是个版本控制系统，它也是个内容管理系统(CMS),工作管理系统等。

如果你是一个具有使用SVN背景的人，你需要做一定的思想转换，来适应GIT提供的一些概念和特征。

Git 与 SVN 区别点：

- 1、GIT是分布式的，SVN不是：这是GIT和其它非分布式的版本控制系统，例如SVN，CVS等，最核心的区别。
- 2、GIT把内容按元数据方式存储，而SVN是按文件：所有的资源控制系统都是把文件的元信息隐藏在一个类似.svn,.cvs等的文件夹里。
- 3、GIT分支和SVN的分支不同：分支在SVN中一点不特别，就是版本库中的另外的一个目录。
- 4、GIT没有一个全局的版本号，而SVN有：目前为止这是跟SVN相比GIT缺少的最大的一个特征。
- 5、GIT的内容完整性要优于SVN：GIT的内容存储使用的是SHA-1哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

## 2、Git安装配置

### 1)、Git配置

Git 提供了`git config`的工具，用于配置读取工作环境变量

- /etc/gitconfig文件：系统级别的配置文件，使用`git config --system`操作此文件
- ~/.gitconfig文件：当前用户的配置文件，使用`git config --global`操作此文件
- .git/config文件：当前Git目录中的配置文件，仅对当前项目有效。

### 2)、用户信息

配置user.name和user.email

```shell
git config --global user.name "name"
git config --global user.email "name@name.com"
```

可以不用`--global`参数，则只配置当前git项目。

### 3)、文本编辑器

可以设置git默认的文本编辑器

```shell
git config --global core.editor vim # 使用vim作为指定编辑器
```

### 4)、差异对比工具

可以指定对比工具

```sh
git config --global merge.tool vimdiff
```

### 5)、查看配置信息

```sh
git config --list
```



## 2、Git 工作流程

git一般工作流程：

- 克隆 Git 资源作为工作目录。
- 在克隆的资源上添加或修改文件。
- 如果其他人修改了，你可以更新资源。
- 在提交前查看修改。
- 提交修改。
- 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

下图展示了 Git 的工作流程：

![git](http://www.runoob.com/wp-content/uploads/2015/02/git-process.png)

## 3、Git工作区、暂存区和版本库

### 基本概念：

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫stage, 或index。一般存放在"git目录"下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![work](http://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage, index），标记为 "master" 的是 master 分支所代表的目录树。

图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。

图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。

当对工作区修改（或新增）的文件执行 "git add" 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。

当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。

当执行 "git reset HEAD" 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。

当执行 "git rm --cached <file>" 命令时，会直接从暂存区删除文件，工作区则不做出改变。

当执行 "git checkout ." 或者 "git checkout -- <file>" 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。

当执行 "git checkout HEAD ." 或者 "git checkout HEAD <file>" 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。

## 5、Git创建仓库

- git init

`git init`初始化git仓库，如此才能执行其他git操作。会生成一个`.git`目录。

```sh
git init <repo_name>
```

- git clone

  从git仓库拷贝项目

  ```sh
  git clone <repo> <directory>
  ```

  其中`repo`为git仓库，`dir`为本地目录

## 6、Git基本操作

- git init
- git clone
- git add
- git status
- git diff
- git commit
- git reset HEAD
- git rm
- git mv

## 7、Git分支管理

- git branch
- git checkout
- git merge

## 8、Git查看提交历史

- git log

## 9、Git标签

- git tag

## 10、Git远程仓库

- git remote add [shortname]\[url]
- git remote
- git fetch
- git pull
- git push [alias]\[branch]
- git remote rm [alias]

## 11、Git服务器搭建

以CentOS为例

- 安装Git

  ```sh
  $ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
  $ yum install git
  ```

  创建一个git用户组和用户，用于运行git服务

  ```sh
  $ groupadd git
  $ adduser git -g git
  ```

- 创建证书登录

  ```sh
  $ cd /home/git/
  $ mkdir .ssh
  $ chmod 700 .ssh
  $ touch .ssh/authorized_keys
  $ chmod 600 .ssh/authorized_keys
  ```

- 初始化Git仓库

  ```sh
  $ cd /home
  $ mkdir gitrepo
  $ chown git:git gitrepo/
  $ cd gitrepo

  $ git init --bare w3cschoolcc.git
  Initialized empty Git repository in /home/gitrepo/w3cschoolcc.git/
  ```

- 克隆仓库

  ```sh
  $ git clone <url>
  Cloning into 'w3cschoolcc'...
  warning: You appear to have cloned an empty repository.
  Checking connectivity... done.
  ```

设置`git`用户不能shell登录，则编辑`/etc/passwd`文件，

```sh
git:x:503:503::/home/git:/bin/bash
#将后面的/bin/bash改为/sbin/nologin
```

