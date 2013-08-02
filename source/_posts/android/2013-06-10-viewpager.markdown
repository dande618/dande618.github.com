---
layout: post
title: "Android的 ViewPager"
date: 2013-06-10 11:00
comments: true
categories: android
---
ViewPager用于实现多页面的切换效果，该类存在于Google的兼容包里面，所以在引用时记得在BuilldPath中加入“android-support-v4.jar”
<!-- more -->
主布局文件

main.xml

{% codeblock lang:xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" >

        <android.support.v4.view.PagerTitleStrip
            android:id="@+id/pagertitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="top" />
    </android.support.v4.view.ViewPager>

</LinearLayout>
{% endcodeblock %}

其中ViewPager为多页显示控件，PagerTitleStrip用于显示当前页面的标题

主窗口代码：

PagerTitleDemoActivity.java

{% codeblock lang:java %}
package com.ns.pager;

import java.util.ArrayList;

import android.app.Activity;
import android.os.Bundle;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.PagerTitleStrip;
import android.support.v4.view.ViewPager;
import android.view.LayoutInflater;
import android.view.View;

public class PagerTitleDemoActivity extends Activity {
    /** Called when the activity is first created. */
	private ViewPager mViewPager;
	private PagerTitleStrip mPagerTitleStrip;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        mViewPager = (ViewPager)findViewById(R.id.viewpager);
        mPagerTitleStrip = (PagerTitleStrip)findViewById(R.id.pagertitle);
        
        //将要分页显示的View装入数组中
        LayoutInflater mLi = LayoutInflater.from(this);
        View view1 = mLi.inflate(R.layout.view1, null);
        View view2 = mLi.inflate(R.layout.view2, null);
        View view3 = mLi.inflate(R.layout.view3, null);
        
        //每个页面的Title数据
        final ArrayList<View> views = new ArrayList<View>();
        views.add(view1);
        views.add(view2);
        views.add(view3);
        
        final ArrayList<String> titles = new ArrayList<String>();
        titles.add("tab1");
        titles.add("tab2");
        titles.add("tab3");
        
        //填充ViewPager的数据适配器
        PagerAdapter mPagerAdapter = new PagerAdapter() {
			
			@Override
			public boolean isViewFromObject(View arg0, Object arg1) {
				return arg0 == arg1;
			}
			
			@Override
			public int getCount() {
				return views.size();
			}

			@Override
			public void destroyItem(View container, int position, Object object) {
				((ViewPager)container).removeView(views.get(position));
			}

			@Override
			public CharSequence getPageTitle(int position) {
				return titles.get(position);
			}

			@Override
			public Object instantiateItem(View container, int position) {
				((ViewPager)container).addView(views.get(position));
				return views.get(position);
			}
		};
		
		mViewPager.setAdapter(mPagerAdapter);
    }
}
{% endcodeblock %}

#用ViewPager实现欢迎引导页面

<http://blog.csdn.net/notice520/article/details/7454568/>

ViewPager需要android-support-v4.jar这个包的支持，来自google提供的一个附加包。大家搜下即可。

ViewPager主要用来组织一组数据，并且通过左右滑动的方式来展示。

现在的大多数应用都会有一个欢迎引导页面，如图所示，通过左右滑动来告知用户一些功能特性。

