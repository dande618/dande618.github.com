---
layout: post
title: "Android之MVC模式"
date: 2013-05-29 19:07
comments: true
categories: android
---
转载 <http://www.cnblogs.com/devinzhang/archive/2012/01/26/2329869.html>

__MVC (Model-View-Controller)：M是指逻辑模型，V是指视图模型，C则是控制器。__一个逻辑模型可以对于多种视图模型，比如一批统计数据你可以分别用柱状图、饼图来表示。一种视图模型也可以对于多种逻辑模型。使用MVC的目的是__将M和V的实现代码分离__，从而使同一个程序可以使用不同的表现形式，而C存在的目的则是确保M和V的同步，一旦M改变，V应该同步更新，这与《设计模式》中的观察者模式是完全一样。

MVC好处：__从用户的角度出发，用户可以根据自己的需求，选择自己合适的浏览数据的方式__。比如说，对于一篇在线文档，用户可以选择以HTML网页的方式阅读，也可以选择以pdf的方式阅读。从开发者的角度，MVC把应用程序的逻辑层与界面是完全分开的，最大的好处是：界面设计人员可以直接参与到界面开发，程序员就可以把精力放在逻辑层上。而不是像以前那样，设计人员把所有的材料交给开发人员，由开发人员来实现界面。在Eclipes工具中开发Android采用了更加简单的方法，设计人员在DroidDraw中设计界面，以XML方式保存，在Eclipes中直接打开就可以看到设计人员设计的界面。 

<!-- more -->

Android中界面部分也采用了当前比较流行的MVC框架，在Android中： 

1. 视图层（View）：一般采用XML文件进行界面的描述，使用的时候可以非常方便的引入。当然，如何你对Android了解的比较的多了话，就一定可以想到在Android中也可以使用JavaScript+HTML等的方式作为View层，当然这里需要进行Java和JavaScript之间的通信，幸运的是，Android提供了它们之间非常方便的通信实现。     

2. 控制层（Controller）：Android的控制层的重任通常落在了众多的Acitvity的肩上，这句话也就暗含了不要在Acitivity中写代码，要通过Activity交割Model业务逻辑层处理，这样做的另外一个原因是Android中的Acitivity的响应时间是5s，如果耗时的操作放在这里，程序就很容易被回收掉。

3. 模型层（Model）：对数据库的操作、对网络等的操作都应该在Model里面处理，当然对业务计算等操作也是必须放在的该层的。就是应用程序中二进制的数据。

在Android SDK中的数据绑定，也都是采用了与MVC框架类似的方法来显示数据。在控制层上将数据按照视图模型的要求（也就是Android SDK中的Adapter）封装就可以直接在视图模型上显示了，从而实现了数据绑定。比如显示Cursor中所有数据的ListActivity，其视图层就是一个ListView，将数据封装为ListAdapter，并传递给ListView，数据就在ListView中现实。 