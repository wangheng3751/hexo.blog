---
title: React-Native 自定义Android原生组件--弹出提示框
categories: 前端
tags: React-Native
---

特别说明：本文仅适合未接触过或不熟悉Android原生开发的React-Native新手开发者！

背景：使用React-Native开发Android APP时，虽然React-Native已经自带弹出提示框组件Alert，但由于各大手机厂商系统风格各异，在使用React-Native自带的弹出提示组件时也风格不一，并且大部分机型的弹出框样式比较难看，或者和APP的整体风格设计不协调，当然也可以使用Modal来实现弹出框，但是又得在所有需要的View添加Modal相应标签，较为繁琐，所以本文介绍一个简单的自定义弹出框的基本步骤。

效果预览：![](https://raw.githubusercontent.com/wangheng3751/my-resources/master/images/android-confirm.jpg)

### 一、设计布局文件
弹出框多为提示框（alert），确认框（confirm）本实例展示一个简单的确认框（即包含“确认”和“取消”按钮）。具体的文字，颜色，背景色等按需修改为和UI设计风格较为统一的即可（可在Android Studio 中可视化设计）！
``` 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:background="#fff"
    android:paddingBottom="15dp">
    <TextView
        android:id="@+id/title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#fff"
        android:gravity="center"
        android:padding="5dp"
        android:text="温馨提示"
        android:textSize="16sp"
        android:textColor="#f18518" />
    <View
        android:layout_width="match_parent"
        android:layout_height="0.5dp"
        android:background="#f18518">
    </View>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:text="您的提示信息，后期从js端传入！"
        android:id="@+id/text_msg"
        android:textSize="15sp"
        android:textColor="#000"
        android:layout_gravity="center_horizontal"
        android:layout_marginLeft="25dp"
        android:layout_marginTop="12dp"
        android:layout_marginRight="25dp"
        android:layout_marginBottom="15dp" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="30dp"
        android:orientation="horizontal">
        <Button
            android:id="@+id/btn_cancel"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#FDEEDF"
            android:text="取消"
            android:textColor="#f08519"
            android:topRightRadius="20dp"
            android:textSize="15sp"
            android:layout_marginLeft="25dp"
            android:layout_marginRight="5dp" />
        <View
            android:layout_width="10dp"
            android:layout_height="match_parent"
            android:background="#fff">
        </View>
        <Button
            android:id="@+id/btn_comfirm"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#f08519"
            android:textColor="#fff"
            android:text="确定"
            android:textSize="15sp"
            android:layout_marginLeft="5dp"
            android:layout_marginRight="25dp" />
    </LinearLayout>
</LinearLayout>
``` 
### 二、添加Moudle类

该类为已设计的弹出框提供提供React-Native调用入口。
``` 
package com.xxx.xxx;//您的包名
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.util.DisplayMetrics;
import android.view.View;
import android.view.Window;
import android.view.WindowManager;
import android.widget.Button;
import android.widget.TextView;
import com.facebook.react.bridge.Callback;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import com.facebook.react.modules.core.DeviceEventManagerModule;

public class xxxModule extends ReactContextBaseJavaModule {
    private AlertDialog dialog=null;
    private static ReactApplicationContext mRAC;
    public  xxxModule(ReactApplicationContext reactContext) {
        super(reactContext);
        mRAC=reactContext;
    }

    @Override
    public String getName() {
        return "xxxModule";
    }

    @Override
    public boolean canOverrideExistingModule() {
        return true;
    }

    //弹出确认取消按钮框
    @ReactMethod
    public void openConfirm(String message,String title, final Callback okBtnBack,final Callback cancelBack) {
        if (message == null || message.equals("")) return;
        Activity activity=getCurrentActivity();
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        View view = View.inflate(activity, R.layout.my_dialog, null);
        builder.setView(view);
        builder.setCancelable(true);
        if (title != null && !title.equals("")) {
            TextView tit= (TextView) view.findViewById(R.id.title);//设置标题
            tit.setText(title);
        }
        TextView input_edt= (TextView) view.findViewById(R.id.text_msg);//显示内容
        input_edt.setText(message);
        //取消按钮点击事件
        Button btn_cancel=(Button)view.findViewById(R.id.btn_cancel);//取消按钮
        btn_cancel.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v) {
                if(dialog!=null) dialog.dismiss();
                if(cancelBack!=null ) cancelBack.invoke();
            }
        });
        //确定按钮点击事件
        Button btn_comfirm=(Button)view.findViewById(R.id.btn_comfirm);//确定按钮
        btn_comfirm.setOnClickListener(
                new View.OnClickListener(){
                    @Override
                    public void onClick(View v) {
                        if(dialog!=null) dialog.dismiss();
                        if(okBtnBack!=null ) okBtnBack.invoke();
                    }
                }
        );
        dialog = builder.create();
        dialog.setCancelable(false);
        dialog.show();
        //设置弹出框宽度
        Window dialogWindow = dialog.getWindow();
        WindowManager.LayoutParams lp = dialogWindow.getAttributes();
        WindowManager m =activity.getWindowManager();
        DisplayMetrics metrics = new DisplayMetrics();
        m.getDefaultDisplay().getMetrics(metrics);
        lp.width = (int) (metrics.widthPixels * 0.8);//宽度
        //lp.height = (int) (metrics.heightPixels * 0.8);//高度
        dialogWindow.setAttributes(lp);
    }

    //关闭确认取消按钮框
    @ReactMethod
    public void  closeConfirm(){
        if(dialog!=null) dialog.dismiss();
    }
}
``` 
### 三、通过Package类注册Moudle方法
>如果没有则创建xxxPackage类

Package可用于管理所有的原生模块的注册。
``` 
package com.xxxxx.xxxx;//您的包名
import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.JavaScriptModule;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class  xxxPackage implements ReactPackage {

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }

    @Override
    public List<NativeModule> createNativeModules(
            ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        ...//其他Module
        modules.add(new xxxModule(reactContext));//您的Moudle
        return modules;
    }
}
``` 

### 四、在MainApplication中注册
``` 
 protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
            ...,//其他包
            new xxxPackage() //您在第三步中创建或使用的Package类
      );
    }
``` 
### 五、前端(React-Native)调用
``` 
 var xxxModule = NativeModules.xxxModule;//xxxModule为第二步中的Moudle类中的getName方法的返回值

 xxxModule .openConfirm("提示信息","标题",()=>{
    //“确认”按钮点击回调方法
  },()=>{
   //“取消”按钮点击回调方法
 });
``` 
