---
layout: post
title: Activity生命周期
category: blog
tags: [Android，Java]
description: Activity生命周期相关及代码实践。
---


## Back Stack

Android是使用任务（Task）来管理活动的，一组存放在栈里的活动的集合被称作返回栈（Back Stack）。当我们启动一个新的活动，他会在返回栈中入栈，当我们按下 Back键或调用 finish()方法去销毁一个活动时，处于栈顶的活动会出栈，这时前一个入 栈的活动就会重新处于栈顶的位置。系统总是会显示处于栈顶的活动给用户。

## 活动生存期及其状态

 **活动的生存期：**

  在Activity中定义了七种回调方法
  1.onCreate()：活动第一次被创建的时候调用。主要进行一些初始化的操作
  2.onStart()：在活动从不可见到可以看见的时候调用
  3.onResume()：在活动准备好与用户交互的时候调用，此时活动一定位于栈顶
  4.onPause()：系统准备启用或者恢复另一个活动的时候调用，通常在这个方法中将消耗的CPU资源释放，保存数据
  5.onStop()：在活动完全看不见的时候调用，而onPause(),在活动处于暂停状态也会调用，
  6.onDestroy()：在活动被销毁前调用。调用这个方法后，活动变为销毁状态
  7.onRestart()：在活动由停止状态，转换为运行状态时调用  

  **活动状态**  
  
  注意：Activity只能在三种状态下存在很长时间（Resumed、Paused、Stopped），其他状态是瞬态。
   
   1.运行状态：活动位于返回栈的栈顶时（系统最不愿意回收的 就是处于运行状态的活动，因为这会带来非常差的用户体验。）  

   2.暂停状态：当一个活动不再处于栈顶位置，但仍然可见时（比如某个活动上面新开了个对话框活动该活动就是暂停活动-系统也不愿意去回收这 种活动。）  

   3.停止状态：当一个活动不再处于栈顶位置，并且完全不可见，就进入了停止状态。（系统仍然会为这种活动保存相应的状态和成员变量，但很容易被回收）

   4.销毁状态：当一个活动从返回栈中移除后就变成了销毁状态（系统会最倾向于回收处于这种状 态的活动，从而保证手机的内存充足。）

完整的生命周期如图：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/android-activity.jpg?raw=true)
  
（PS：阴影部分表示进程被杀死）

## 代码实践

1.新建一个 ActivityLifeCycleTest项目默认生成主活动，和布局，修改布局文件activity_main.xml，重新定制我们主活动的布局。
```xml
  //加入两个按钮

 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 

          android:layout_width="match_parent"  

          android:layout_height="match_parent"  

          android:orientation="vertical" > 
 
       <Button      

          android:id="@+id/start_normal_activity"

          android:layout_width="match_parent"   

          android:layout_height="wrap_content"   

          android:text="Start NormalActivity" /> 
 
     <Button       

          android:id="@+id/start_dialog_activity"    

          android:layout_width="match_parent"   

         android:layout_height="wrap_content"      

         android:text="Start DialogActivity" /> 
 
  </LinearLayout>
```  

2.我们还需要分别再创建两个子活动，NormalActivity 和 DialogActivity。

   新建 normal_layout.xml文件  
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  

            android:layout_width="match_parent" 

            android:layout_height="match_parent"

            android:orientation="vertical" >   

        <TextView    

             android:layout_width="match_parent"     

             android:layout_height="wrap_content"   

             android:text="This is a normal activity"   

       /> 
</LinearLayout>
```
  新建一个 dialog_layout.xml文件  
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

          android:layout_width="match_parent"   

          android:layout_height="match_parent"   

           android:orientation="vertical" >    

        <TextView     

           android:layout_width="match_parent"     

           android:layout_height="wrap_content"    

           android:text="This is a dialog activity"       

              /> 
</LinearLayout>
```
   新建 DialogActivity继承自 Activity加载了 dialog_layout这个布局  
```java   
       public class DialogActivity extends Activity { 
 
             @Override 

            protected void onCreate(Bundle savedInstanceState) {  

                 super.onCreate(savedInstanceState); 

                 requestWindowFeature(Window.FEATURE_NO_TITLE);  

              setContentView(R.layout.dialog_layout); 

          } 
}
```
 新建 NormalActivity继承自 Activity加载了 normal_layout这个布局。  
```java
      public class NormalActivity extends Activity {  

            @Override 

                protected void onCreate(Bundle savedInstanceState) { 

                super.onCreate(savedInstanceState);  

                requestWindowFeature(Window.FEATURE_NO_TITLE);  

                setContentView(R.layout.normal_layout); 

            } 

  }
```
   在 AndroidManifest.xml注册时，将活动DialogActivity设成对话框式，将NormalActivity设为普通的活动。  
