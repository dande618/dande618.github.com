---
layout: post
title: "launcher2的Folder布局相关"
date: 2013-07-19 20:50
comments: true
categories: android
---
这里只写文件图标相关，主要是FolderIcon.java控制。可以看到，FolderIcon是LinearLayout的子类。

观察/res/layout-land(port)/folder_icon.xml，FolderIcon是由一个ImageView和一个BubbleTextView组成，这个ImageView是FolderIcon的背景。

在这里可以调整文件夹图标的大小。在BubbleTextView对应的的style里调整padding、字体大小、文字与图标间距等。

再看FolderIcon.java。

有一些final的属性，比如显示preview的图标的个数，默认是3个。以及相关动画显示的时间等。

Folder的加载可以参考这篇博客 <http://blog.csdn.net/wdaming1986/article/details/7748738>

显示的程序图标预览在computePreviewItemDrawingParams()方法，之后通过drawPreviewItem()绘制在屏幕上。

当拖动图标移动到一个文件夹上时，会调用CellLayout的onDraw方法，显示蓝色的外框和内部背景。

当点击Hotseat上FolderIcon，打开文件夹后，Hotseat上会显示表示文件夹位置的一个小篮圈，这个也是在CellLayout的onDraw方法里实现的。在growAndFadeOutFolderIcon()方法判断是不是位于Hotseat。

调用顺序依次是onClick【Launcher.java】 -> handleFolderClick【Launcher.java】 -> openFolder【Launcher.java】 -> growAndFadeOutFolderIcon【Launcher.java】 -> setFolderLeaveBehindCell【CellLayout.java】 -> invalidate【CellLayout.java】