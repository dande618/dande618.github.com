---
layout: post
title: "Android Context"
date: 2013-05-27 20:59
comments: true
categories: android
---
<http://blog.csdn.net/qinjuning/article/details/7310620>

{% img center http://img.my.csdn.net/uploads/201203/1/0_1330607569Vj4c.gif %}

Context，中文直译为“上下文”，SDK中对其说明如下：

> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

<!-- more -->

从上可知一下三点,即：

1. 它描述的是一个应用程序环境的信息，即上下文。

2. 该类是一个抽象(abstract class)类，Android提供了该抽象类的具体实现类(后面我们会讲到是ContextIml类)。

3. 通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个Activity，发送广播，接受Intent信息 等。。

于是，我们可以利用该Context对象去构建应用级别操作(application-level operations) 。

__Context类__    路径： /frameworks/base/core/java/android/content/Context.java

说明：  抽象类，提供了一组通用的API。

源代码(部分)如下：   

{% codeblock lang:java %}
public abstract class Context {
     //...
     public abstract Object getSystemService(String name);  //获得系统级服务
     public abstract void startActivity(Intent intent);     //通过一个Intent启动Activity
     public abstract ComponentName startService(Intent service);  //启动Service
     //根据文件名得到SharedPreferences对象
     public abstract SharedPreferences getSharedPreferences(String name,int mode);
     //...
}
{% endcodeblock %}

Activity类 、Service类 、Application类本质上都是Context子类。

应用程序创建Context实例的情况有如下几种情况：

1. 创建Application对象时， 而且整个App共一个Application对象

2. 创建Service对象时

3. 创建Activity对象时

========

##Android context（Application/Activity）与内存泄露 

android中的context可以做很多操作，但是最主要的功能是加载和访问资源。

在android中有两种context，一种是 application context，一种是activity context，通常我们在各种类和方法间传递的是activity context。 

比如一个activity的onCreate： 

{% codeblock lang:java %}
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
  
    requestWindowFeature(Window.FEATURE_NO_TITLE);
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,WindowManager.LayoutParams.FLAG_FULLSCREEN);
    setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
  
    mGameView = new GameView(this);
    setContentView(mGameView);
}
{% endcodeblock %}

把activity context传递给view，意味着view拥有一个指向activity的引用，进而引用activity UI占有的资源：view , resource, SensorManager等。 

但是这样如果context发生内存泄露的话，就会泄露很多内存，这里泄露的意思是gc没有办法回收activity的内存（当前Activity为活动或finish后还没来得及回收）。

Leaking an entire activity是很容易的一件事。 

当屏幕旋转的时候，系统会销毁当前的activity，保存状态信息再创建一个新的。 

比如我们写了一个应用程序，需要加载一个很大的图片，我们不希望每次旋转屏幕的时候都销毁这个图片重新加载。

实现这个要求的简单想法就是定义一个静态的Drawable，这样Activity 类创建销毁它始终保存在内存中，访问速度会很快。 

__实现类似：__

{% codeblock lang:java %}
public class myactivity extends Activity {   
    private static Drawable sBackground;   
    protected void onCreate(Bundle state) {   
        super.onCreate(state);   
  
        TextView label = new TextView(this);   
        label.setText("Leaks are bad");   
  
        if (sBackground == null) {   
            sBackground = getDrawable(R.drawable.large_bitmap);   
        }   
        label.setBackgroundDrawable(sBackground);//drawable attached to a view   
  
        setContentView(label);   
    }   
}
{% endcodeblock %}

这段程序看起来很简单，但是却问题很大。当屏幕旋转的时候会有leak，即gc没法销毁activity

我们刚才说过，屏幕旋转的时候系统会销毁当前的activity。但是当drawable和view关联后，drawable保存了view的 reference，即sBackground保存了label的引用，而label保存了activity的引用。既然drawable不能销毁，它所引用和间接引用的都不能销毁，这样系统就没有办法销毁当前的activity，于是造成了内存泄露。gc对这种类型的内存泄露是无能为力的。 

避免这种内存泄露的方法是避免activity中的任何对象的生命周期长过activity，避免由于对象对 activity的引用导致activity不能正常被销毁

同时，我们可以使用application context

application context伴随application的一生，与activity的生命周期无关。

application context可以通过Context.getApplicationContext或者Activity.getApplication方法获取。 

使用Application，需要在 AndroidManifest.xml 文件注册，即android:name=".GApplication"：

{% codeblock lang:xml %}
<application android:icon="@drawable/icon"   
             android:label="@string/app_name"  
             android:name=".GApplication">  
  
    <activity android:name=".WordSearch"  
              android:label="@string/app_name"  
              android:theme="@android:style/Theme.NoTitleBar.Fullscreen"  
              android:screenOrientation="portrait"  
              android:configChanges="keyboardHidden|orientation|keyboard|screenLayout">  
        <intent-filter>  
            <action android:name="android.intent.action.MAIN" />  
            <category android:name="android.intent.category.LAUNCHER" />  
        </intent-filter>  
    </activity>  
</application>  
{% endcodeblock %}

__避免context相关的内存泄露，记住以下几点：__

1. 不要让生命周期长的对象引用activity context，即保证引用activity的对象要与activity本身生命周期是一样的 

2. 对于生命周期长的对象，可以使用application context （继承类：public class GApplication extends Application）

3. 尽量使用静态类（全局），避免非静态的内部类，避免生命周期问题，注意内部类对外部对象引用导致的生命周期变化