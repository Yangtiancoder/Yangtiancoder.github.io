---
layout: post
title: 浅谈Android广播
category: blog
tags: [Android，Java]
description: Android广播及代码实践。
---


## Android中的广播类型

Android中的广播主要可以分为两种类型,标准广播和有序广播。标准广播(Normal broadcasts)是一种完全异步执行的广播,在广播发出之后,所有的 广播接收器几乎都会在同一时刻接收到这条广播消息,有序广播(Ordered broadcasts)则是一种同步执行的广播,在广播发出之后,同一时刻 只会有一个广播接收器能够收到这条广播消息。

## 系统广播  

 系统广播通过广播接收器的方式，主要有动态注册和静态注册两种方法。

 **动态注册**  

 动态注册的主要步骤如下：

 -  在 MainActivity中定义一个内部类继承自BroadcastReceiver

 -  在 onCreate() 方法中注册

 -  在 onDestroy() 方法中取消注册

 ```java
 package com.example.broadcasttest;

 import android.content.BroadcastReceiver;
 import android.content.Context;
 import android.content.Intent;
 import android.content.IntentFilter;
 import android.net.ConnectivityManager;
 import android.net.NetworkInfo;
 import android.support.v4.content.LocalBroadcastManager;
 import android.support.v7.app.AppCompatActivity;
 import android.os.Bundle;
 import android.view.View;
 import android.widget.Button;
 import android.widget.Toast;

 public class MainActivity extends AppCompatActivity {

    private IntentFilter intentFilter;
    private LocalReceiver localReceiver;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        intentFilter = new IntentFilter();//创建IntentFilter实例
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");//确定接收的广播种类
        NetworkChangeRecierver = new NetworkChangeRecierver();//创建广播接收器实例
        registerReceiver(NetworkChangeRecierver, intentFilter); // 注册本地广播监听器，这样LocalReceiver就会收到所有值为android.net.conn.CONNECTIVITY_CHANGE的广播，也就实现了监听网络变化的功能
    }
    /**
    *取消注册
    */
    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(NetworkChangeRecierver);//取消注册
    }

    /**
     * 这个是广播类继承了BroadcastReceiver类
     */
    class NetworkChangeRecierver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            //getSystemService()方法得到ConnectivityManager的实例，这是一个系统服务，专门用于管理网络连接的。
            ConnectivityManager connectionManager = (ConnectivityManager)getSystemService(Context.CONNECTIVITY_SERVICE);
            //getActiveNetworkInfo()方法得到NetworkInfo实例
            NetworkInfo networkInfo = connectionManager.getActiveNetworkInfo();
            //NetworkInfo的isAvailable()方法用以判断当前是否网络
            if (networkInfo != null && networkInfo.isAvailable()) {
                Toast.makeText(context, "network is available",
                        Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(context, "network is unavailable",
                        Toast.LENGTH_SHORT).show();
            }
        }
    }
 }

 ```
 以上代码便实现的是动态监听网络的变化，但是在android中会涉及到一些权限问题，因此涉及到网络权限的访问，我们需要在AndroidManifest.xml中生命如下
 ```xml
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
 ```
 动态注册的广播接收器虽然可以自由地控制注册和注销，但是必须在程序启动之后才能接收到广播，因为注册的逻辑是写在onCreate()方法中，如果想让程序在未启动的情况下就能接收到广播，就需要静态注册了。现在我们让程序接收一条开机广播，当收到这个条广播时就可以在onReceive（）方法里执行相应的逻辑，从而实现开机启动的功能。

 **静态注册**
 
 我们可以通过AS中的快捷操作来完成，右键项目New-Other-BroadcastReceiver，按操作创建一个BootCompleteReceiver

 同样修改BootCompleteReceiver中的代码如下：
 ```java
public class BootCompleteReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context,"Boot Complete",Toast.LENGTH_LONG).show();
    }
}

 ```
 静态注册在AndroidManifest.xml中，我们会发现系统已经为我们做出了注册，我们修改如下：
 ```xml
 <?xml version="1.0" encoding="utf-8"?>
 <manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.broadcasttest">

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!--开机广播权限-->
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!--注册的广播-->
        <receiver
            android:name=".BootCompleteReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <!--接收的广播类型-->
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
    </application>
 </manifest>
 ```  
  由于Android系统启动完成后会发出一条值为android.permission.RECEIVE_BOOT_COMPLETED的广播，因此我们 标签里添加了相应的action。  监听系统开机广播也是需要声明权限的，我们使用标签又加入一条android.permission.RECEIVE_BOOT_COMPLETED权限。将模拟器重新启动就可以收到开机广播了。

  **small注意：** 不要在onReceive()方法中添加过多的逻辑或者进行任何的耗时操作，因为在广播接收器中是不允许开启线程的，当onReceive方法运行较长时间而没有结束时，程序就会报错。所以广播接收器更多的是扮演一种打开程序其他组件的角色，比如创建一条状态栏通知，或者启动一个服务等。
  
