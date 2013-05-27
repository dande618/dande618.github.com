---
layout: post
title: "android Service"
date: 2013-05-26 19:00
comments: true
categories: android
---
android中服务是运行在后台的东西，级别与activity差不多。既然说service是运行在后台的服务，那么它就是不可见的，没有界面的东西。你可以启动一个服务Service来播放音乐，或者记录你地理信息位置的改变，或者启动一个服务来运行并一直监听某种动作。

Service和其他组件一样，都是运行在主线程中，因此不能用它来做耗时的请求或者动作。你可以在服务中开一一个线程，在线程中做耗时动作。

<http://www.cnblogs.com/zhangdongzi/archive/2012/01/08/2316711.html>

<!-- more -->
##如何使用Service

使用Service，需要定义一个继承android.app.Service的子类；

{% codeblock lang:java %}
public class TestService extends Service {}
{% endcodeblock %}

然后在AndroidManifest.xml文件中的<application>节点里对服务进行配置:

{% codeblock lang:xml %}
<service android:name=".TestService" />
{% endcodeblock %}

并通过调用Context.startService()或Context.bindService()方法启动服务。

1. 使用startService()方法启用服务，调用者与服务之间没有关连，即使调用者退出了，服务仍然运行。

2. 使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止。

服务一般分为这两种：

1. 本地服务， Local Service 用于应用程序内部。在Service可以调用Context.startService()启动，调用Context.stopService()结束。在内部可以调用Service.stopSelf() 或 Service.stopSelfResult()来自己停止。无论调用了多少次startService()，都只需调用一次stopService()来停止。

2. 远程服务， Remote Service 用于android系统内部的应用程序之间。可以定义接口并把接口暴露出来，以便其他应用进行操作。客户端建立到服务对象的连接，并通过那个连接来调用服务。调用Context.bindService()方法建立连接，并启动，以调用 Context.unbindService()关闭连接。多个客户端可以绑定至同一个服务。如果服务此时还没有加载，bindService()会先加载它。

提供给可被其他应用复用，比如定义一个天气预报服务，提供与其他应用调用即可。

##Service的生命周期

