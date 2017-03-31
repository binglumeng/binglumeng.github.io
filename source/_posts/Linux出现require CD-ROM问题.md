---
title: UbuntuServer/CentOS安装require CD-ROM问题
date: 2017-01-09 11:08
tags: 
    - Linux
    - CD-ROM
categories:
    - Linux
---

闲话少叙，直入正题。话说最近在公司帮助安装过CentOS服务器系统，自己也搭建Android编译环境--Ubuntu Server系统。想如今安装系统基本都是U盘安装或者硬盘本地安装，几乎很少用光盘安装模式。简要步骤如下：

##1、制作启动盘
使用Linux Live Creater 制作linux系统启动盘，该工具还是比较不错的，多次用于制作desktop版本的linux系统盘。

另一个更为常用的便是UltraISO软碟通，该工具制作windows，linux系统盘都可以。
##2、安装

-  安装CentOS系统时候，使用LinuxLiveCreater制作启动盘，<font color="FF0000"> **电脑是Dell Vostro商用台式机**</font> 该型号电脑多有奇怪，网上关于在该型电脑上引发的一些系统安装与环境配置问题也不少。使用UltraISO制作的启动盘，无法正确进入安装步骤。用linuxlivecreater制作的启动盘，在安装过程中，会出现找不到安装盘，require CD-ROM，也就是说它不认U盘中的安装文件。

-  解决方法，就是在U盘中放置一个CentOS的系统镜像，然后在如上安装步骤中，选择手动指定安装盘位置，选择U盘中的iso镜像，如此即可正常完成安装。

Ubuntu Server的安装

- 同样是Dell Vostro电脑，这次是使用LinuxLiveCreater制作的U盘无法在安装过程中找到安装文件，而这里不能放置iso镜像文件，因为在安装选项中，没有让你选择指定路径的选项。报错 require CD-ROM。
  解决方式，是使用UltraISO来制作启动盘，安装相对就比较顺利了。

虽说问题不算多么高的技术含量，但是也许能帮助到一些需要的网友，在此记录之。