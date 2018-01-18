---
title: Selenium初探--Nodejs+Selenium环境搭建及基础用法
date: 2018-01-17 16:44:08
tags: Selenium
categories: 工具
---

## 前言
Selenium多用于系统的自动化测试，但有时候也可以用来做一些类似外挂一样的东西，比如定时打开某个网站去做一些操作，在不久前的项目中由于需要定时（实际是我方系统检测到有用户充值时）去操作一个合作公司的网站完成一些操作（实际是打开网站登录管理员账号去完成充值操作，因为合作方不能提供充值接口）。在考了么macaca,appium,selenim（甚至按键精灵😄 ）等工具后最终选择了使用Nodejs+Selenium的方式完成。一路探索和尝试后完成了功能，在此做下记录和总结。

## 环境搭建
#### 1.安装 Nodejs
到[Nodejs](https://nodejs.org/en/download/)官方下载地址下载最新稳定版本Nodejs后安装。安装成功后在命令行模式使用以下命令查看是否成功。成功后会显示相应的版本信息。
>node -v

#### 2.项目初始化
创建一个文件夹（用户存放项目文件）后命令行模式进入到该文件夹下执行命令：
>npm init

#### 3.配置selenium运行环境
在上一步相同的命令行下运行以下命令至其运行安装完成。
>npm install selenium-webdriver --save

#### 4.安装浏览器驱动

- 安装Chrome驱动
>npm install chromedriver --save
- 安装Firefox驱动
>npm install geckodriver --save
- 安装IE驱动
>npm install iedriver --save

说明：出现安装驱动后运行脚本提示驱动不正常之类的问题时可自行下载驱动放到与脚本相同的目录下试试。驱动下载地址：http://www.seleniumhq.org/download/

## 基本用法
#### 一.初始化
初始化一个浏览器并打开一个网页，如下：
```
var webdriver = require('selenium-webdriver');
var driver = new webdriver.Builder()
    .forBrowser('ie')
    .build();
driver.get('http://www.baidu.com');
```
#### 二.常用方法
1. 元素定位
- 根据id定位
```
driver.findElement(By.id('ID'))；//类似于jquery的$("#id")
```
- 根据className定位
```
driver.findElement(By.className('Class'))；//类似于jquery的$(".class")
```
- 更多定位方式可参考：http://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_By.html

2. 设置元素的值
```
driver.findElement(By.id('ID')).sendKeys('value');//类似于jquery的$(".id").val("value");
```

3. 清空元素的值
```
driver.findElement(By.id('id')).clear();
```
4. 单击按钮
```
driver.findElement(By.id('id')).click();  
```
5. 元素等待
有时对一些元素需要等待页面跳转或操作完成才会显示，如果操作耗时或者网络原因，如果该元素还没出现就进行操作可能会跑出异常，这是我们需要设置一些等待，等待该元素出现在页面上时才能进行操作：
```
var until = webdriver.until;
driver.wait(until.elementLocated(By.id('id'), 10000));
...
```
6. 程序睡眠
睡眠功能解决的问题和元素等待类似，更推荐使用元素等待方法。
```
driver.sleep(500);//毫秒
```
7. 执行JavaScript
在网页上运行一段javascript,此方法在selenium的使用中非常有用，当有些时候某个元素是在难以获取时，可使用该方法直接触发该元素本身的操作，例如某个按钮点击后执行网页跳转，但是我们难以定位该元素时可以直接使用以下方式跳转：
```
driver.executeScript('location.href="/xx.html" ');
```
或者需要给元素设置值时可以使用：
```
driver.executeScript('document.getElementById("id").value="value"');//$("#id").val("value");
```
8. 执行JavaScript并获取返回值
在网页上运行JavaScript还可返回值，以供我们自动运行程序调用做出一些判断，比如可以检查网页上的某个值大于100做某种操作，小于100做另外一种操作。
```
driver.executeScript('return $("#id").val()').then(function(obj){
    //obj即为返回值
    if(obj>100){
        //操作
    }else{
       //其他操作
    }
})
```
9. 切换作用域（switchTo）
- 切换到iframe
网页中常常会嵌入一些iframe，或者是标签页面或者是弹窗的形式。这是要操作iframe里面的元素前就需把当前的作用域切换到iframe，切换后在切换会主页面前所有的操作都是针对iframe，在iframe内的操作结束后需切换回主页面。
```
driver.switchTo().frame(driver.findElement(By.id("iframe-id")));  //iframe-id为iframe元素的id
```
- 切换到弹出框
有时候一些网页会弹出一些操作提示，提示框会堵塞整个任务的执行，需将其关闭(只针对原生的js弹出框)。
```
driver.switchTo().alert().then(function(alert) {
     //检测到弹出框时执行
     //关闭alert
     return alert.dismiss();
},function(){
    //没有检测到弹出框时执行
});
```
- 切换回主页面
```
driver.switchTo().defaultContent();
```
10. 网页最大化（全屏）
```
driver.manage().window().maximize(); 
```
11. 网页截图(定位)
网页截图看上去很简单，就一行代码如下：
```
driver.takeScreenshot()；
```
截图后的结果为base64格式，可自行处理。类似这样：
```
driver.takeScreenshot().then(function(d){
    //此处d即为截图结果base64字符串，可在此自行处理
});
```
但是往往在实际应用中可能我们不需要一整个网页图片，我们值需要某部分的图片，这时候就需要换种方式了。
在网上找到了一些信息（已不记得出处）后做了整理和亲测后记录如下：
```
driver.findElement(By.className('yanzheng')).then(function(obj){
        obj.getSize().then(function(size){          
            obj.getLocation().then(function(loc){
                driver.takeScreenshot().then(function(d){
                      var data={
                            d:d,
                            width:size.width,
                            height:size.height,
                            x:loc.x,
                            y:loc.y
                      };
                    //此处省略以下两步
                    //1.提交data信息到服务器处理图片
                    //2.先根据d获取整张图片信息，再根据需要截取的元素的其实位置x,y以及长宽width,height截取相应的图片
                })
           }
        }
})
```
12. 退出程序（关闭网页）
```
driver.quit();
```

更多详细文档可参考官方文档：http://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/