{% img center http://img.my.csdn.net/uploads/201203/20/0_13322041884wH4.gif %}

context.startService() -> onCreate() -> onStart() -> Service running -- 调用context.stopService() -> onDestroy() 

context.bindService() -> onCreate() -> onBind() -> Service running -- 调用>onUnbind() -> onDestroy() 

- onCreate() 该方法在服务被创建时调用，该方法只会被调用一次，无论调用多少次startService()或bindService()方法，服务也只被创建一次。 onDestroy()该方法在服务被终止时调用。

- onStart() 只有采用Context.startService()方法启动服务时才会回调该方法。该方法在服务开始运行时被调用。多次调用startService()方法尽管不会多次创建服务，但onStart() 方法会被多次调用。

- onBind()只有采用Context.bindService()方法启动服务时才会回调该方法。该方法在调用者与服务绑定时被调用，当调用者与服务已经绑定，多次调用Context.bindService()方法并不会导致该方法被多次调用。onUnbind()只有采用Context.bindService()方法启动服务时才会回调该方法。该方法在调用者与服务解除绑定时被调用。

1 采用startService()启动服务

{% codeblock lang:java %}
     Intent intent = new Intent(DemoActivity.this, DemoService.class);
     startService(intent);
{% endcodeblock %}

2 采用bindService()启动

{% codeblock lang:java %}
    Intent intent = new Intent(DemoActivity.this, DemoService.class);
    bindService(intent, conn, Context.BIND_AUTO_CREATE);
   //unbindService(conn);//解除绑定
{% endcodeblock %}

<http://blog.csdn.net/bobbybear/article/details/7798043>

##intentService的使用

<http://android.tgbus.com/Android/tutorial/201106/355229.shtml>

<http://rainbow702.iteye.com/blog/1143286>

IntentService是Service类的子类，用来处理异步请求。客户端可以通过startService(Intent)方法传递请求给IntentService，IntentService通过worker thread处理每个Intent对象，执行完所有的工作之后自动停止Service。

说明：worker thread处理所有通过传递过来的请求，创建一个worker queue，一次只传递一个intent到onHandleIntent中，从而不必担心多线程带来的问题。处理完毕之后自动调用stopSelf()方法；默认实现了Onbind()方法，返回值为null；

模式实现了OnStartCommand()方法，这个方法会放到worker queue中，然后在onHandleIntent()中执行。

使用IntentService需要两个步骤：

1. 写构造函数

2. 复写onHandleIntent()方法

好处：处理异步请求的时候可以减少写代码的工作量，比较轻松地实现项目的需求

##与Service通信的方法

<http://zhangyan1158.blog.51cto.com/2487362/491358>

在android中Activity负责前台界面展示，service负责后台的需要长期运行的任务。Activity和Service之间的通信主要由IBinder负责。在需要和Service通信的Activity中实现ServiceConnection接口，并且实现其中的onServiceConnected和onServiceDisconnected方法。然后在这个Activity中还要通过如下代码绑定服务:

{% codeblock lang:java %}
Intent intent = new Intent().setClass(this,IHRService.class);
bindService(intent,this,Context.BIND_AUTO_CREATE);
{% endcodeblock %}

当调用bindService方法后就会回调Activity的onServiceConnected，在这个方法中会向Activity中传递一个IBinder的实例，Acitity需要保存这个实例。代码如下:

{% codeblock lang:java %}
public void onServiceConnected( ComponentName inName , IBinder serviceBinder) {  
    if ( inName.getShortClassName().endsWith( "IHRService" ) ) {  
    try {  
        this.serviceBinder= serviceBinder;  
        mService = ( (IHRService.MyBinder) serviceBinder).getService();  
        //mTracker = mService.mConfiguration.mTracker;  
        } catch (Exception e) {}  
    }  
}  
{% endcodeblock %}

在Service中需要创建一个实现IBinder的内部类(这个内部类不一定在Service中实现，但必须在Service中创建它)。

{% codeblock lang:java %}
public class MyBinder extends Binder {  
//此方法是为了可以在Acitity中获得服务的实例  
    public IHRService getService() {  
        return IHRService.this;  
    }  
//这个方法主要是接收Activity发向服务的消息，data为发送消息时向服务传入的对象，replay是由服务返回的对象  
    public boolean onTransact( int code , Parcel data , Parcel reply , int flags ) {  
        //called when client calls transact on returned Binder  
        return handleTransactions( code , data , reply , flags );  
    }  
}  
{% endcodeblock %}

然后在Service中创建这个类的实例：

{% codeblock lang:java %}
public IBinder onBind( Intent intent ) {
    IBinder result = null;
    if ( null == result ) result = new MyBinder();
    return result;
}  
{% endcodeblock %}

这时候如果Activity向服务发送消息，就可以调用如下代码向服务端发送消息：

{% codeblock lang:java %}
inSend = Parcel.obtain();
serviceBinder.transact( inCode , inSend , null , IBinder.FLAG_ONEWAY );
{% endcodeblock %}

这种方式是只向服务端发送消息,没有返回值的。如果需要从服务端返回某些值则可用如下代码：

{% codeblock lang:java %}
result = Parcel.obtain();
serviceBinder.transact( inCode , inSend , result , 0 );
return result;
{% endcodeblock %}

发送消息后IBinder接口中的onTransact将会被调用。在服务中如果有结果返回(比如下载数据)则将结果写入到result参数中。在Activity中从result中读取服务执行的结果。

上面只是描述了如何由Acitity向Service发送消息，如果Service向Activity发送消息则可借助于BroadcastReceiver实现。

##跨进程调用Service

跨进程调用Service，需要先定义一个远程调用接口，然后为该接口提供一个实现类。使用AIDL（Android Interface Definition Language）来定义远程接口。

1. 在Eclipse Android工程的Java包目录中建立一个扩展名为aidl的文件。该文件的语法类似于Java代码，但会稍有不同。

2. 如果aidl文件的内容是正确的，ADT会自动生成一个Java接口文件（*.java）。

3. 建立一个服务类（Service的子类）。

4. 实现由aidl文件生成的Java接口。

5. 在AndroidManifest.xml文件中配置AIDL服务，尤其要注意的是，<action>标签中android:name的属性值就是客户端要引用该服务的ID，也就是Intent类的参数值。

##调用系统Service

###Android电话信息相关API

<http://blog.csdn.net/leixingjing/article/details/6860337>

Android平台提供的电话信息系统管理功能，主要包括：获取电话信息（设备信息、SIM信息以及网络信息）、侦听电话状态（呼叫状态、服务状态、信号强度状态等）和调用电话拨号器。

###TelephoneManager类

在Manifest中添加Permission ：

{% codeblock %}
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
{% endcodeblock %}

{% codeblock lang:java %}
//TelephonyManager的获得：
TelephonyManager tm = (TelephonyManager) this.getSystemService(TELEPHONY_SERVICE);
{% endcodeblock %}

###SmsManager

在Manifest中添加Permission ：

{% codeblock lang:xml %}
<uses-permission android:name="android.permission.SEND_SMS"/>
{% endcodeblock %}

{% codeblock lang:java %}
SmsManager manager = SmsManager.getDefault();   //获得默认的消息管理器
ArrayList<String> list = manager.divideMessage(String txt);  //拆分长短信
manager.sendTextMessage(String phone,null,String content,null,null);  //发送短信
{% endcodeblock %}

<http://home.cnblogs.com/group/topic/49026.html>