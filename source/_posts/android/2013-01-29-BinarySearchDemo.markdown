---
layout: post
title: "二分查找演示程序"
date: 2013-02-18 22:00
comments: true
categories: android
---

##二分查找介绍

　　二分查找又称折半查找，优点是比较次数少，查找速度快，平均性能好；其缺点是要求待查表为有序表，且插入删除困难。因此，折半查找方法适用于不经常变动而查找频繁的有序列表。
<!-- more -->
##二分查找原理

　　首先，假设表中元素是按升序排列，将表中间位置记录的关键字与查找关键字比较，如果两者相等，则查找成功；否则利用中间位置记录将表分成前、后两个子表，如果中间位置记录的关键字大于查找关键字，则进一步查找前一子表，否则进一步查找后一子表。重复以上过程，直到找到满足条件的记录，使查找成功，或直到子表不存在为止，此时查找不成功。 

##演示程序

1. 给出一组固定升序排列的数组

2. 用户输入查找数字（限定0-9之间）

3. 点击按键逐次查找，显示查找次数和边界值变化

4. 查找完成后可复位成初始状态

##程序代码分析

###初始化

　　给出数组和初始数值。其中mLow为上边界，mHigh为上边界，mMiddle为中间值。mTimes为计次变量。

{% codeblock lang:java %}
	final int[] src = new int[] { 1, 3, 5, 7, 8, 9 };
	private int mLow = 0;
	private int mHigh = src.length - 1;
	private int mTimes = 0;
	private int mMiddle = -1;
{% endcodeblock %}

__设置屏幕、组件，绑定监听器__

　　批量设置textview的方法

{% codeblock lang:java %}
	TextView[] tv = null;
	Integer[] view = { R.id.num0, R.id.num1, R.id.num2, R.id.num3,R.id.num4, R.id.num5 };
	tv = new TextView[6];
	for (int i = 0; i < 6; i++) {
		tv[i] = (TextView) findViewById(view[i]);
		tv[i].setText(src[i] + "");
{% endcodeblock %}

###设置“搜索”按键的监听类

__判断计次变量mTimes__

　　为-1时表示查询结束，按查询键不再反应。

__mLow与mHigh相比较__

　　如果mLow大于mHigh，则说明未查找到，查找结束。

　　如果mLow小于mHigh，则令mMiddle为二者和的一半。

{% codeblock lang:java %}
	if (mLow <= mHigh) {
		mMiddle = (mLow + mHigh) / 2;
{% endcodeblock %}

__mMiddle与目标查询值des相比较__

■ 相等，则找到

{% codeblock lang:java %}
if (des == src[mMiddle]) {
	//显示完成
	mTimes = -1;
}
{% endcodeblock %}

■ mMiddle大，则目标在左侧，令mHigh为mMiddle-1

{% codeblock lang:java %}
else if (des < src[mMiddle]) {
	mHigh = mMiddle - 1;
	mTimes++;
}
{% endcodeblock %}
■ mMiddle小，则目标在右侧，令mLow为mMiddle+1
{% codeblock lang:java %}
else {
	mLow = mMiddle + 1;
	mTimes++;
}
{% endcodeblock %}
###设置“复位”按键的监听类

　　属性、组件文字还原至初始值

###其他细节备忘

1. 查找第一次时将editview设为不可获得焦点（不可编辑），按复位时还原。

{% codeblock lang:java %}
editText.setFocusable(false);
{% endcodeblock %}

2. 获得editview内用户输入的值

{% codeblock lang:java %}
int des = Integer.parseInt(editText.getText().toString());
{% endcodeblock %}

3. 判断输入为空时提示

{% codeblock lang:java %}
if (editText.getText().toString().length() == 0) {
	result.setText("请输入要查询的数字");
}
{% endcodeblock %}

4. 查找成功变色（颜色0xFF0000FF，0x是代表颜色整数的标记，FF是表示透明度，0000FF表示颜色）

{% codeblock lang:java %}
tv[mMiddle].setTextColor(0xFF0000FF);
{% endcodeblock %}
<br/>
<hr>
<br/>
<img style="width:600px;" src="http://img14.poco.cn/mypoco/myphoto/20130218/21/4309479020130218213456040.png"  alt="图" />
<br/>
<hr>
<br/>

源码地址：<https://github.com/dande618/BinarySearchDemo.git>