## 自定义广播

  **标准广播**
  
  -  定义一个广播接收器，新建一个MyBroadcastReceiver，代码：
  ```java
  public class MyBroadcastReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context,"received in MyBroadcastReceiver",Toast.LENGTH_SHORT).
        show();
    }
 }
  ```
  -  以上是通过快捷方式创建的广播接收器，所以配置文件里面已经有了receiver标签，修改标签内容，即添加过滤者，代码：
  ```xml
  <receiver
    android:name=".MyBroadcastReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
        <action android:name="com.beidou.broadcasttest.MY_BROADCAST">
        </action>
    </intent-filter>
 </receiver>
  ```
  这里让MyBroadcastReceiver接收一条值为com.beidou.broadcasttest.MY_BROADCAST的广播，因此待会在发送广播的时候，我们需要发出这样的一条广播。

  -  修改activity_main.xml以及MainActivity中的代码：
  ```java
  Button button = (Button) findViewById(R.id.button);
  button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Intent intent = new Intent("com.beidou.broadcasttest.MY_BROADCAST");
        sendBroadcast(intent);
    }
 });
  ```
  ```xml
  <Button
    android:id="@+id/button"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="send broadcast!" />
  ```

  总结：
  1. 首先构建出了一个Intent对象，并把要发送的广播的值传入。 
  2. 然后调用了Context的sendBroadcast方法将广播发送出去，这样所有监听com.beidou.broadcasttest.MY_BROADCAST这条广播的广播接收器就会受到信息。 
  3. 由于广播是使用Intent进行传递的，因此你还可以在Intent中携带数据传递给广播接收器。

  **有序广播**  

  -  调用sendOrderedBroadcast()方法发送广播// 发送有序广播广播.第一个参数是intent,第二个参数是与权限有关的字符串，这里传入null
  -  注册广播时需要定义顺序值android:priority，代码：
  ```xml
  <receiver
    android:name=".MyBroadcastReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="100"><!--这里确定该广播接收器接收广播的优先级，值越大越先接收-->
        <action android:name="com.example.broadcasttest.MY_BROADCAST"/>
    </intent-filter>
 </receiver>

  ```
  -  在广播接收器的onReceive()中调用abortBroadcast()发放截断有序广播，代码如下：
 ```java
 package com.example.broadcasttest;
 import android.content.BroadcastReceiver;
 import android.content.Context;
 import android.content.Intent;
 import android.widget.Toast;

 public class MyBroadcastReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "received in MyBroadcastReceiver", Toast.LENGTH_SHORT).show();
        abortBroadcast();//截断有序广播，这之后的广播接收器就收不到广播
    }
 }
 ```

## 本地广播

 只在程序内部传播的广播，避免安全性问题,而且本地广播无法通过静态注册的方式接收。本地广播使用LocalBroadcastManager类来对广播进行管理，并提供了发送广播和注册广播接收器的方法：
 ```java
 package com.example.broadcasttest;
 import android.content.BroadcastReceiver;
 import android.content.Context;
 import android.content.Intent;
 import android.content.IntentFilter;
 import android.net.ConnectivityManager;
 import android.net.NetworkInfo;
 import android.support.v4.content.LocalBroadcastManager;
 import android.support.v7.app.AppCompatActivity;
 import android.os.Bundle;
 import android.view.View;
 import android.widget.Button;
 import android.widget.Toast;

 public class MainActivity extends AppCompatActivity {

    private IntentFilter intentFilter;
    private LocalReceiver localReceiver;
    private LocalBroadcastManager localBroadcastManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        localBroadcastManager = LocalBroadcastManager.getInstance(this); // 获取实例
        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent("com.example.broadcasttest.LOCAL_BROADCAST");
                localBroadcastManager.sendBroadcast(intent); // 发送本地广播
            }
        });
        intentFilter = new IntentFilter();
        intentFilter.addAction("com.example.broadcasttest.LOCAL_BROADCAST");
        localReceiver = new LocalReceiver();
        localBroadcastManager.registerReceiver(localReceiver, intentFilter); // 注册本地广播监听器
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        localBroadcastManager.unregisterReceiver(localReceiver);//注销本地广播
    }

    /**
     * 这个是广播类继承了BroadcastReceiver类
     */
    class LocalReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context, "received local broadcast", Toast.LENGTH_SHORT).show();
        }

    }
 }

 ```


**参考资料**

《Android第一行代码》


