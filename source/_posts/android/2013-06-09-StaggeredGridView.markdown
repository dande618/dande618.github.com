---
layout: post
title: "Android StaggeredGridView"
date: 2013-06-09 15:13
comments: true
categories: android
---
Staggered adj.错列的，叉排的

一个类似Google Plus Android 上使用的动态GridView的控件，可以用来实现瀑布流效果。

{% img center http://image14.poco.cn/mypoco/myphoto/20130609/19/430947902013060919105709.png %}

github上的一个实际例子 <https://github.com/chrisjenx/StaggeredGridView>

{% img center http://image14.poco.cn/mypoco/myphoto/20130609/19/4309479020130609190127093.png %}
<!-- more -->
在adapter里，根据position来getview：

{% codeblock lang:java %}
        @Override
        public View getView(int position, View convertView, ViewGroup parent)
        {
            final LayoutParams lp;
            final View v;
            switch (position)
            {
                case 29:
                    v = mInflater.inflate(R.layout.element_header, parent, false);
                    lp = new LayoutParams(v.getLayoutParams());
                    lp.span = mSGV.getColumnCount();
                    break;
                default:
                    v = mInflater.inflate(R.layout.element_item, parent, false);
                    lp = new LayoutParams(v.getLayoutParams());
                    lp.span = 2;
                    break;
            }
            v.setLayoutParams(lp);
            return v;
        }
{% endcodeblock %}

###实现拖动到最后，自动加载下一页的一种方法

判断position到达getCount()的最大值时，调用其他对象的方法加载更多数据，并使用回调的方式，调用notifyDataSetChanged()方法，加载更多的view。
