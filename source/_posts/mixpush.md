---
title: React-Native 消息推送--Android混合推送
date: 2018-01-09 14:10:00
categories: 前端
tags: React-Native
---
# 背景

由于使用任何一种Android推送都很难在APP进程被杀死后收到推送，只有集成各厂商提供的系统级别推送才能完成此任务,故考虑小米、华为、魅族手机使用官方推送，其他手机使用个推推送！

项目GitHub地址：

https://github.com/wangheng3751/react-native-mixpush

# 安装：

    npm install --save react-native-mixpush-android

# 配置使用：

## 1、配置android/settings.gradle

```
    ...
    include ':react-native-mixpush-android'
    project(':react-native-mixpush-android').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-mixpush-android')
```

## 2、配置app/build.gradle

```
    manifestPlaceholders = [
            PACKAGE_NAME : "你的包名",
            //测试环境
            GETUI_APP_ID : "个推APPID",
            GETUI_APP_KEY : "个推APPKEY",
            GETUI_APP_SECRET : "个推APPSECRE"
    ]
    dependencies {
        ...
        compile project(":react-native-mixpush-android")
    }
```

## 3、配置android/build.gradle

```
allprojects {
         repositories {
             mavenLocal()
             jcenter()
             ...
             //个推
             maven {
                 url "http://mvn.gt.igexin.com/nexus/content/repositories/releases/"
             }
             //华为推送
             maven {url 'http://developer.huawei.com/repo/'}
         }
 }
```

## 4、配置AndroidManifest.xml

### manifest节点下添加：

```
        <!--小米推送-->
         <permission android:name="${PACKAGE_NAME}.permission.MIPUSH_RECEIVE" android:protectionLevel="signature" />
         <uses-permission android:name="${PACKAGE_NAME}.permission.MIPUSH_RECEIVE" />
         <!--小米推送END-->

         <!--魅族推送-->
         <!-- 兼容flyme5.0以下版本，魅族内部集成pushSDK必填，不然无法收到消息-->
         <uses-permission android:name="com.meizu.flyme.push.permission.RECEIVE"></uses-permission>
         <permission android:name="${PACKAGE_NAME}.push.permission.MESSAGE" android:protectionLevel="signature"/>
         <uses-permission android:name="${PACKAGE_NAME}.push.permission.MESSAGE"></uses-permission>
         <!--  兼容flyme3.0配置权限-->
         <uses-permission android:name="com.meizu.c2dm.permission.RECEIVE" />
         <permission android:name="${PACKAGE_NAME}.permission.C2D_MESSAGE"
             android:protectionLevel="signature"></permission>
         <uses-permission android:name="${PACKAGE_NAME}.permission.C2D_MESSAGE"/>
         <!--魅族推送END-->
```

### application节点下添加：

```
        <!--华为推送配置begin-->
        <meta-data   android:name="com.huawei.hms.client.appid"  android:value="你的华为推送APPID"/>
        <provider android:name="com.huawei.hms.update.provider.UpdateProvider"
            android:authorities="${PACKAGE_NAME}.hms.update.provider"
            android:exported="false"
            android:grantUriPermissions="true" >
        </provider>
```

## 5、注册推送

### MainApplication中引用组件：

```
  import com.duanglink.rnmixpush.MixPushReactPackage;

  protected List<ReactPackage> getPackages() {
          return Arrays.<ReactPackage>asList(
              new MainReactPackage(),
              ...
              new MixPushReactPackage()
          );
        }
   };
```

### MainActivity中注册推送：

``` 
  import com.duanglink.huaweipush.HuaweiPushActivity;
    
  //特别注意：此处继承HuaweiPushActivity 
  public class MainActivity extends HuaweiPushActivity {
        @Override
        public void onCreate(Bundle savedInstanceState) {
            if(savedInstanceState==null){
                savedInstanceState=new Bundle();
            }
            savedInstanceState.putString("meizuAppId","魅族AppId");
            savedInstanceState.putString("meizuAppKey","魅族AppKey");
            savedInstanceState.putString("xiaomiAppId","小米AppId");
            savedInstanceState.putString("xiaomiAppKey","小米AppKey");
            super.onCreate(savedInstanceState);
        }
        ...
    }
```

## 6、React-Native客户端接收事件：

``` 
var { NativeAppEventEmitter } = require('react-native');
NativeAppEventEmitter.addListener(
            'receiveRemoteNotification',
            (notification) => {
                    Alert.alert('消息通知',JSON.stringify(notification));
            }
);
```

## 7、React-Native客户端方法说明：

```
import MixPush from 'react-native-mixpush-android';
- MixPush.setAlias(alias); //设置别名
- MixPush.unsetAlias(alias); //取消设置别名
- MixPush.setTags(tags); //设置用户标签
- MixPush.unsetTags(tags); //取消设置用户标签
- MixPush.getClientId(); //获取客户端ID
```


>以上方法均不支持华为手机
    
说明：getClientId获取到的ID为用户在推送平台的唯一标识（小米：regId，魅族：pushId;个推：clientId），用于定向推送;
    
此外,所有推送平台在APP推送注册成功后会往客户端发送一次注册成功事件(包含华为:deviceToken)，事件名为:"receiveClientId",并携带clientId,可使用该事件与getClientId方法配合使用达到获取clientId的目的。
    
实例：

```
    //主动获取<br>
    MixPush.getClientId((cid)=>{
       alert("cid:"+cid);//自行处理cid代码
    });
    
    //监听事件<br>
    NativeAppEventEmitter.addListener(
        'receiveClientId',
        (cid) => {
             alert("cid:"+cid);//自行处理cid代码
        }
    );
```

# 说明

后台推送使用各平台后台管理系统或自行集成SDK。

>项目QQ交流群：516032289
