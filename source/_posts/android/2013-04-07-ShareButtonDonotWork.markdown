---
layout: post
title: "ShareAction在AVD失效的问题"
date: 2013-04-07 11:11
comments: true
categories: android
---
　　在虚拟机（AVD，Android Virtual Device）使用ActionBar／ActionBarSherlock）上的ShareAction分享按键，怎么点也点不出程式列表，但在手机上没有问题。

<!-- more -->
<br />

<img src="http://img14.poco.cn/mypoco/myphoto/20130407/09/4309479020130407093427048.jpg" />

<br />

　　在网路上查找，确有人反映过这个问题，应该是一个BUG。

　　参见 [android.widget.ShareActionProvider does not work on the emulator](http://code.google.com/p/android/issues/detail?id=25467)

<br />

<div style="float:right;width:38.2%;margin-left: 0.6em;margin-top: 0.6em;"><img src="http://img14.poco.cn/mypoco/myphoto/20130407/09/4309479020130407093549098.jpg" /></div>　　据分析，当只有一个（或零个）程式接受Intent.ACTION_SEND（或其他）的某个类型（如text/plain）时，就无法点出分享。而AVD初始时可能只有SMS（简讯息）可接受此Intent。如果再装载一个程式（如微博客户端），就可以正常使用ShareAction打开分享程式列表了。由于ShareAction会记住使用最多的程式，并显示在ActionBar上，所以使用过一次分享到SMS后卸载掉“微博”也是会显示SMS。但这并不解决问题。

　　亦可参见 [ShareActionProvider does not work if only one intent](https://github.com/JakeWharton/ActionBarSherlock/issues/415)

<br />

附ShareAction实现的部分代码（在SherlockFragment内，覆盖onCreateOptionsMenu方法）
<br />
{% codeblock lang:java %}
private ShareActionProvider mShareActionProvider; 

@Override 
public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) { 
MenuItem menuItem = menu.findItem(R.id.share); 
// Get the provider and hold onto it to set/change the share intent. 
mShareActionProvider = (ShareActionProvider) menuItem.getActionProvider(); 
GoodsItem item = GoodsItemManager.instance().getGoodsItems().get(mPosition); 

Intent shareIntent = new Intent(Intent.ACTION_SEND); 
shareIntent.putExtra(Intent.EXTRA_SUBJECT, getResources().getString(R.string.share_subject)); 
shareIntent.putExtra(Intent.EXTRA_TEXT, Html.fromHtml(item.getTitle())); 
shareIntent.setType("text/plain"); 
mShareActionProvider.setShareIntent(shareIntent);   
 }
{% endcodeblock %}

<br />

menu下的xml文件中定义
{% codeblock lang:xml %}
    <item
        android:id="@+id/share"
        android:icon="@drawable/ic_action_share"
        android:showAsAction="ifRoom"
        android:actionProviderClass="com.actionbarsherlock.widget.ShareActionProvider"
        android:title="@string/action_share">
    </item>
{% endcodeblock %}

<br />

　　ActionBarSherlock似乎在最近修复了这个问题，详见[上文链接](https://github.com/JakeWharton/ActionBarSherlock/issues/415)。有兴趣的朋友可以一试，或者在调试时装载其他支持分享的程式！