---
title: 在使用vue+webpack模版创建的项目中使用font-awesome
date: 2018-03-22 11:27:59
tags: vue
categories: 前端
---

前言：最近使用vue+webpack进行一个小项目的开发，按照[官方模版文档](https://vuejs-templates.github.io/webpack/)完成项目初始化后打算引入ont-awesome字体图标库进行使用，引入过程中遇到一些问题并解决，现记录如下。

一开始进展很顺利，百度了基本用法后安装：
>npm install font-awesome --save

然后在main.js中引入：
>import 'font-awesome/css/font-awesome.css'

接下来就开始愉快的使用各种图标了：
> <i class="fa fa-search"></i>   
 <i class="fa fa-star"></i>   

npm run dev   之后效果也如预期般顺利，这样的：
![image.png](https://upload-images.jianshu.io/upload_images/9814928-68b60ed6333916a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然而，当我npm run build后生成dist文件夹，并部署之后发现情况改变了！
![](https://upload-images.jianshu.io/upload_images/9814928-dcc7f433d98b8324.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
图标不见了！！

经过一些调试之后发现是webpack打包之后生成的css文件对字体的引用路径有问题：url(static/fonts/...)应该是url(../static/fonts/...)这样才能正常。
![](https://upload-images.jianshu.io/upload_images/9814928-c085748a7b8e317f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

于是继续问伟大的度娘！
找到了一篇文章中午看到了曙光了（[原文连接](https://www.cnblogs.com/mybky/p/7098452.html)），正确的步骤应该如下：
### 1.安装依赖包
>npm install font-awesome-loader less less-loader css-loader style-loader file-loader font-awesome -save

### 2.配置font-awesome-loader
打开项目目录build/webpack.base.conf配置font-awesome-loader如下：
![](https://upload-images.jianshu.io/upload_images/9814928-ccbabb496357fdb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
开始运行npm run build，有一个错误来的措手不及！
![](https://upload-images.jianshu.io/upload_images/9814928-b3a963c733de94aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
还好，根据错误提示运行安装一下node-sass，所以：
### 3.安装node-sass
>npm install node-sass@latest

此时，再执行npm run build时终于看见梦寐以求的效果，打包成功了。部署后运行也成了。
![](https://upload-images.jianshu.io/upload_images/9814928-f5e9f827122bbe17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
至此，可以随意使用font-awesome图标库了！