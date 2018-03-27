---
title: react-native错误处理--No bundle URL present Make sure you’re running a packager server or have included a .jsbundle file
date: 2018-03-27 12:57:40
tags: react-native
categories: 前端
---

错误信息：No bundle URL present Make sure you’re running a packager server or have included a .jsbundle file。

详情如下：

![](https://upload-images.jianshu.io/upload_images/9814928-7c9f2c0006b1eaf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我遇到此问题的原因：
在运行项目之前已经运行着另外一个项目，当前APP在启动时会自动去localhost://8081获取资源，即.jsbundle，当启动APP前已经有另外一个APP在运行时则当前正在运行的packager server并不是当前项目，所以获取到的资源就会发生错乱而导致出现该问题！

其实本来是很容易解决的问题，但有时候会忘记已经启动了另外一个项目而忘记从这方面考虑，所以纠结好久才发现问题所在。

关闭上一个项目的packager server重启当前项目目录的packager server并且reload js不能解决此问题！

### 解决办法：
保证当前运行的packager server是当前项目或者直接关闭当前运行的packager server，卸载APP重新运行react-native run-ios！

如果你遇到同样的问题却不能用此办法解决，可以参考另外一个同学的解决办法，[链接地址](https://blog.csdn.net/ghoiufyia/article/details/78783870
) 。
