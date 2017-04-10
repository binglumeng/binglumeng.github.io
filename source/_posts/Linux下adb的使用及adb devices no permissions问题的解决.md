---
title: Linux adb使用及adb devices no permissions问题解决
date: 2017-04-07 11:52:25
tags:
    - Linux
    - Android
    - adb
categories:
    - Android
---
## Linux下adb的使用及adb devices ： no permissions问题的解决

最近在Android开发过程中，需要用到linux下的开发环境，而使用adb时候遇到点小问题，特此笔记记录一下，方便自已，亦希望有助于他人。

### 1、adb的安装

本人的Linux开发环境为Ubuntu Server 16.04，有使用其他平台，如CentOS等，可灵活变通。

```sh
# 安装adb
sudo apt install adb
sudo apt install android-tools-adb
```

### 2 、no permissions

安装好adb工具之后，连接安卓设备，并开启usb调试，使用`adb devices`发现显示出来的竟然是`?????? no permissions`。

在网上查看到有相关解决方案[^1]

- 首先，在未连接Android设备的情况下，查看一下Linux下的usb，类似如下

  ```sh
  # 运行lsusb命令
  lsusb
  # 结果
  Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
  Bus 001 Device 003: ID 0bda:0129 Realtek Semiconductor Corp. RTS5129 Card Reader Controller
  Bus 001 Device 005: ID 0cf3:e005 Atheros Communications, Inc.
  Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
  ```

- 然后，打开android设备的usb调试模式，连接到Linux电脑上，运行

  ```sh
  # 运行lsusb指令，查看设备信息
  lsusb
  # 显示结果如下
  Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
  Bus 001 Device 003: ID 0bda:0129 Realtek Semiconductor Corp. RTS5129 Card Reader Controller
  Bus 001 Device 005: ID 0cf3:e005 Atheros Communications, Inc.
  Bus 001 Device 019: ID 2207:0010   # 此条新增的条目，则是新连接的android设备
  Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
  ```

  如上则可以查看到新连接的Android设备信息，注意其ID号，这里是`2207`和`0010`

- 然后`cd /etc/udev/rules.d/`目录下，查看`.rules`文件

  ```sh
  cd /etc/udev/rules.d/
  ls
  # 结果如下,名称可能不同
  51-android.rules
  # 然后编辑该文件
  sudo vim 51-android.rules
  # 然后加入如下代码
  SUBSYSTEM=="usb",ATTRS{idVendor}=="2207",ATTRS{idProduct}=="0010",MODE="0666"
  ```

  这里`2207`和`0010`则分别是上一步中查看到的android设备的额ID信息，MODE应该是表示权限。

- 重启设备

  ```sh
  sudo chmod a+rx /etc/udev/rules.d/51-android.rules
  sudo service udev restart
  ```

**至此，拔掉usb重新连接，然后在运行如下命令，便可进行adb操作**

```sh
sudo adb kill-server
sudo adb start-server
sudo devices
# 若要需要root权限进入Android设备的shell，可以运行
adb root
adb remount
adb shell
```

#### adb devices为空

若是运行`adb devices`列表为空，而`lsusb`却能看到已经连接的Android设备，此时可以

```sh
# 编辑adb_usb.ini文件
sudo vim ~/.android/adb_usb.ini
# 加入 0x0bb4 然后执行
sudo service udev restart
android update adb
```

参考文章：

[^1]: http://blog.csdn.net/chychc/article/details/7276294

