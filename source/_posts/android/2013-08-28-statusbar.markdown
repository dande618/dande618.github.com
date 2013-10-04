---
layout: post
title: "android隐藏下方系统栏statusbar"
date: 2013-08-28 18:10
comments: true
categories: android
---
最近修复了一个BUG是定制SetupWizard时，下面的系统栏会出现。

SetupWizard是一台安卓设备第一次运行时的设置程序，可以设置Google帐号，姓名什么的，所以不允许用户点击返回、Home或最近运行三个虚拟按键。

当设置Google帐号时，会打开GoogleLoginService的Activity，所以用网上的有些方法不好用。一进入输入状态时，StatusBar还会显示出来。

<!-- more -->

正常的方法，可以参考SystemUI的源码，是使用android.app.StatusBarManager这个类来进行操作，但问题是这个类是hide隐藏的，相关的参数也是隐藏的，比如View.STATUS_BAR_DISABLE_HOME、Context.STATUS_BAR_SERVICE等。

代码如下：

{% codeblock lang:java %}
import android.app.StatusBarManager;

private StatusBarManager mStatueBarManager;

mStatueBarManager = (StatusBarManager)getSystemService(Context.STATUS_BAR_SERVICE);
mStatueBarManager.disable(View.STATUS_BAR_DISABLE_HOME|View.STATUS_BAR_DISABLE_BACK|View.STATUS_BAR_DISABLE_RECENT);
{% endcodeblock %}

这样的话只要Activity不destroy掉，会一直隐藏。可以在适当的时候

{% codeblock lang:java %}
mStatueBarManager.disable(0x00000000);
{% endcodeblock %}

另外还需要在manifest加上权限

{% codeblock lang:xml %}
<uses-permission android:name="android.permission.STATUS_BAR" />
{% endcodeblock %}

当然这个权限也是只能系统的程序使用。之后在源码环境下，使用mm或者mmm编译后，push到system/app下就可以了。

使用反射的方法调用StatusBarManager，也是需要android.permission.STATUS_BAR的权限。

如果没有添加权限，会有异常提示不是系统签名和缺少相关权限。

对于一般的程序，需要暂时隐藏系统栏的，比如视频播放界面，可以使用setSystemUiVisibility临时隐藏，在下一次操作时会显示。

参考com.google.android.apps.proofer.SystemUiHider，以下代码实现了隐藏statusbar和顶端状态栏，点击屏幕后，显示两秒系统栏再自动隐藏。

{% codeblock lang:java %}
package com.example.test;

import android.app.Activity;
import android.os.Build;
import android.os.Bundle;
import android.os.Handler;
import android.view.View;
import android.view.Window;
import android.view.WindowManager;

public class MainActivity extends Activity {

private static final int HIDE_DELAY_MILLIS = 1000;

private Handler mHandler;
private View mView;

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
mView = getWindow().getDecorView();
setup(getWindow());
}

@Override
protected void onStart() {
super.onStart();
}

public void setup(Window window) {
hideSystemUi();


// Pre-Jellybean just hide the status bar
if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN) {
window.addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
}

mHandler = new Handler();
mView.setOnSystemUiVisibilityChangeListener(new View.OnSystemUiVisibilityChangeListener() {
@Override
public void onSystemUiVisibilityChange(int visibility) {
if ((visibility & View.SYSTEM_UI_FLAG_HIDE_NAVIGATION) == 0) {
delay();
}
}
});
}

private void hideSystemUi() {
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
// On Jellybean we can use new System UI flags to allow showing
// titlebar/systembar
// only upon touching the screen, while still having the content
// laid out in
// the entire screen.
mView.setSystemUiVisibility(View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
| View.SYSTEM_UI_FLAG_FULLSCREEN
| View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
| View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
} else {
mView.setSystemUiVisibility(View.SYSTEM_UI_FLAG_HIDE_NAVIGATION);
}
}

private Runnable mHideRunnable = new Runnable() {
public void run() {
hideSystemUi();
}
};

public void delay() {
mHandler.removeCallbacks(mHideRunnable);
mHandler.postDelayed(mHideRunnable, HIDE_DELAY_MILLIS);
}
 }

{% endcodeblock %}

> 