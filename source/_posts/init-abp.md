---
title: Net开源框架ABP初探（一）— 项目初始化
date: 2018-07-18 11:17:43
categories: 后端
tags: C#
---

# 1.创建项目
### 进入[ABP官网](https://aspnetboilerplate.com/),点击“Get started”:
![Get started](https://upload-images.jianshu.io/upload_images/9814928-f28fcb544d1758e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 选择想要创建的项目类型：
![templates](https://upload-images.jianshu.io/upload_images/9814928-760cf716674161a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>此处我选择ASP.NET Core 2.x + Multi Page Web Application(多页面，标准的.net mvc模式)，然后选中“include login,register,user,role and tenant management pages”(选中后创建的项目会默认包含登录，注册，角色和多组合模块，此选项可选可不选，一般系统都需要这些模块，选中可以节省一些自己开发的时间，同时新手可以参照这些模块进行开发，能快速了解此框架的一些用法)。
### 创建完成
输入验证码后点击“create my project”项目创建完成并自动下载到本地。

# 2.配置项目
打开 [开发文档](https://aspnetboilerplate.com/Pages/Documents/Zero/Startup-Template-Core)，即可看到详细的开发说明。
![image.png](https://upload-images.jianshu.io/upload_images/9814928-a2137dbaa22825ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 使用vs2017 v15.3.5以上版本打开项目解决方案。
>此处应该是因为我选择了 .NET CORE 2.x的原因,选择其他框架应该不需要2017也行。

### 还原项目依赖包
>打开项目后自动还原，如果没自动则手动使用NuGet还原。

### 把Web.Mvc项目设为默认启动项目
>之后还原数据库时会需要该项目里配置的链接字符串。

### 配置数据库连接字符串
>.NET CORE程序的链接字符串在appsettings.json中配置。

### 更新数据库
- 打开“工具”->“NuGet包管理器”->“程序包管理控制台”。
- 选择“EntityFrameworkCore”项目为默认项目。
![](https://upload-images.jianshu.io/upload_images/9814928-9f125de849a86fc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 执行“Update-Database”命令。
![](https://upload-images.jianshu.io/upload_images/9814928-dced2378cbb019e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行完成后可以打开数据库看到已经自动创建了数据库和相应的表。
# 3.运行项目
运行“Web.Mvc”项目，即可看到如下登录页面。
![](https://upload-images.jianshu.io/upload_images/9814928-d73e00c8384c0180.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入默认的用户名：admin，密码：123qwe即可进入系统。
### 至此，项目初始化完成。
初探abp，感觉博大精深，后续会继续记录和分享一些个人使用过程中的一些探索和结果。和广大abp使用者一起努力，共勉.....

