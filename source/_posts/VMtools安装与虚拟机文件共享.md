---
title: VMtools安装与虚拟机文件共享
date: 2016-08-15 18:02
tags:
    - vmtools
    - 虚拟机
categories:
    - 系统相关
---

程序开发工作之余，作为调节思维也是学习技术，决定系统的学习（折腾）一下Linux。首先便是安装一个Linux系统，搭建一个学习Linux的平台环境。笔者算是技术小白，也接触了一些Linux发行版，于是选择了CentOS作为Server实验系统，ubuntu作为desktop系统。闲言少叙，直入主题。
#### Windows下虚拟机安装linux

>常用的虚拟机有Vmware和virtualbox，虽说virtualbox挺好的，内存占用也小，但是笔者还是选择了vmware，使用个人版，无须注册破解。
>在vmware中安装centos，或者ubuntu系统。（此处不做详细介绍）

#### 安装vmtools

>新版的vmware虚拟机都会自动安装vmtools功能，基本上安装好linux虚拟机的同时，就可以实现屏幕分辨率的自动调整，主机与虚拟机的文件复制等功能。
>但是在vmware设置中，开启文件共享后，重启linux系统，并未能在/mnt目录下发现主机共享的文件夹。且/mnt下没有hgfs这个目录。
>网上很多描述主机与linux虚拟机文件夹共享的说法都未提及这个问题。或许都是因为他们自主安装vmtools，而非有vmware自动安装的缘故吧。

#### 文件夹共享

>在虚拟机设置里，选项中设置好文件夹的共享，然后进入虚拟的linux系统，复制一个网上下载的完整的vmtools for linux 的vmtools.tar.gz包，到linux虚拟机中，然后在linux下解压，运行vm-install.pl，安装完整版的vmtools，一路回车即可完成安装。然后就能在/mnt目录下看到hgfs目录，之前设置的主机共享目录，也会出现在下面。

>补充，有时候不知懂啊怎么的，/mnt/hgfs下面不显示共享的文件夹，网上有说法是cd到/etc/vmware-tools下面，运行sudo vmware-config-tools.pl这个命令，重新配置一下。

* 附上可能会用的软件工具
* vmtools for Linux版 http://pan.baidu.com/s/1qXFTn8C
* vmware player Windows版 http://pan.baidu.com/s/1gfBcjmj
* vmtools for Mac版 http://pan.baidu.com/s/1pKC74UJ
* windows 的 vmtools：http://pan.baidu.com/s/1boNfw43

