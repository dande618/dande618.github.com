---
layout: post
title: "SideNavigation＆ActionBarSherlock"
date: 2013-03-12 19:22
comments: true
categories: android
---
两个有用的开源项目
<!-- more -->
##ActionBarSherlock　<span style="font-size:small"><http://actionbarsherlock.com/></span>

ActionBarSherlock可以让2.x的系统也能使用actionbar。

如果你在3.0以上的机子上使用，那么它会调用系统原生的actionbar。另外它的使用方法和系统自身的方法相当相似。

##SideNavigation　<span style="font-size:small"><https://github.com/johnkil/SideNavigation></span>

一个侧边栏导航组件

{% codeblock %}
public class MainActivity implements ISideNavigationCallback
{% endcodeblock %}
1.添加组件
{% codeblock %}
    <com.devspark.sidenavigation.SideNavigationView
        android:id="@+id/side_navigation_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
{% endcodeblock %}
要在RelativeLayout的最后添加，使得该组件最后加载，而在其他组件的上面，不会被覆盖。

2.创建定义侧边栏的menu文件，例
{% codeblock %}
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android" >

    <item
        android:id="@+id/side_navigation_menu_item1"
        android:icon="@drawable/ic_android1"
        android:title="@string/title1"/>
    <item
        android:id="@+id/side_navigation_menu_item2"
        android:icon="@drawable/ic_android2"
        android:title="@string/title2"/>
    <item
        android:id="@+id/side_navigation_menu_item3"
        android:icon="@drawable/ic_android3"
        android:title="@string/title3"/>
    <item
        android:id="@+id/side_navigation_menu_item4"
        android:icon="@drawable/ic_android4"
        android:title="@string/title4"/>
    <item
        android:id="@+id/side_navigation_menu_item5"
        android:icon="@drawable/ic_android5"
        android:title="@string/title5"/>
</menu>
{% endcodeblock %}
3.在onCreate加入
{% codeblock %}
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    // other code

    sideNavigationView = (SideNavigationView) findViewById(R.id.side_navigation_view);
    sideNavigationView.setMenuItems(R.menu.side_navigation_menu);
    sideNavigationView.setMenuClickCallback(this);
    sideNavigationView.setMode(Mode.LEFT);

    getActionBar().setDisplayHomeAsUpEnabled(true);
}
{% endcodeblock %}
4.重写onOptionsItemSelected()
{% codeblock %}
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
    case android.R.id.home:
        sideNavigationView.toggleMenu();
        break;
    default:
        return super.onOptionsItemSelected(item);
    }
    return true;
}
{% endcodeblock %}

具体例子可以看SideNavigation和ActionBarSherlock的simple。






