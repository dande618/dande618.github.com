---
layout: post
title: "NotificationManager和Notification的使用总结"
date: 2013-06-16 16:03
comments: true
categories: android
---
<http://www.cnblogs.com/jerrychoi/archive/2010/05/28/1746221.html>
##(1)、使用系统定义的Notification

以下是使用示例代码：

<!-- more -->
{% codeblock lang:java %}
//创建一个NotificationManager的引用 

String ns = Context.NOTIFICATION_SERVICE; 

NotificationManager mNotificationManager = (NotificationManager)getSystemService(ns); 

//定义Notification的各种属性 

int icon = R.drawable.icon; //通知图标 

CharSequence tickerText = "Hello"; //状态栏显示的通知文本提示 

long when = System.currentTimeMillis(); //通知产生的时间，会在通知信息里显示 

//用上面的属性初始化Nofification 

Notification notification = new Notification(icon,tickerText,when); 

/* 

* 添加声音 

* notification.defaults |=Notification.DEFAULT_SOUND; 

* 或者使用以下几种方式 

* notification.sound = Uri.parse("file:///sdcard/notification/ringer.mp3"); 

* notification.sound = Uri.withAppendedPath(Audio.Media.INTERNAL_CONTENT_URI, "6"); 

* 如果想要让声音持续重复直到用户对通知做出反应，则可以在notification的flags字段增加"FLAG_INSISTENT" 

* 如果notification的defaults字段包括了"DEFAULT_SOUND"属性，则这个属性将覆盖sound字段中定义的声音 

*/ 

/* 

* 添加振动 

* notification.defaults |= Notification.DEFAULT_VIBRATE; 

* 或者可以定义自己的振动模式： 

* long[] vibrate = {0,100,200,300}; //0毫秒后开始振动，振动100毫秒后停止，再过200毫秒后再次振动300毫秒 

* notification.vibrate = vibrate; 

* long数组可以定义成想要的任何长度 

* 如果notification的defaults字段包括了"DEFAULT_VIBRATE",则这个属性将覆盖vibrate字段中定义的振动 

*/ 

/* 

* 添加LED灯提醒 

* notification.defaults |= Notification.DEFAULT_LIGHTS; 

* 或者可以自己的LED提醒模式: 

* notification.ledARGB = 0xff00ff00; 

* notification.ledOnMS = 300; //亮的时间 

* notification.ledOffMS = 1000; //灭的时间 

* notification.flags |= Notification.FLAG_SHOW_LIGHTS; 

*/ 

/* 

* 更多的特征属性 

* notification.flags |= FLAG_AUTO_CANCEL; //在通知栏上点击此通知后自动清除此通知 

* notification.flags |= FLAG_INSISTENT; //重复发出声音，直到用户响应此通知 

* notification.flags |= FLAG_ONGOING_EVENT; //将此通知放到通知栏的"Ongoing"即"正在运行"组中 

* notification.flags |= FLAG_NO_CLEAR; //表明在点击了通知栏中的"清除通知"后，此通知不清除， 

* //经常与FLAG_ONGOING_EVENT一起使用 

* notification.number = 1; //number字段表示此通知代表的当前事件数量，它将覆盖在状态栏图标的顶部 

* //如果要使用此字段，必须从1开始 

* notification.iconLevel = ; // 

*/ 

//设置通知的事件消息 

Context context = getApplicationContext(); //上下文 

CharSequence contentTitle = "My Notification"; //通知栏标题 

CharSequence contentText = "Hello World!"; //通知栏内容 

Intent notificationIntent = new Intent(this,Main.class); //点击该通知后要跳转的Activity 

PendingIntent contentIntent = PendingIntent.getActivity(this,0,notificationIntent,0); 

notification.setLatestEventInfo(context, contentTitle, contentText, contentIntent); 

//把Notification传递给NotificationManager 

mNotificationManager.notify(0,notification);
{% endcodeblock %}

如果想要更新一个通知，只需要在设置好notification之后，再次调用 setLatestEventInfo(),然后重新发送一次通知即可，即再次调用notify()。 

##(2)、使用自定义的Notification

要创建一个自定义的Notification，可以使用RemoteViews。要定义自己的扩展消息，首先要初始化一个RemoteViews对象，然后将它传递给Notification的contentView字段，再把PendingIntent传递给contentIntent字段。以下示例代码是完整步骤：

{% codeblock lang:java %}
//1、创建一个自定义的消息布局 view.xml 

<?xml version="1.0" encoding="utf-8"?> 

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 

android:layout_width="fill_parent" android:layout_height="fill_parent"> 

<ImageView android:id="@+id/image" android:layout_width="wrap_content" 

android:layout_height="fill_parent" android:layout_marginRight="10dp" /> 

<TextView android:id="@+id/text" android:layout_width="wrap_content" 

android:layout_height="fill_parent" android:textColor="#000" /> 

</LinearLayout> 

//2、在程序代码中使用RemoteViews的方法来定义image和text。然后把RemoteViews对象传到contentView字段 

RemoteViews contentView = new RemoteViews(getPackageName(),R.layout.view); 

contentView.setImageViewResource(R.id.image,R.drawable.icon); 

contentView.setTextViewText(R.id.text,”Hello,this message is in a custom expanded view”); 

notification.contentView = contentView; 

//3、为Notification的contentIntent字段定义一个Intent(注意，使用自定义View不需要setLatestEventInfo()方法) 

Intent notificationIntent = new Intent(this,Main.class); 

PendingIntent contentIntent = PendingIntent.getActivity(this,0,notificationIntent,0); 

notification.contentIntent = contentIntent; 

//4、发送通知 

mNotificationManager.notify(2,notification); 

//以下是全部示例代码 

//创建一个NotificationManager的引用 

String ns = Context.NOTIFICATION_SERVICE; 

NotificationManager mNotificationManager = (NotificationManager)getSystemService(ns); 

//定义Notification的各种属性 

int icon = R.drawable.icon; //通知图标 

CharSequence tickerText = "Hello"; //状态栏显示的通知文本提示 

long when = System.currentTimeMillis(); //通知产生的时间，会在通知信息里显示 

//用上面的属性初始化Nofification 

Notification notification = new Notification(icon,tickerText,when); 

RemoteViews contentView = new RemoteViews(getPackageName(),R.layout.view); 

contentView.setImageViewResource(R.id.image, R.drawable.iconempty); 

contentView.setTextViewText(R.id.text, "Hello,this is JC"); 

notification.contentView = contentView; 

Intent notificationIntent = new Intent(this,Main.class); 

PendingIntent contentIntent = PendingIntent.getActivity(this,0,notificationIntent,0); 

notification.contentIntent = contentIntent; 

//把Notification传递给NotificationManager 

mNotificationManager.notify(0,notification);
{% endcodeblock %}
