---
title: React-Native 开发篇一Facebook快捷登录接入
date: 2018-04-04 17:18:28
categories: 前端
tags: React-Native
---

## 一.注册Facebook开发者平台账号
Facebook开发者平台地址：https://developers.facebook.com/
注册成功后登录开发者平台。
## 二.创建应用

#### 1.添加新应用
![](https://upload-images.jianshu.io/upload_images/9814928-a49c9fca9a2e3b85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/9814928-fb4cc083703ff874.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.创建成功后在应用的控制面板添加Facebook登录功能。
![image.png](https://upload-images.jianshu.io/upload_images/9814928-c33c5450478907a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
添加成功后控制面板最下角出现我的商品中包含“Facebook登录”。
![](https://upload-images.jianshu.io/upload_images/9814928-dba458160a7d6cce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3.添加平台
在应用管理菜单：“设置”=>“基本”下点击添加平台。
![](https://upload-images.jianshu.io/upload_images/9814928-624c7eb9ba82095c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
android平台填写信息如下：
![](https://upload-images.jianshu.io/upload_images/9814928-d7f97a0769f0e7e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其中包名和类名直接填写就行，只是密码散列稍微有点麻烦。
密码散列需要以下步骤生成：
1.安装ActivePerl ：  [http://www.activestate.com/activepython/downloads](http://www.activestate.com/ActivePerl)   在环境变量中配置per的bin路径到系统PATH（安装时有自动配置，选中自动配置）。
2.下载openssl ： https://code.google.com/archive/p/openssl-for-windows/downloads   在环境变量中配置openssl的bin路径到系统PATH。
3.进入CMD输入: keytool -exportcert -alias （签名的alias名字） -keystore （keystore完整路径） | openssl sha1 -binary | openssl base64
>注：步骤3中的括号实际使用时不需要！

ios平台信息如下：
![](https://upload-images.jianshu.io/upload_images/9814928-07d6acd77fb7a106.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
填写相关信息即可。

## 三.安装使用React-Native组件
组件名称：react-native-facebook-login
组件地址:https://github.com/magus/react-native-facebook-login
android集成时按照文档说明即可一步步走通：android文档地址：https://github.com/magus/react-native-facebook-login/blob/master/android/README.md
但是ios集成时发现一些文档里大概没写的东西：需要在info.plist中添加如下配置。
```
<key>FacebookAppID</key>
<string>您的FacebookAppID</string>
<key>CFBundleURLTypes</key>
<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>fb您的FacebookAppID</string>
			</array>
		</dict>
</array>
<key>FacebookDisplayName</key>
<string>AppName</string>
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>fbapi</string>
	<string>fb-messenger-api</string>
	<string>fbauth2</string>
	<string>fbshareextension</string>
</array>

```
此外FBLogin属性buttonView发现在android上可以正常使用，在ios上不能很好的实现自定义的按钮。可直接抛弃FBLogin，使用自定义按钮，然后使用FBLoginManager实现登录功能。
即：
```
FBLoginManager.loginWithPermissions(["email","user_friends"], function(error, data){
  if (!error) {
    console.log("Login data: ", data);
  } else {
    console.log("Error: ", error);
  }
})
```
其余按照文档操作说明即可！

## 四.测试
应用在未上线前测试登录是会提示没有权限，此时需要在Facebook管理页面添加测试账号：
![](https://upload-images.jianshu.io/upload_images/9814928-e64895fa711c6e9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
添加后使用该账号登录Facebook即可登录APP！