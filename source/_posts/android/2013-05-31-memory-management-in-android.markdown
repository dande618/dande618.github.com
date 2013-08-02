---
layout: post
title: "[转] Android 操作系统的内存回收机制"
date: 2013-05-31 15:18
comments: true
categories: android
---
全文见 <http://www.oschina.net/question/129540_64132>

Android 是一款基于 Linux 内核，面向移动终端的操作系统。为适应其作为移动平台操作系统的特殊需要，谷歌对其做了特别的设计与优化，使应用程序关闭但不退出，并由操作系统进行进程 的回收管理。本文在 Application Framework 与 Linux 内核两个层次上，以进程为粒度，对 Android 操作系统的进程资源回收机制进行了剖析。读者可以从本文获得对 Android 应用程序的生存周期的进一步理解，从而更加合理、高效地构建应用程序。
<!-- more -->

##Android APP 的运行环境

Android 是一款基于 Linux 内核，面向移动终端的操作系统。为适应其作为移动平台操作系统的特殊需要，谷歌对其做了特别的设计与优化，使得其进程调度与资源管理与其他平台的 Linux 有明显的区别。主要包含下面几个层次：

###Application Framework 

Application Framework 将整个操作系统分隔成两个部分。对应用开发者而言，所有 APP 都是运行在 Application Framework 之上，而并不需要关心系统底层的情况。Application Framework 层为应用开发者提供了丰富的应用编程接口，如 Activity Manager，Content Provider，Notification Manager，以及各种窗口 Widget 资源等。在 Application Framework 层，Activity 是一个 APP 最基本的组成部分。一般每个 Activity 对应于屏幕上的一个视图（或者说一屏），一个 APP 可以有一个或者多个 Activity。应用程序被打包成 .apk 格式的文件，由 Dalvik VM 解释执行。

###Dalvik VM 

Dalvik 虚拟机采用寄存器架构，而不是 JVM 的栈结构。Java 程序编译后的 .class 文件并不能在 Dalvik 中解释执行。因此 Google 提供了一个 dx 工具，用于将 .class 文件转换成 Dalivk 能够识别的 .dex 格式。具体 Dalvik VM 的细节不是本文重点，以下不再讨论。

###Linux kernel 

由上所述，所有的 APP 都是由 Java 代码编写并在 Dalvik VM 中得到解释执行。在 Android 操作系统中，每个 Dalvik VM 的每个 Instance 都对应于 Linux 内核中的一个进程。可以使用 adb shell 工具查看系统中的当前进程。如下图所示，Android2.3.3 启动后内核中的进程列表。


图 1. Android 2.3 中的进程列表（部分）