```java   
         <activity android:name=".NormalActivity" >

         </activity>

         <activity android:name=".DialogActivity" android:theme="@android:style/

         Theme.Dialog" >

         </activity>
```  

3.最后修改 MainActivity中的代码。第一个按钮会启动 NormalActivity，点击第二个按钮会启动 DialogActivity。  
```java
      public class MainActivity extends Activity { 

             public static final String TAG = "MainActivity"; 
                 @Override 

          protected void onCreate(Bundle savedInstanceState) {

                 super.onCreate(savedInstanceState); 

                 Log.d(TAG, "onCreate"); 

                 requestWindowFeature(Window.FEATURE_NO_TITLE); 

                 setContentView(R.layout.activity_main);  

                 Button startNormalActivity = (Button) findViewById(R.id.start_ normal_activity); 

                 Button startDialogActivity = (Button) findViewById(R.id.start_ dialog_activity);  

               startNormalActivity.setOnClickListener(new OnClickListener() { 

                      @Override

                    public void onClick(View v) {  

                    Intent intent = new Intent(MainActivity.this, NormalActivity.class);   

                    startActivity(intent);  

               }  

          });

                   @Override

             protected void onStart() { 

                     super.onStart();

                      Log.d(TAG, "onStart"); 

               }   

                  @Override 

             protected void onResume() {  

                  super.onResume();

                  Log.d(TAG, "onResume"); 

              }  

                 @Override

            protected void onPause() { 

                 super.onPause(); 

                 Log.d(TAG, "onPause");

           } 

               @Override

           protected void onStop() {  

                super.onStop(); 

                Log.d(TAG, "onStop");

         }  

             @Override

         protected void onDestroy() {

               super.onDestroy(); 

              Log.d(TAG, "onDestroy");

         }   

           @Override

         protected void onRestart() { 

             super.onRestart();  

             Log.d(TAG, "onRestart"); 

           } 
 
       }
```  

   **然后运行程序：** onCreate()、onStart()、onResume() 方法被执行  

   **点击第一个按钮启动 NormalActivity：** （由于 NormalActivity已经把 MainActivity完全遮挡住，）onPause()、 onStop()被执行  

   **按下 Back键返回 MainActivity：** （由于之前 MainActivity已经进入了停止状态）onRestart()、onStart()、onResume()被执行  

   **再点击第二个按钮，启动 DialogActivity：** （DialogActivity并没有完全遮挡住 MainActivity） onPause()被执行  

   **Back键返回 MainActivity：** onResume()被执行  

   **在MainActivity按下 Back键退出程序：** onPause()、onStop()、onDestroy()被执行  

##  Activity内存被回收问题

**情景1:** 应用中有一个Activity A, 用户在A的基础上启动了 B，A就进入停止状态，但是由于系统内存不足，将活动A回收掉了，此时用户按下Back键返回活动A，会出现什么情况了？   

其实依然会正常显示活动A的，只是这时不再执行onRestart()方法，而是会执行A的onCreate()方法，因为Activity A在这种情况下会被重新创建一次。（被销毁的Activity，只能被重新创建）。

**情景2:** 如果活动A中存在临时数据和状态，那在重新创建A时，该如何恢复？

Bundle提供了一些列保存数据的方法。在Activity中重写void onSaveInstanceState(Bundle outState)方法即可保存临时数据。  

```java
/* 如果Activity被回收，那么会自动调用on onSaveInstanceState(Bundle outState )方法,Bundle 类型的参数，使用 putString()方法保存字符串，使用 putInt()方法保存整型数据*/

             protected void onSaveInstanceState(Bundle outState) {

             super.onSaveInstanceState(outState);

             String tempData = "Something you just typed"; 

             outState.putString("data_key", tempData);

          }

   //使用的 onCreate()方法其实也有一个 Bundle类型的参数,将数据取出即可。

         @Override

       protected void onCreate(Bundle savedInstanceState) { 

               super.onCreate(savedInstanceState);

               Log.d(TAG, "onCreate"); 

              requestWindowFeature(Window.FEATURE_NO_TITLE);

              setContentView(R.layout.activity_main); 

              if (savedInstanceState != null) {

                   String tempData = savedInstanceState.getString("data_key");

                   Log.d(TAG, tempData); 

                 } 

               …… }

```

**参考资料**

《Android第一行代码》


