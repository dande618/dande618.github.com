---
layout: post
title: "【转载】convertView&setTag方法的一点理解  "
date: 2013-03-22 22:02
comments: true
categories: android
---
convertView&setTag方法的一点理解

转载来源 <http://blog.163.com/freemanls@126/blog/static/164585061201171210504864/>
<!-- more -->

##前言

　　首先我们要知道setTag方法是干什么的，SDK解释为

Tags

> Unlike IDs, tags are not used to identify views. Tags are essentially an extra piece of information that can be associated with a view. They are most often used as a convenience to store data related to views in the views themselves rather than by putting them in a separate structure.

　　Tag不像ID是用标示view的。Tag从本质上来讲是就是相关联的view的额外的信息。它们经常用来存储一些view的数据，这样做非常方便而不用存入另外的单独结构。

##convertView中的TAG

1.对于使用了LayoutInflater对象进行View扩充的Tag的使用

　　在之前，在adapter中，我们在getView中是这么些的代码：

{% codeblock lang:java %}
public View getView(int position, View convertView, ViewGroup parent) {
ViewHolder holder = null;
if (convertView == null) {
holder = new ViewHolder();
convertView = inflater.inflate(R.layout.vlist2, null);
holder.img = (ImageView) convertView.findViewById(R.id.img);
holder.title = (TextView) convertView.findViewById(R.id.title);
holder.info = (TextView)
convertView.findViewById(R.id.info);
// setTag的妙用
convertView.setTag(holder);
} else {
// setTag的妙用
holder = (ViewHolder) convertView.getTag();
}
//……略
}
{% endcodeblock %}

　　注意标红的地方，他们是使用了Tag的。

　　首先我们要知道setTag方法是干什么的，他是给View对象的一个标签，标签可以是任何内容，我们这里把他设置成了一个对象，因为我们是把vlist2.xml的元素抽象出来成为一个类ViewHolder，用了setTag，这个标签就是ViewHolder实例化后对象的一个属性。我们之后对于ViewHolder实例化的对象holder的操作，都会因为java的引用机制而一直存活并改变convertView的内容，而不是每次都是去new一个。我们就这样达到的重用——我希望我说清楚了。如果有更简单的解释，请指教。

　　这是我们在Adapter中的使用，那么我们在这里不使用Tag标签会怎么样呢？

　　我们试想，如果我们不用Tag标签，那么我们的对象如何与convertView缓存结合并达到合理的效率利用？貌似答案并不明朗——所以使用Tag是比较明智的做法。

2.对于没有使用LayoutInflater对象进行View扩充的Tag的使用。
{% codeblock lang:java %}
if (convertView != null) {
view = convertView;
...
} else {
view = new Xxx(...);
...
}
{% endcodeblock %}

　　这是我们的程序，我们看到，貌似没有用Tag——是的，当没有使用LayoutInflater进行View的扩充的时候，是没有必要用的，虽然也可以用。

3.对于其他View的Tag使用

　　我们可以对所有的View对象进行操作，至于怎么用，就看作者怎么想的了，下面举例说一个View的子类button对于tag的一个使用。

　　直接贴代码了：

{% codeblock lang:java %}
public class ButtonTagTestActivity extends Activity implements OnClickListener {
/** Called when the activity is first created. */
@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
Button button1 = (Button) findViewById(R.id.button1);
Button button2 = (Button) findViewById(R.id.button2);
Button button3 = (Button) findViewById(R.id.button3);
button1.setTag(1);
button2.setTag(2);
button3.setTag(3);
button1.setOnClickListener(this);
}
@Override
public void onClick(View arg0) {
// TODO Auto-generated method stub
int tag = (Integer) arg0.getTag();
switch (tag) {
case 1: {
Toast.makeText(this, "我是button1", Toast.LENGTH_LONG).show();
break;
}
case 2: {
Toast.makeText(this, "我是button2", Toast.LENGTH_LONG).show();
break;
}
case 3: {
Toast.makeText(this, "我是button3", Toast.LENGTH_LONG).show();
break;
}
default: {
break;
}
}
}
}
{% endcodeblock %}

　　Xml页面代码就不贴了。这个例子是点击界面上的3个button然后会显示用户点击的按钮。我们的程序是实现了页面全局监听，在监听前设置了每个button的tag，之后我们在switch的时候，使用getTag取出的标签来看是什么操作。

　　这样做的好处是可以将监听集中管理，提高代码的易读性——当然，这是我的自我理解。

##后记

　　看了这么多的实例，我想已经明白了Tag以及convertView。

　　对我们知道了Tag的作用就是设置标签，标签可以是任意玩意。

　　以及convertView是如何在程序中使代码运行变的效率的：利用缓存convertView尽可能少实例化同样结构体的对象；