---
layout: post
title: "Launcher中锁定图标位置"
date: 2013-07-22 19:12
comments: true
categories: android
---
###一、使图标不能被挤开

在Launcher中，AllAppsButton的图标是在Hotseat.java加载的，并且设置了对应的layoutparams的属性 canReorder = false;

如字面上的意思，设置了此属性为false的layoutparams中的图标不能被重新排序，即不能被直接或间接挤开。但是可以移动（AllApps按钮不能移动，使因为在hotseat.java单独加载时，没有绑定长按监听器）。当拖动其他图标到此处时，可以形成文件夹。所以如果对此有需求，还要更改其他对应的代码。

通过对源码的搜索，发现只有两处用到了这个属性。

一处是在rearrangementExists方法

{% codeblock lang:java %}
if (Rect.intersects(r0, r1)) {  
    if (!lp.canReorder) {  
        return false;  
    }  
    mIntersectingViews.add(child);  
}  
{% endcodeblock %}

注释掉此处后，图标可以被直接挤开。 

另一处是pushViewsToTempLocation方法

{% codeblock lang:java %}
if (!cluster.views.contains(v) && v != dragView) {  
    if (cluster.isViewTouchingEdge(v, whichEdge)) {  
        LayoutParams lp = (LayoutParams) v.getLayoutParams();  
        if (!lp.canReorder) {  
            // The push solution includes the all apps button,  
            // this is not viable.  
            fail = true;  
            break;  
        }  
{% endcodeblock %}

注释掉此处后，图标可以被间接挤开，也就是挤压相邻的另一个图标。