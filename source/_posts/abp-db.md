---
title: .Net开源框架ABP初探（二）— 使用Code First方式创建数据表
date: 2018-07-18 14:10:43
categories: 后端
tags: C#
---

> 初用abp可能会对它的架构各层的作用有一些迷茫，比如我们平时开发中的分层中可能会专门有一个模型层(可能命名为xxx.Model),用来存储开发中和数据库表相对于的模型映射类，可能会有一个持久层/仓储层(可能命名为xxx.Repository)来专门处理数据的持久化(即增删查改等)。所以在初使用abp时会感觉无从下手，其实在abp中这两个层的功能都被放到了.Core项目下，abp基于模块化的考虑，在.Core项目中把相同模块的一些类（包括模型和持久化）放在了同一目录。
# 1.创建模型类
- 打开.Core项目,新建新建一个项目文件夹(Clothes);
为了演示表关联及外键的使用，创建两个类：
- 创建类ClothesCategoty.cs
```
using Abp.Domain.Entities;
namespace myTest.Clothes
{
    public class ClothesCategory:Entity
    {
           public virtual string Name { get; set; }
    }
}
```
> using Abp.Domain.Entities引用Abp.Domain.Entities，abp中所有的类都继承自Abp.Domain.Entities.Entity,集成后会自动创建表主键字段Id。
- 创建类Clothes.cs
```
using System;
using System.ComponentModel.DataAnnotations.Schema;
using Abp.Domain.Entities;

namespace myTest.Clothes
{
    [Table("Clothes")]
    public class Clothes:Entity
    {
        public virtual DateTime CreationTime { get; set; }
        public virtual string PictureUrl { get; set; }
        [ForeignKey("ClothesCategoryId")]
        public virtual ClothesCategory ClothesCategory { get; set; }
    }
}
```
> [Table("xxx")]指定表名，不指定默认使用类名；[ForeignKey("xxx")]指定关联表外键的名称。

# 2.添加类到DbContext
打开.EntityFrameworkCore项目，找到xxxDbContext类，在类中加入新模型的相关代码：
```
public virtual DbSet<Clothes.Clothes> Clothes { get; set; }
public virtual DbSet<Clothes.ClothesCategory> ClothesCategory { get; set; }
```
如下图：
![](https://upload-images.jianshu.io/upload_images/9814928-93cdb997c41495c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 3.执行命令
打开NuGet程序包管理控制台，选择默认项目为.EntityFrameworkCore项目。
- 执行Add-Migration xxxx,其中xxxx可自主命名；
![](https://upload-images.jianshu.io/upload_images/9814928-7645f0fb73b1b9c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行完成后会在项目的Migrations文件夹下自动生成两个文件，如下图：
![](https://upload-images.jianshu.io/upload_images/9814928-dfe0ff48fc1e0695.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 执行“Update-Database”
### 至此，数据库表创建完成。


