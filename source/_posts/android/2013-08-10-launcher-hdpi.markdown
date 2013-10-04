---
layout: post
title: "源码Launcher编译，使用mdpi的资源"
date: 2013-08-10 8:10
comments: true
categories: android
---
4.2.2的源码，进行对Launcher2的修改，之后使用mm或mmm编译，使用adb push安装到平板中。

因为使用的一代nexus7，所以一般程序调用资源的是drawable_sw600dp_hdpi下的图片资源，但是按照上面方法编译出的Launcher使用drawable_sw600dp_mdpi的图片资源。

在网上搜索后，解决方法是

> 默认编译mdpi的apk，如果要编译hdpi，需要在./build/target/product/full.mk文件中添加： 

{% codeblock %}
PRODUCT_AAPT_CONFIG := normal hdpi 
PRODUCT_AAPT_PREF_CONFIG := hdpi 
{% endcodeblock %}

经过测试，

PRODUCT_AAPT_CONFIG指定了hdpi的话，就会使用hdpi的资源，而不使用mdpi资源！指定xhdpi的话，就不会使用hdpi的资源！

PRODUCT_AAPT_PREF_CONFIG指定hdpi，编译出的APK中会有hdpi、mdpi等等，没有xhdpi和xxhdpi，默认是mdpi，所以编译出的APK中就没有hdpi。

aapt 是android assert packaging tool的缩写，即安卓打包工具。

加上这两句以后，会影响其参数，只为一种设备匹配文件。而eclipse生成的程序可以为多种设备匹配。