{% img center http://my.csdn.net/uploads/201204/12/1334221992_9936.png %}

这个引导图效果用ViewPager可以很轻松的实现。

正如前面所说，ViewPager是用来展示一组数据的，所以肯定需要Adapter来绑定数据和view。先写一个Adapter：

{% codeblock lang:java %}
package com.notice.viewpagerd;

import java.util.List;

import android.os.Parcelable;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.view.View;

public class ViewPagerAdapter extends PagerAdapter{
	
	//界面列表
	private List<View> views;
	
	public ViewPagerAdapter (List<View> views){
		this.views = views;
	}

	//销毁arg1位置的界面
	@Override
	public void destroyItem(View arg0, int arg1, Object arg2) {
		((ViewPager) arg0).removeView(views.get(arg1));		
	}

	@Override
	public void finishUpdate(View arg0) {
		// TODO Auto-generated method stub
		
	}

	//获得当前界面数
	@Override
	public int getCount() {
		if (views != null)
		{
			return views.size();
		}
		
		return 0;
	}
	

	//初始化arg1位置的界面
	@Override
	public Object instantiateItem(View arg0, int arg1) {
		
		((ViewPager) arg0).addView(views.get(arg1), 0);
		
		return views.get(arg1);
	}

	//判断是否由对象生成界面
	@Override
	public boolean isViewFromObject(View arg0, Object arg1) {
		return (arg0 == arg1);
	}

	@Override
	public void restoreState(Parcelable arg0, ClassLoader arg1) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public Parcelable saveState() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public void startUpdate(View arg0) {
		// TODO Auto-generated method stub
		
	}

}
{% endcodeblock %}

这里我们要绑定的每一个item就是一个引导界面，我们用一个list来保存。 

通过继承PagerAdapter，并实现几个我写注释的方法即可。

布局界面比较简单，加入ViewPager组件，以及底部的引导小点：

{% codeblock lang:xml %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

	<android.support.v4.view.ViewPager
	    android:id="@+id/viewpager"
	    android:layout_width="fill_parent" 
	    android:layout_height="fill_parent" 
	    />    
    
    
    <LinearLayout 
        android:id="@+id/ll" 
    	android:orientation="horizontal" 
    	android:layout_width="wrap_content" 
    	android:layout_height="wrap_content" 
    	android:layout_marginBottom="24.0dip" 
    	android:layout_alignParentBottom="true" 
    	android:layout_centerHorizontal="true">
    	
    	<ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:clickable="true"
            android:padding="15.0dip"
            android:src="@drawable/dot" />

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:clickable="true"
            android:padding="15.0dip"
            android:src="@drawable/dot" />

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:clickable="true"
            android:padding="15.0dip"
            android:src="@drawable/dot" />

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:clickable="true"
            android:padding="15.0dip"
            android:src="@drawable/dot" />

    </LinearLayout>

</RelativeLayout>
{% endcodeblock %}

其中小点的图片用一个selector来控制颜色（设置item的enable为true或者false）

dot.xml:

{% codeblock lang:xml %}
<?xml version="1.0" encoding="UTF-8"?>
<selector
  xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="true" android:drawable="@drawable/dark_dot" />
    <item android:state_enabled="false" android:drawable="@drawable/white_dot" />
</selector>
{% endcodeblock %}

下面就是写Activity了。

{% codeblock lang:java %}
package com.notice.viewpagerd;

import java.util.ArrayList;
import java.util.List;

import android.app.Activity;
import android.os.Bundle;
import android.support.v4.view.ViewPager;
import android.support.v4.view.ViewPager.OnPageChangeListener;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.ImageView;
import android.widget.LinearLayout;

public class ViewPagerDemoActivity extends Activity implements OnClickListener, OnPageChangeListener{
	
	private ViewPager vp;
	private ViewPagerAdapter vpAdapter;
	private List<View> views;
	
	//引导图片资源
	private static final int[] pics = { R.drawable.whatsnew_00,
			R.drawable.whatsnew_01, R.drawable.whatsnew_02,
			R.drawable.whatsnew_03 };
	
	//底部小店图片
	private ImageView[] dots ;
	
	//记录当前选中位置
	private int currentIndex;
	
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        views = new ArrayList<View>();
       
        LinearLayout.LayoutParams mParams = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT,
        		LinearLayout.LayoutParams.WRAP_CONTENT);
        
        //初始化引导图片列表
        for(int i=0; i<pics.length; i++) {
        	ImageView iv = new ImageView(this);
        	iv.setLayoutParams(mParams);
        	iv.setImageResource(pics[i]);
        	views.add(iv);
        }
        vp = (ViewPager) findViewById(R.id.viewpager);
        //初始化Adapter
        vpAdapter = new ViewPagerAdapter(views);
        vp.setAdapter(vpAdapter);
        //绑定回调
        vp.setOnPageChangeListener(this);
        
        //初始化底部小点
        initDots();
        
    }
    
    private void initDots() {
		LinearLayout ll = (LinearLayout) findViewById(R.id.ll);

		dots = new ImageView[pics.length];

		//循环取得小点图片
		for (int i = 0; i < pics.length; i++) {
			dots[i] = (ImageView) ll.getChildAt(i);
			dots[i].setEnabled(true);//都设为灰色
			dots[i].setOnClickListener(this);
			dots[i].setTag(i);//设置位置tag，方便取出与当前位置对应
		}

		currentIndex = 0;
		dots[currentIndex].setEnabled(false);//设置为白色，即选中状态
    }
    
    /**
     *设置当前的引导页 
     */
    private void setCurView(int position)
    {
		if (position < 0 || position >= pics.length) {
			return;
		}

		vp.setCurrentItem(position);
    }

    /**
     *这只当前引导小点的选中 
     */
    private void setCurDot(int positon)
    {
		if (positon < 0 || positon > pics.length - 1 || currentIndex == positon) {
			return;
		}

		dots[positon].setEnabled(false);
		dots[currentIndex].setEnabled(true);

		currentIndex = positon;
    }

    //当滑动状态改变时调用
	@Override
	public void onPageScrollStateChanged(int arg0) {
		// TODO Auto-generated method stub
		
	}

	//当当前页面被滑动时调用
	@Override
	public void onPageScrolled(int arg0, float arg1, int arg2) {
		// TODO Auto-generated method stub
		
	}

	//当新的页面被选中时调用
	@Override
	public void onPageSelected(int arg0) {
		//设置底部小点选中状态
		setCurDot(arg0);
	}

	@Override
	public void onClick(View v) {
		int position = (Integer)v.getTag();
		setCurView(position);
		setCurDot(position);
	}
}
{% endcodeblock %}

注意实现OnClickListener, OnPageChangeListener接口，监听小点的点击事件以及viewPager的滑动，在相应的回调方法中设置小点的enable状态，我相信这个部分代码比我讲的清楚，就是判断当前选中的位置对相应的小点进行设置～

可以看到ViewPager还是一个非常简单，也非常实用的一个控件。










