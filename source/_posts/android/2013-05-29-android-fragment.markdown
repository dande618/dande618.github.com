---
layout: post
title: "Fragment"
date: 2013-05-29 17:10
comments: true
categories: android
---
官方API文档翻译：<http://blog.csdn.net/aomandeshangxiao/article/details/7671533>

1. Fragment 作为 Activity 界面的一部分组成出现。

2. 可以在一个 Activity 中同时出现多个 Fragment，并且，一个 Fragment 亦可在多个 Activity 中使用。

3. 在 Activity 运行过程中，可以添加、移除或者替换 Fragment（add()、remove()、replace()）。

4. Fragment 可以响应自己的输入事件，并且有自己的生命周期，当然，它们的生命周期直接被其所属的宿主 Activity 的生命周期影响。

{% img center http://developer.android.com/images/fundamentals/fragments.png %}
<!-- more -->

##创建 Fragment

要创建一个 Fragment，必须创建一个 Fragment 的子类（或者继承自一个已存在的它的子类），Fragment 类的代码看起来很像 Activity 。它包含了和 activity 类似的回调方法，例如onCreate()、onStart()、onPause()以及onStop()。事实上，如果你准备将一个现成的 Android 应用转换到使用 Fragment ，可能只需简单的将代码从你的 activity 的回调方法分别移动到你的 fragment 的回调方法即可。

{% img center http://developer.android.com/images/fragment_lifecycle.png %}

##有 2 种方法你可以添加一个 fragment 到 activity layout：

###在activity的layout文件中声明fragment

在这种情况下，你可以像为View一样, 为fragment指定layout属性。例子是一个有2个fragment的activity的layout：

{% codeblock lang:html %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment android:name="com.example.news.ArticleListFragment"
            android:id="@+id/list"
            android:layout_weight="1"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
    <fragment android:name="com.example.news.ArticleReaderFragment"
            android:id="@+id/viewer"
            android:layout_weight="2"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
</LinearLayout>
{% endcodeblock %}

<fragment> 中的 android:name属性指定了在layout中实例化的Fragment类。

当系统创建这个activity layout时,它实例化每一个在layout中指定的fragment,并调用每一个上的onCreateView()方法，来获取每一个 fragment的layout。系统将从fragment返回的View直接插入到<fragment>元素所在的地方。

注意: 每一个fragment都需要一个唯一的标识,如果activity重启,系统可以用来恢复fragment(并且你也可以用来捕获fragment来处理事务,例如移除它.)   

有3种方法来为一个fragment提供一个标识:

- 为 android:id 属性提供一个唯一ID.

- 为 android:tag 属性提供一个唯一字符串.

- 如果以上2个你都没有提供, 系统使用容器view的ID.

###撰写代码将fragment添加到一个已存在的ViewGroup.

当activity运行的任何时候, 都可以将fragment添加到activity layout.只需简单的指定一个需要放置fragment的ViewGroup.为了在你的 activity中操作fragment事务(例如添加,移除,或代替一个fragment),必须使用来自FragmentTransaction 的API.

可以按如下方法,从你的Activity取得一个 FragmentTransaction 的实例:

{% codeblock lang:java %}
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
{% endcodeblock %}

然后你可以使用 add() 方法添加一个fragment, 指定要添加的fragment和要插入的view.

{% codeblock lang:java %}
ExampleFragment fragment = new ExampleFragment();
fragmentTransaction.add(R.id.fragment_container,fragment);
fragmentTransaction.commit();
{% endcodeblock %}

add()的第一个参数是fragment要放入的ViewGroup, 由resource ID指定,第二个参数是需要添加的fragment.一旦用FragmentTransaction做了改变,为了使改变生效,必须调用commit().