{% img http://image142.poco.cn/mypoco/myphoto/20130531/20/4309479020130531201129048.png %}

图 1 中，UID 标识为 app_xx 的每一项都是一个 app 所占用的进程，可见 Android 设计使得每个应用程序由一个独立的 Dalvik 实例解释执行，而每个 Linux 内核进程加载一个 Dalvik 实例，通过这种方式提供 app 的运行环境。如此，每个 APP 的资源被完全屏蔽，互不干扰。虽然同时引入了进程间通信的困难，但也带来了更强的安全性。

###Android 内存回收原则

下面将从 Application Framework 和 Linux kernel 两个层次分析 Android 操作系统的资源管理机制。

Android 之所以采用特殊的资源管理机制，原因在于其设计之初就是面向移动终端，所有可用的内存仅限于系统 RAM，必须针对这种限制设计相应的优化方案。当 Android 应用程序退出时，并不清理其所占用的内存，Linux 内核进程也相应的继续存在，所谓“退出但不关闭”。从而使得用户调用程序时能够在第一时间得到响应。当系统内存不足时，系统将激活内存回收过程。为了不因 内存回收影响用户体验（如杀死当前的活动进程），Android 基于进程中运行的组件及其状态规定了默认的五个回收优先级：

- IMPORTANCE_FOREGROUND:

- IMPORTANCE_VISIBLE:

- IMPORTANCE_SERVICE:

- IMPORTANCE_BACKGROUND:

- IMPORTANCE_EMPTY:

这几种优先级的回收顺序是 Empty process、Background process、Service process、Visible process、Foreground process。关于划分原则参见 http://developer.android.com/guide/topics/fundamentals/processes-and-threads.html文件中。

ActivityManagerService 集中管理所有进程的内存资源分配。所有进程需要申请或释放内存之前必须调用 ActivityManagerService 对象，获得其“许可”之后才能进行下一步操作，或者 ActivityManagerService 将直接“代劳”。类 ActivityManagerService 中涉及到内存回收的几个重要的成员方法如 下：trimApplications()，updateOomAdjLocked()，activityIdleInternal() 。这几个成员方法主要负责 Android 默认的内存回收机制，若 Linux 内核中的内存回收机制没有被禁用，则跳过默认回收。

###默认回收过程

Android 操作系统中的内存回收可分为两个层次，即默认内存回收与内核级内存回收……

#Android架构

<http://www.cnblogs.com/skynet/archive/2010/04/15/1712924.html>

##1、架构图直观

下面这张图展示了Android系统的主要组成部分：

{% img center http://images.cnblogs.com/cnblogs_com/skynet/WindowsLiveWriter/Androidandroid_1C63/Android_system_architecture_thumb.jpg Android系统架构（来源于：android sdk） %}

可以很明显看出，Android系统架构由5部分组成，分别是：Linux Kernel、Android Runtime、Libraries、Application Framework、Applications。第二部分将详细介绍这5个部分。

##2、架构详解

现在我们拿起手术刀来剖析各个部分。其实这部分SDK文档已经帮我们做得很好了，我们要做的就是拿来主义，然后再加上自己理解。下面自底向上分析各层。

###2.1、Linux Kernel

Android基于Linux 2.6提供核心系统服务，例如：安全、内存管理、进程管理、网络堆栈、驱动模型。Linux Kernel也作为硬件和软件之间的抽象层，它隐藏具体硬件细节而为上层提供统一的服务。

如果你学过计算机网络知道OSI/RM，就会知道分层的好处就是使用下层提供的服务而为上层提供统一的服务，屏蔽本层及以下层的差异，当本层及以下层发生了变化不会影响到上层。也就是说各层各司其职，各层提供固定的SAP（Service Access Point），专业点可以说是高内聚、低耦合。

如果你只是做应用开发，就不需要深入了解Linux Kernel层。

###2.2、Android Runtime

Android包含一个核心库的集合，提供大部分在Java编程语言核心类库中可用的功能。每一个Android应用程序是Dalvik虚拟机中的实例，运行在他们自己的进程中。Dalvik虚拟机设计成，在一个设备可以高效地运行多个虚拟机。Dalvik虚拟机可执行文件格式是.dex，dex格式是专为Dalvik设计的一种压缩格式，适合内存和处理器速度有限的系统。

大多数虚拟机包括JVM都是基于栈的，而Dalvik虚拟机则是基于寄存器的。两种架构各有优劣，一般而言，基于栈的机器需要更多指令，而基于寄存器的机器指令更大。dx 是一套工具，可以將 Java .class 转换成 .dex 格式。一个dex文件通常会有多个.class。由于dex有時必须进行最佳化，会使文件大小增加1-4倍，以ODEX结尾。

Dalvik虚拟机依赖于Linux 内核提供基本功能，如线程和底层内存管理。

###2.3、Libraries

Android包含一个C/C++库的集合，供Android系统的各个组件使用。这些功能通过Android的应用程序框架（application framework）暴露给开发者。下面列出一些核心库：

- 系统C库——标准C系统库（libc）的BSD衍生，调整为基于嵌入式Linux设备

- 媒体库——基于PacketVideo的OpenCORE。这些库支持播放和录制许多流行的音频和视频格式，以及静态图像文件，包括MPEG4、 H.264、 MP3、 AAC、 AMR、JPG、 PNG

- 界面管理——管理访问显示子系统和无缝组合多个应用程序的二维和三维图形层

- LibWebCore——新式的Web浏览器引擎,驱动Android 浏览器和内嵌的web视图

- SGL——基本的2D图形引擎 

- 3D库——基于OpenGL ES 1.0 APIs的实现。库使用硬件3D加速或包含高度优化的3D软件光栅

- FreeType ——位图和矢量字体渲染

- SQLite ——所有应用程序都可以使用的强大而轻量级的关系数据库引擎

###2.4、Application Framework

通过提供开放的开发平台，Android使开发者能够编制极其丰富和新颖的应用程序。开发者可以自由地利用设备硬件优势、访问位置信息、运行后台服务、设置闹钟、向状态栏添加通知等等，很多很多。

开发者可以完全使用核心应用程序所使用的框架APIs。应用程序的体系结构旨在简化组件的重用，任何应用程序都能发布他的功能且任何其他应用程序可以使用这些功能（需要服从框架执行的安全限制）。这一机制允许用户替换组件。

所有的应用程序其实是一组服务和系统，包括：

- 视图（View）——丰富的、可扩展的视图集合，可用于构建一个应用程序。包括包括列表、网格、文本框、按钮，甚至是内嵌的网页浏览器 

- 内容提供者（Content Providers）——使应用程序能访问其他应用程序（如通讯录）的数据，或共享自己的数据 

- 资源管理器（Resource Manager）——提供访问非代码资源，如本地化字符串、图形和布局文件 

- 通知管理器（Notification Manager）——使所有的应用程序能够在状态栏显示自定义警告 

- 活动管理器（Activity Manager）——管理应用程序生命周期,提供通用的导航回退功能 

###2.5、Applications

Android装配一个核心应用程序集合，包括电子邮件客户端、SMS程序、日历、地图、浏览器、联系人和其他设置。所有应用程序都是用Java编程语言写的。更加丰富的应用程序有待我们去开发！

##3、总结

从上面我们知道Android的架构是分层的，非常清晰，分工很明确。Android本身是一套软件堆叠(Software Stack)，或称为「软件叠层架构」，叠层主要分成三层：操作系统、中间件、应用程序。从上面我们也看到了开源的力量，一个个熟悉的开源软件在这里贡献了自己的一份力量。

现在我们对android的系统架构有了一个整体上的了解，我将用一个例子来深入体会一下，但是考虑到此系列希望0基础的人也能看懂，在介绍例子之前将介绍一下Android应用程序的原理及一些术语，可能需要几篇来介绍。敬请关注！

作者：吴秦

出处：http://www.cnblogs.com/skynet/