---
title: .Net开源框架ABP初探（三）—  使用Mysql数据库
date: 2018-12-27 11:30:40
categories: 后端
tags: C#
---

# 本文针对abp .net core模版项目。
ABP 项目初始化完成后默认使用的是SQL SERVER数据库，如果想使用MYSQL数据库，需要进行一些配置。
# 1.添加mysql程序包
在.EntityFrameworkCore项目下添加程序包：
EntityFrameworkCore.MySql和EntityFrameworkCore.MySql.Design。

![](https://upload-images.jianshu.io/upload_images/9814928-33d09c011b5931f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 2.修改源代码
修改EntityFrameworkCore项目下xxxxDbContextConfigurer.cs文件内容如下：

```
public static void Configure(DbContextOptionsBuilder<OSMSDbContext> builder, string connectionString)
 {
      //builder.UseSqlServer(connectionString);
      builder.UseMySql(connectionString);
 }

 public static void Configure(DbContextOptionsBuilder<OSMSDbContext> builder, DbConnection connection)
  {
      //builder.UseSqlServer(connection);
       builder.UseMySql(connection);
  }
```

# 3.修改数据库连接字符串
修改.Migrator和.Mvc项目下的appsettings.json文件的下数据库配置信息如下：
```
"ConnectionStrings": {
    //"Default": "Server=localhost; Database=xxxDb; Trusted_Connection=True;"
    "Default": "Server=localhost; Database=xxxDb; User ID=root; Password=*****; port=3306"
  }
```

# 4.生成迁移文件
#### Windows系统下的操作：
打开VS的包管理控制台，并在包管理控制台中选择 .EntityFrameworkCore 项目作为默认项目。然后在控制台中执行下面命令生成迁移文件：
> Add-Migration "AbpZero_Initial"

然后使用下面命令来创建数据库：
> Update-Database

#### Mac系统下的操作：
- 在终端打开.EntityFrameworkCore项目：右键项目=》工具=》在终端打开；
- 执行以下命令生产迁移文件；
> dotnet ef migrations add "AbpZero_Initial"
- 执行以下命令完成数据库创建
 > dotnet ef database update

顺利完成收可在数据库中查看数据库是否创建成功。

# 5.运行项目
设置.mvc项目为启动项目运行，网站运行后输入账号admin，密码123qwe即可登陆。
![](https://upload-images.jianshu.io/upload_images/9814928-c89825cb01bb2cc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 参考文献

- 1. [ABP .Net Core Entity Framework迁移使用MySql数据库](http://www.sohu.com/a/213814486_468635)
- 2. [visual studio for Mac如何执行nuget命令？](https://segmentfault.com/q/1010000011454619/a-1020000011647459)


