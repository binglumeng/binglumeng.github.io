---
title: Android系统源码编译 unsupported reloc 43 和 make update-api问题
date: 2017-04-10 16:08
tags:
    - Linux
    - Android
    - make
categories:
    - Android
---
## Android系统源码编译 unsupported reloc 43 和 make update-api

最近初次接触到Android系统源码的编译工作，公司内使用的开发板是RK3288平台的。众所周知，Android是一个开源、开放的系统平台，国内的众多所谓的智能系统好多都是基于Android平台裁剪定制而来的。

无可厚非，Android的开放性却也带来的一些副作用，各类修改版的Android系统在使用和开发过程中，就会出现各种不同的适配问题，最让开发者头疼。

#### 1、Android源码编译环境

根据google官方的要求，推荐使用ubuntu系统平台、openJDK作为java环境，然后添加相关依赖，基本上都可以配置成功。此处提供两个参考文章:

- [Android 系统源码编译环境配置](http://blog.csdn.net/binglumeng/article/details/54342462)
- [Google 官方文档](https://source.android.com/source/requirements)

#### 2、unsupported reloc 43

在编译RK3288系统源码时候，使用`sudo ./mk.sh -s`不久，便会出现`failed`提示编译失败；分析问题需要注意的事项：

- 要使用对应与Android平台的相应openjdk，比如4.4的要使用openjdk6，而5.1的就需要openjdk7.

- 再看一下当前使用的jdk是否是需要的版本，也许安装了多个版本的jdk

  ```sh
  sudo update-alternatives --config java  //选择序号回车即可  
  sudo update-alternatives --config javac  //选择序号回车即可  
  sudo update-alternatives --config javap  //选择序号回车即可  
  ```

- 修改HOST_x86_common.mk

  ```sh
  cd SourcePath/build/core/clang/
  sudo vim ./HOST_x86_common.mk
  # 在如下文档中添加 -B$($(clang_2nd_arch_prefix)HOST_TOOLCHAIN_FOR_CLANG)/x86_64-linux/bin \

  ifeq ($(HOST_OS),darwin)
  # nothing required here yet
  endif

  ifeq ($(HOST_OS),linux)
  CLANG_CONFIG_x86_LINUX_HOST_EXTRA_ASFLAGS := \
    --gcc-toolchain=$($(clang_2nd_arch_prefix)HOST_TOOLCHAIN_FOR_CLANG) \
    --sysroot=$($(clang_2nd_arch_prefix)HOST_TOOLCHAIN_FOR_CLANG)/sysroot \
    -B$($(clang_2nd_arch_prefix)HOST_TOOLCHAIN_FOR_CLANG)/x86_64-linux/bin \
    -no-integrated-as

  CLANG_CONFIG_x86_LINUX_HOST_EXTRA_CFLAGS := \
    --gcc-toolchain=$($(clang_2nd_arch_prefix)HOST_TOOLCHAIN_FOR_CLANG) \
    -no-integrated-as
  ```

  对于Android7.0以下的，需要保留`-no-integrated-as`这句指令。

**注：**一般情况修改如上`HOST_x86_common.mk`文件即可解决此问题，有时候又不行，那么可以在修改以下两个文件：

- Android.common_build.mk

  ```sh
  # 找到Android.common_build.mk文件，搜索到 ifneq ($(WITHOUT_HOST_CLANG,true))这句话
  cd SourcePath/art/build/
  sudo vim Android.common_build.mk
  ## 修改 ifneq ($(WITHOUT_HOST_CLANG,true))中的true 为false
  # Host.
  ART_HOST_CLANG := false
  ifneq ($(WITHOUT_HOST_CLANG),false)
    # By default, host builds use clang for better warnings.
    ART_HOST_CLANG := true
  endif
  ```

- 替换`ld`文件

  ```sh
  # 替换源码中的ld文件为Ubuntu系统本身的ld.gold
  sudo cp usr/bin/ld.gold SourcePath/prebuilts/gcc/linux-x86/host/x86-linux-glibc2.11-4.6/x86_64-linux/bin/ld
  ```

  **注意：以上用`SourcePath`代之你的源码所在的根目录**

如此在编译时候，又会报出`failed,javadoc @hide`之类的错误，提示要么`@hide`添加好多注解，要么就`make update-api`，若是提示`javadoc`文件比较少的话，可以逐一添加`@hide`注解，但是太多的话，我选择了`make update-api`命令。

若是不先处理掉`unsupported reloc 43`这个错误，那么`javadoc`这个错误，怎么也处理不好，至少我遇到的情况是这样的。

希望这点小小笔记，记录个人编译过程踩过的坑的同时，能够帮助其他朋友免去这个坑的困扰。