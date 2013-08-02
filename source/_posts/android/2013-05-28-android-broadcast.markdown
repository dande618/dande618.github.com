---
layout: post
title: "Android Broadcast"
date: 2013-05-28 16:59
comments: true
categories: android
---
转载 <http://www.cnblogs.com/playing/archive/2011/03/23/1992030.html>

在 Android 中使用 Activity, Service, Broadcast, BroadcastReceiver

活动(Activity) - 用于表现功能

服务(Service) - 相当于后台运行的 Activity

广播(Broadcast) - 用于发送广播

广播接收器(BroadcastReceiver) - 用于接收广播

<span style="color:red">Intent - 用于连接以上各个组件，并在其间传递消息</span>

<!-- more -->

========

##BroadcastReceiver：

在Android中，Broadcast是一种广泛运用的在应用程序之间传输信息的机制。而BroadcastReceiver是对发送出来的 Broadcast进行过滤接受并响应的一类组件。下面将详细的阐述如何发送Broadcast和使用BroadcastReceiver过滤接收的过程：

__首先在需要发送信息的地方__，把要发送的信息和用于过滤的信息(如Action、Category)__装入一个Intent对象__，然后通过调用 Context.sendBroadcast()、sendOrderBroadcast()或sendStickyBroadcast()方法，把 Intent对象以广播方式发送出去。

当Intent发送以后，所有已经注册的BroadcastReceiver__会检查注册时的IntentFilter是否与发送的Intent相匹配__，若匹配则就会调用BroadcastReceiver的__onReceive()__方法。所以当我们定义一个BroadcastReceiver的时候，都需要实现onReceive()方法。

###<span style="color:red">注册BroadcastReceiver有两种方式:</span>

<span style="color:red">一种方式是，静态的在AndroidManifest.xml中用<receiver>标签生命注册，并在标签内用<intent- filter>标签设置过滤器。</span>

<span style="color:red">另一种方式是，动态的在代码中先定义并设置好一个 IntentFilter 对象，然后在需要注册的地方调Context.registerReceiver()方法，如果取消时就调用 Context.unregisterReceiver()方法。__如果用动态方式注册的BroadcastReceiver的Context对象被销毁时，BroadcastReceiver也就自动取消注册了。（特别注意，有些可能需要后台监听的，如短信消息）__</span>

另外，若在使用sendBroadcast()的方法是指定了接收权限，则只有在AndroidManifest.xml中用<uses- permission>标签声明了拥有此权限的BroascastReceiver才会有可能接收到发送来的Broadcast。同样，若在注册BroadcastReceiver时指定了可接收的Broadcast的权限，则只有在包内的AndroidManifest.xml中 用<uses-permission>标签声明了，拥有此权限的Context对象所发送的Broadcast才能被这个 BroadcastReceiver所接收。

__1.静态注册BroadcastReceiver:__

静态注册比动态注册麻烦点，先__新建一个类继承BroadcastReceiver__，如：

clsReceiver2.java
{% codeblock lang:java %}

package com.testBroadcastReceiver;
 
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;
 
/*
 * 接收静态注册广播的BroadcastReceiver，
 * step1:要到AndroidManifest.xml这里注册消息
 *         <receiver android:name="clsReceiver2">
            <intent-filter>
                <action
                    android:name="com.testBroadcastReceiver.Internal_2"/>
            </intent-filter>
        </receiver>
    step2:定义消息的字符串
    step3:通过Intent传递消息来驱使BroadcastReceiver触发
 */
public class clsReceiver2 extends BroadcastReceiver{
    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        Toast.makeText(context, "静态:"+action, 1000).show();
    }
}
{% endcodeblock %}

然后到__AndroidManifest.xml 添加receiver标签__
{% codeblock lang:xml %}
<receiver android:name="clsReceiver2">  
    <intent-filter>  
        <action  
            android:name="com.testBroadcastReceiver.Internal_2"/>  
    </intent-filter>  
</receiver> 
{% endcodeblock %}

第一个name是类名,即你的继承BroadcastReceiver的类的名字，里面实现了BroadcastReceive的__onReceive()__方法，来处理你接到消息的动作。

第二个name是action的名称，即你要监听的消息名字（其它消息都会被过滤不监听）。

__2.动态注册BroadcastReceiver:__

主要代码部分为：
{% codeblock lang:java %}
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(String); //为BroadcastReceiver指定action，即要监听的消息名字。
registerReceiver(MyBroadcastReceiver,intentFilter); //注册监听
unregisterReceiver(MyBroadcastReceiver); //取消监听
{% endcodeblock %}

(一般：在onStart中注册，onStop中取消unregisterReceiver)

{% codeblock lang:java %}
private class MyBroadcastReceive extends BroadcastReceiver
     {
           public void onReceive(Context context, Intent intent)
           {
               String action = intent.getAction(); 
                if(intent.ACTION_BATTERY_CHANGED.equals(action)) //判断是否接到电池变换消息 
                {
                   //处理内容
                 }
            }
      }
{% endcodeblock %}

========

##Broadcast：

发送广播消息，把要发送的信息和用于过滤的信息（如Action、Category）<span style="color:red">装入一个Intent对象</span>，然后通过<span style="color:red">调用 Context.sendBroadcast()、sendOrderBroadcast()或sendStickyBroadcast()方法</span>，把 Intent对象以广播方式发送出去。

例如：
{% codeblock lang:java %}
Intent intent = new Intent(INTENAL_ACTION_3);
intent.putExtra("Name", "hellogv");
intent.putExtra("Blog", "http://blog.csdn.net/hellogv");
sendBroadcast(intent);//传递过去
{% endcodeblock %}

###两种注册类型的区别是：

1)第一种不是常驻型广播，也就是说广播跟随程序的生命周期。

2)第二种是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。