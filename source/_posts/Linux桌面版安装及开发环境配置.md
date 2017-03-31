---
title: Linux桌面版安装及开发环境配置
date: 2016-10-24 10:45:25
tags:
    - Linux
    - 开发环境
categories:
    - Linux
---

# Linux桌面版安装及开发环境配置

## 1、安装Linux For Desktop

本文所面向对象无非也是和本人一样的技术小白，和[windows](http://blog.csdn.net/binglumeng/article/details/50195649)系统以及mac不同，对于linux系统的选择却也是众说纷纭，各有喜好。目前本小白所认知范围内，服务器server多是RedHat、centos和ubuntu server。桌面版那可太多了，只列举个人所喜好和试用过的linux发行版：

> Linux Mint、Ubuntu、Deepin、Fedora、Centos。估计大多数朋友和我一样都会有一个选择纠结症，不过目前来说，还是比较喜欢Linux Mint。

闲言少叙，既然做开发玩Linux的朋友，想必都有点基础，下面就不过于具体描述操作。

1. 首先选择个人喜好的linux发行版，在官网下载最新稳定版image镜像。
2. 在windows下用软碟通[步骤请自行百度]，或者linux live creater、来制作安装盘。*切记安装盘会被全部格式化的哦！*<推荐使用LinuxLiveCreater>
3. 完成安装盘的制作后，重启电脑选择启动菜单，进入U盘安装模式。
4. 多数Linux提供了live模式，然后就是Next-->Next...安装系统了。一般我们小白级别的安装选择的Linux都比较方便的，基本都是开箱即用。若是高级玩家，使用如archLinux之类的，另当别论。

## 2、系统配置

以下配置说明，仅是依据个人喜好和个人所认知范围内的最佳搭档和选择，仅供参考。

- 系统设置

  > 1. 安装系统时候，选择中文语言环境，有的系统却并不能同时安装中文输入法，需要手动安装。在系统---设置---语言，中选择安装语言和输入配置。将ibus或者fitx程序及扩展插件选择安装，即可完成中文输入法的配置。(命令配置方式，暂不介绍)
  > 2. 常用办公软件的安装，一般LinuxMint或者Ubuntu桌面版都会附带一些办公软件，但是个人还是选择一些喜好的[工具](http://blog.csdn.net/binglumeng/article/details/51590549)。office软件有LibreOffice、WPS，浏览器Chrome、firefox、opera、tor。便于文件传输与即时通讯，安装iptux或者飞鸽传书。流程图有libreDraw，Dia，UML有umbrello。思维导图就用Xmind咯。项目管理Planner。
  > 3. 辅助类工具软件，ruler屏幕尺子、有道词典、网易云音乐、color-picker、有印象笔记，截图shutter，录屏RecordMyDesktop，安装chmsee看chm文档还是很好的工具。AnyDesk远程工具。redshift根据地理位置和时间调整屏幕色温的软件，挺好的。flux也一样。然后安装indicator-lockkeys（还有其他的）用于键盘状态的指示。`MarkDown`笔记编辑器有typora、cmdMarkdown、haroopad、atom和vs code也很好的支持。chrome应用形式的马克飞象、StackEdit。
  > 4. 虚拟机的安装很有必要，linux下有时候不得不想用一下windows的软件，虽然有wine，但是感觉还是用个小虚拟机系统好些，比如有时候linux的下载速度就是不如windows，也很无奈哦。VM player、VM box各有优劣，前者支持广泛，后者运行轻便，根据本机配置和虚拟系统来选择。

- 开发环境配置

  > 本人入行IT开始是从Android开发入门的，所以配置环境以此为主来介绍。
  >
  > - 文本编辑，程序员纠结不清的选择就是编辑器和系统，呵呵，咱也不扯了，依据自己喜好来吧。geany、sublime text、atom、visual studio code。二进制查看wxHexEditor。
  >
  > - 各种sdk的配置，JDK(android或java开发还是不推荐openJDK)，AndroidSDK。用tomcat搭建简单的web服务器用于测试。Coding的各种IDE选择使用Eclipse，AndroidStuido（类似intellij）。
  >
  > - 版本控制常用还是git和svn，IDE都可集成版本控制操作。有时候还是需要额外的工具，如BeyondCompare用于文件比较。还有数据库sqlite man。
  >
  > - AndroidStudio插件也是很重要，这个个人装的比较多哦：
  >
  >   ADB Idea、ADB WIFI、ButterKnife、DPI、Drwaable Importer、Holo Colors、Material Design、Methods Count、History Mining、CodeGlance、Codota、Key Promoter、Parcelable、Postfix、Dash、Power Mode、Robotium Recorder、Shortcut Translator、SonarLint、SonarQube、xStructure。

*附言* ：技术小白，记录笔记仅供参考，初稿难免潦草，有好的工具推荐或者其他建议，烦请留言，知识共享，共同学习。