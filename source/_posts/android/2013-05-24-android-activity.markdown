---
layout: post
title: "android Activity"
date: 2013-05-24 11:00
comments: true
categories: android
---
Activity是Android组件中最基本也是最为常见用的四大组件之一，通常是一个单独的屏幕，它上面可以显示一些控件也可以监听并处理用户的事件做出响应。Activity之间通过Intent进行通信。
<!-- more -->
##【1】生命周期

<http://blog.csdn.net/liuhe688/article/details/6733407>

{% img center http://dl.iteye.com/upload/picture/pic/92402/eec1c58c-4034-3e02-bf24-80300bb08df8.png %}

1. 启动Activity：系统会先调用onCreate方法，然后调用onStart方法，最后调用onResume，Activity进入运行状态。

2. 当前Activity被其他Activity覆盖其上或被锁屏：系统会调用onPause方法，暂停当前Activity的执行。

3. 当前Activity由被覆盖状态回到前台或解锁屏：系统会调用onResume方法，再次进入运行状态。

4. 当前Activity转到新的Activity界面或按Home键回到主屏，自身退居后台：系统会先调用onPause方法，然后调用onStop方法，进入停滞状态。

5. 用户后退回到此Activity：系统会先调用onRestart方法，然后调用onStart方法，最后调用onResume方法，再次进入运行状态。

6. 当前Activity处于被覆盖状态或者后台不可见状态，即第2步和第4步，系统内存不足，杀死当前Activity，而后用户退回当前Activity：再次调用onCreate方法、onStart方法、onResume方法，进入运行状态。

7. 用户退出当前Activity：系统先调用onPause方法，然后调用onStop方法，最后调用onDestory方法，结束当前Activity。

##【2】关于onSaveInstanceState()

<http://www.cnblogs.com/hanyonglu/archive/2012/03/28/2420515.html>

> Android calls onSaveInstanceState() before the activitybecomes vulnerable to being destroyed by the system, but does not bothercalling it when the instance is actually being destroyed by a user action (suchas pressing the BACK key).

当某个activity变得"容易"被系统销毁时，该activity的onSaveInstanceState()就会被执行，除非该activity是被用户主动销毁的，例如当用户按BACK键的时候。
1. 当用户按下HOME键时。

2. 长按HOME键，选择运行其他的程序时。

3. 按下电源按键（关闭屏幕显示）时。

4. 从activity A中启动一个新的activity时。

5. 屏幕方向切换时，例如从竖屏切换到横屏时。

##【3】如何将一个Activity设置成窗口样式。

在AndroidManifext.xml中Activity定义处添加

android:theme="@android:style/Theme.Dialog"

android:theme="@android:style/Theme.Translucent"。 

打开这种Activity时，前一个Activity只调用onPause()方法。

##【4】Activity之间的数据交换

Activity之间通过Intent进行通信。

比如打开第二个Activity

{% codeblock lang:java %}
ComponentName comp = new ComponentName(MainActivity.this,SecondActivity.class);
Intent intent = new Intent();
intent.setComponent(comp);
startActivity(intent);
{% endcodeblock %}

可以简化写成

{% codeblock lang:java %}
Intent intent = new Intent(MainActivity.this,SecondActivity.class);
startActivity(intent);
{% endcodeblock %}

需要交换的数据需要放入intent里。即调用putExtra("key",value)方法。

或者调用putExtras(Bundle data)放入一个数据便携包

或调用putSerializable(String key,Serializable data)，放入一个可序列化的对象。

实现可传递的对象需要类实现Serializable接口或者Parcelable接口。

<http://blog.sina.com.cn/s/blog_6321ab240100ssxp.html>

###启动其他Activity并返回结果

使用startActivityForResult(Intent intent,int requestCode)方法来启动其他Activity，并获取第二个Activity返回的结果。

- 第一个Activity需要重写 onActivityResult(int requestCode, int resultCode, Intent data)；

- 在第二个Activity里调用setResut(int resultCode, Intent intent)，并finish()关闭Activity；

##【5】ANR问题

<http://hi.baidu.com/xielingling20/item/882d1cd1176d1c03d90e44e3>

anr问题的解析。

在Android中，活动管理器和窗口管理器这两个系统服务负责监视应用程序的响应。当出现下列情况时，Android就会显示ANR对话框了： 

对输入事件(如按键、触摸屏事件)的响应超过5秒 

意向接受器(intentReceiver)超过10秒钟仍未执行完毕 

Android应用程序完全运行在一个独立的线程中(例如main)。这就意味着，任何在主线程中运行的，需要消耗大量时间的操作都会引发ANR。因为此时，你的应用程序已经没有机会去响应输入事件和意向广播(Intent broadcast)。 

因此，任何运行在主线程中的方法，都要尽可能的只做少量的工作。特别是活动生命周期中的重要方法如onCreate()和 onResume()等更应如此。潜在的比较耗时的操作，如访问网络和数据库;或者是开销很大的计算，比如改变位图的大小，需要在一个单独的子线程中完成(或者是使用异步请求，如数据库操作)。但这并不意味着你的主线程需要进入阻塞状态已等待子线程结束 -- 也不需要调用Therad.wait()或者Thread.sleep()方法。取而代之的是，主线程为子线程提供一个句柄(Handler)，让子线程在即将结束的时候调用它。使用这种方法涉及你的应用程序，能够保证你的程序对输入保持良好的响应，从而避免因为输入事件超过5秒钟不被处理而产生的ANR。这种实践需要应用到所有显示用户界面的线程，因为他们都面临着同样的超时问题。

1、 什么是ANR？

ANR:Application Not Responding，即应用无响应，就是说应用程序在5S内没有响应。

2、 为什么会ANR？

（1）当前的事物发生堵塞，没有被执行。比如说UI线程中处理耗时的操作，发生线程的堵塞

（2）当前事物正在被执行，而没有被完全执行。

3、 ANR的分类：

（1）按键或触摸事件在5S内没有响应

（2）BroadcastReceiver在特定时间内无法处理完成（BroadcastTimeout）

（3）Service在特定的时间内无法处理完成（ServiceTimeout）

4、 如何尽量去避免 ANR 的出现

一般ANR多是因为线程堵塞照成的，比如说UI线程去处理很耗时的操作（数据库，网络的连接，下载等类似的操作），所以要避免此类问题的出现。

（1）UI线程尽量只做跟UI相关的工作。

（2）耗时的操作可以开启一个新的线程，或者是后台去处理。

5、 有哪些UI线程？

Activity:onCreate(), onResume(), onDestroy(), onKeyDown(), onClick()

AsyncTask: onPreExecute(), onProgressUpdate(), onPostExecute(), onCancel,

Mainthread handler: handleMessage(), post*(runnable r)

子线程：

Thread，intentservice、Asynctask

##【6】使用Activity应该注意的问题

Activity几乎承接着用户对应用程序（Application）的所有操作，可以通过主题（Theme）改变样子的。

- Activity应该要注意它的生命周期（Lifecycle）、设备状态（Configuration）改变时的影响以及运行状态和数据的保存，这个在一个应用程序是否可靠和人性化上至关重要。

- Activity里还应该要申明一些许可（Permissions），以便使用Android的一些软硬件功能，这些申明可以由代码或者Manifest.xml给出。

- 每个Activity（的入口）一定要在Manifest当中申明。

- 耗时的操作应该放在UI线程外的其他线程里，以防阻塞操作。如在非UI线程更新UI需要使用Handler

- 继承Activity，覆盖各个方法时需要注意调用super父类中的相同方法。

- 要考虑多分辨率。至少为hdpi, mdpi, ldpi准备图片和布局。元素的单位也尽可能的使用dip而不要用px。

- 考虑国际化和资源自适应。文本文字应该写入strings.xml并实现国际化。