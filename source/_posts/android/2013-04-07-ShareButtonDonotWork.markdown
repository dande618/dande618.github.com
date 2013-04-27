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

{% img center http://img14.poco.cn/mypoco/myphoto/20130407/09/4309479020130407093427048.jpg ShareAction %}

<br />

　　在网路上查找，确有人反映过这个问题，应该是一个BUG。

　　参见 [android.widget.ShareActionProvider does not work on the emulator](http://code.google.com/p/android/issues/detail?id=25467)

<br />

{% img center http://img14.poco.cn/mypoco/myphoto/20130409/10/4309479020130409101555091.jpg ShareActionProvider %}

　　此图是系统提供的ShareActionProvider类及相关对象，提供快速存取提供共享服务的Action。

　　整个类图主要包括ShareActionProvider、ActivityChooserModel、ActivityChooserView、ActivityChooserViewAdapter等对象。

　　派生自ActionProvider的具体类ShareActionProvider，用来实例化ActivityChooserModel、ActivityChooserView对象，并为ActivityChooserView对象设置数据模式，生成视图，操作和获取Model信息，根据Model信息创建活动菜单。

　　ActivityChooserModel、ActivityChooserView、ActivityChooserViewAdapter三者构成MVC模式，分别对应Model、View及采用Adapter模式的Control，ActivityChooserView通过ActivityChooserViewAdapter获取ActivityChooserModel中的活动信息，ActivityChooserModel本身派生自DataSetObservable，可以在ActivityChooserView对象中为ActivityChooserModel对象登记一个DataSetObserver类型的对象，ActivityChooserModel对象通过该对象向ActivityChooserView对象发送ActivityChooserModel对象中的数据变化通知。

　　ActivityChooserModel对于通过intent从包管理器中获得的符合intent条件的活动记录（以ActivityResolveInfo类型保存在数组列表中mActivites）的排序方法采用了策略模式，排序方法被封装成对象，在没有通过setActivitySorter方法设置排序方法时，采用默认排序方法，由DefaultSorter对象封装。DefaultSorter对象提供的排序方法是依据活动记录中的weight值进行排序，被排序的活动记录的weight值还依据记录在HistoricalRecord（HistoricalRecord中的活动通过读私有的XML类型的共享历史文件获得）列表中的活动顺序进行修改，依据HistoricalRecord列表的从后往前的顺序为mActivites数组中对应的对象增加weight值，活动历史记录中越往后的记录在mActivites列表中对应活动增加的权值越小，最新的相应记录增加的权值越大。

　　HistoryPersister线程对象用于把HistoricalRecord列表中的记录保存到历史文件中。HistoryLoader线程对象用于读取历史文件到HistoricalRecord列表中。DataModelPackageMonitor对象用于监视数据包，在数据包更新时同步活动记录列表mActivites。ActivityChooserMode对象还通过Map类型的HashMap保证一个相同的历史文件只能实例化一个ActivityChooserModel对象，是单例模式的具体应用。

<br />
<br />
在ActivityChooserView.java内的ActivityChooserViewAdapter类
{% codeblock lang:java %}
private boolean mShowDefaultActivity;

if (!mShowDefaultActivity && mDataModel.getDefaultActivity() != null) {
activityCount--;
}
{% endcodeblock %}
<br />
{% codeblock lang:java %}
 if (activityCount > 0 && historySize > 0) {
            mDefaultActivityButton.setVisibility(VISIBLE);
            ResolveInfo activity = mAdapter.getDefaultActivity();
            PackageManager packageManager = mContext.getPackageManager();
            mDefaultActivityButtonImage.setImageDrawable(activity.loadIcon(packageManager));
            if (mDefaultActionButtonContentDescription != 0) {
                CharSequence label = activity.loadLabel(packageManager);
                String contentDescription = mContext.getString(
                        mDefaultActionButtonContentDescription, label);
                mDefaultActivityButton.setContentDescription(contentDescription);
            }

            // Work-around for #415.
            mAdapter.setShowDefaultActivity(false, false);
        } else {
            mDefaultActivityButton.setVisibility(View.GONE);
        }
{% endcodeblock %}

　　当打开程式时，如果只有一个响应intent的程式，activityCount成为0，button因而不能使用。

<br />
<br />
<br />
<br />

<div style="float:right;width:38.2%;margin-left: 0.6em;margin-top: 0.6em;"><img src="http://img14.poco.cn/mypoco/myphoto/20130407/09/4309479020130407093549098.jpg" /></div>　　据分析，当只有一个（或零个）程式接受Intent.ACTION_SEND（或其他）的某个类型（如text/plain）时，就无法点出分享。而AVD初始时可能只有SMS（简讯息）可接受此Intent。如果再装载一个程式（如微博客户端），就可以正常使用ShareAction打开分享程式列表了。由于ShareAction会记住使用最多的程式，并显示在ActionBar上，所以使用过一次分享到SMS后卸载掉“微博”也是会显示SMS。但这并不解决问题。

　　参见 [ShareActionProvider does not work if only one intent](https://github.com/JakeWharton/ActionBarSherlock/issues/415)

<br />

　　附ShareAction实现的部分代码（在SherlockFragment内，覆盖onCreateOptionsMenu方法）
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

　　ActionBarSherlock在最近修复了这个问题，详见[上文链接](https://github.com/JakeWharton/ActionBarSherlock/issues/415)。有兴趣的朋友可以下载最新代码，或者在调试时装载其他支持分享的程式！