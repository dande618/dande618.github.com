---
layout: post
title: "Android ContentProvider"
date: 2013-04-28 20:01
comments: true
categories: android
---
ContentProvider是Android不同应用程序之间进行数据交换的标准API。ContentProvider是Android的四大组件之一（Activity、ContentProvider、Service、BroadcastReceiver），他们都需要在Mainfest文件中配置。ContentProvider以Uri对外提供数据，其他应用使用ContentResolver根据Uri去访问操作指定数据。
<!-- more -->

其中ContentProvider负责

+ 组织应用程序的数据；

+ 向其他应用程序提供数据；

ContentResolver则负责

+ 获取ContentProvider提供的数据；

+ 修改/添加/删除更新数据等；

## Uri

与Url较为相似。由 content:// 、authority 和 words 三部分组成

比如，访问words数据 content://com.test.providertest/words

访问words数据中id为2的数据 content://com.test.providertest/words/2

将string转化为uri，可以使用Uri.parse(String)静态方法

## ContentResolver

使用 getContentResolver()方法获取到ContentResolver对象，然后可以使用对应的ContentProvider中的方法来操作数据了。











