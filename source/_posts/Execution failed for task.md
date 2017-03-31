---
title: "Error:Execution failed for task ':app:mergeDebugResources'"
date: 2017-02-22 10:26
tags:
    - Android Studio
categories:
    - 编程相关
---
在使用AndroidStudio时，出现
```java
Error:Execution failed for task':app:mergeDebugResources'.>Error:java.util.concurrent.ExecutionException:com.android.ide.common.process.ProcessException:
```
 异常,作为技术小白，也曾被这问题折腾了一些时间，现记录于此，亦希望于人于己皆有帮助。


##问题原因
出现这个异常是IDE抛出的异常，问题出在编译过程中，工程的资源文件有非法标识。
1、比如`string.xml`中的配置，只有key而无value，如`<string name="">abc</string>`这么就是错的，必备属性`name`就需要有个名称。
2、res文件夹中的layout和drawable等，是否命名规范，不能用数字开头，小写字母和下划线命名，而且不要有中文等。
3、还有一个关于res文件命名，在eclipse中似乎可以用`.9`文件命名带`.9.jpg`之类的后缀。而在AndroidStudio中则不可以，只能使用单一的`.jpg`后缀，不可以`.9`了。这点要注意。扯淡的是，AndroidStudio中根本没有任何定位到这个地方的错误提示。