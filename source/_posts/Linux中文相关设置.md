---
title: Linux中文相关设置
date: 2016-08-22 11:02:25
tags:
    - Linux
    - 中文
categories:
    - Linux
---
编程语言与开发工具，操作系统大多都是国外人开发的，这点来说确实让国人汗颜，也不过多的牢骚什么，言归正传。在学习linux初期，总会遇到一些中文编码与显示及字体相关的问题，本人小白一个，也遇到此类问题。在此作为笔记记录如下，亦希望与他人有益。
#### Linux中文语言设置
>CentOS7.0中，一般在安装CentOS时候就可以选择中文，然而大多数情况下，我们还是选择安装英文版比较好。但是也需要中文支持。在Applications中--settings--Language&Region，选择语言中添加中文China汉语，下面框inputsources中添加Chinese(Intelligen Pinyin)。其默认切换键为Shift，然后apply保存，即可使用中文输入法和中文支持。若在刚才一步中language选择汉语，则linux系统界面的语言也会变成中文。

#### Linux添加字体
>其实widows和linux，以至我们常用的android手机，字体都是通用的，支持ttf格式字体。所以笔者从widows10的C：/windows/fonts中copy了几款喜欢的字体，复制到linux桌面。

>在linux下，将字体文件或者文件夹，以root权限，复制到/usr/share/fonts下，然后cd到该目录下，运行mkfontscale,mkfontdir,fc-cache -fv这三条命令，建立字体缓存，加入字体索引库。然后再系统或者其他地方的设置里就可以用这些新加入的字体了。

附，笔者发现在linux和window下有时候文件显示不一致，会乱码，所以在linux下的terminal设置为编码utf-8,其他编译器也是这个格式，在windows下也用这个格式，就能通用显示了。但是windows下的记事本似乎默认ASIIC编码，会在linux下中文乱码，所以可以用其他编辑器代替。