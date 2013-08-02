---
layout: post
title: "JAVA单例模式"
date: 2013-06-10 19:28
comments: true
categories: 
---
<http://blog.csdn.net/it_man/article/details/5787567>

1.预先加载法 

{% codeblock lang:java %}
class S1 {
    private S1() {
        System.out.println("ok1");
    }

    private static S1 instance = new S1();

    public static S1 getInstance() {
        return instance;
    }
}
{% endcodeblock %}

优点：   

1.线程安全的 

2.在类加载的同时已经创建好一个静态对象,调用时反应速度快。

缺点： 资源利用效率不高，可能getInstance永远不会执行到，但是执行了该类的其他静态方法或者加载了该类（class.forName），那么这个实例仍然初始化了 

<!-- more -->

2.initialization on demand,延迟加载法  (考虑多线程) 


{% codeblock lang:java %}
class S2 {  
    private S2() {  
        System.out.println("ok2");  
    }  
  
    private static S2 instance = null;  
  
    public static synchronized S2 getInstance() {  
        if (instance == null) instance = new S2();  
        return instance;  
    }  
}  
{% endcodeblock %}

优点： 资源利用率高,不执行getInstance就不会被实例，可以执行该类其他静态方法。 

缺点： 第一次加载时发应不快  ，多线程使用不必要的同步开销大 

3.initialization on demand double check 双重检测( 考虑多线程 ) 

{% codeblock lang:java %}
class S3 {  
    private S3() {  
        System.out.println("ok3");  
    }  
  
    private static S3 instance = null;  
  
    public static S3 getInstance() {  
        if (instance == null) {  
            synchronized (S3.class) {  
                if (instance == null)  
                    instance = new S3();  
            }  
        }  
        return instance;  
    }  
}  
{% endcodeblock %}

优点： 资源利用率高, 不执行getInstance就不会被实例，可以执行该类其他静态方法。 

缺点： 第一次加载时发应不快  ，由于java 内存模型一些原因偶尔会失败 

4.initialization on demand holder  (考虑多线程) 


{% codeblock lang:java %}
class S4 {  
    private S4() {  
        System.out.println("ok4");  
    }  
  
    private static class S4Holder {  
        static S4 instance = new S4();  
    }  
  
  
    public static S4 getInstance() {  
        return S4Holder.instance;  
    }  
}  
{% endcodeblock %}

优点： 资源利用率高, 不执行getInstance就不会被实例，可以执行该类其他静态方法。 

缺点： 第一次加载时发应不快 

总结： 一般采用 1 即可，若对资源十分在意也可考虑 4 ，不要使用2，3了。 


测试代码：（暂不探讨Class.forName类加载机制） 


{% codeblock lang:java %}
/**  
 * Created by IntelliJ IDEA.  
 * User: yiminghe  
 * Date: 2009-6-8  
 * Time: 19:20:52  
 */  
public class Singleton {   
    public static void main(String[] args) throws Exception{   
        System.out.println(Class.forName("S1"));   
        System.out.println(Class.forName("S2"));   
        System.out.println(Class.forName("S3"));   
        System.out.println(Class.forName("S4"));   
    }   
}   
  
/*  
    预先加载法  
    优点：1.线程安全的,  
          2.在类加载的同时已经创建好一个静态对象,调用时反应速度快。  
 
    缺点： 资源利用效率不高，可能这个单例不会需要使用也被系统加载  
 */  
class S1 {   
    private S1() {   
        System.out.println("ok1");   
    }   
  
  
    private static S1 instance = new S1();   
  
    public static S1 getInstance() {   
        return instance;   
    }   
}   
  
/*  
    initialization on demand,延迟加载法  (考虑多线程)  
    优点：1.资源利用率高  
    缺点：第一次加载是发应不快  ，多线程使用不必要的同步开销大  
 
 */  
class S2 {   
    private S2() {   
        System.out.println("ok2");   
    }   
  
    private static S2 instance = null;   
  
    public static synchronized S2 getInstance() {   
        if (instance == null) instance = new S2();   
        return instance;   
    }   
}   
  
  
/*  
    initialization on demand - double check 延迟加载法改进之双重检测  (考虑多线程)  
    优点：1.资源利用率高  
    缺点：第一次加载是发应不快  ，由于java 内存模型一些原因偶尔会失败  
 
 */  
class S3 {   
    private S3() {   
        System.out.println("ok3");   
    }   
  
    private static S3 instance = null;   
  
    public static S3 getInstance() {   
        if (instance == null) {   
            synchronized (S3.class) {   
                if (instance == null)   
                    instance = new S3();   
            }   
        }   
        return instance;   
    }   
}   
  
  
/*  
   initialization on demand holder  (考虑多线程)  
   优点：1.资源利用率高  
   缺点：第一次加载是发应不快  
 
*/  
class S4 {   
    private S4() {   
        System.out.println("ok4");   
    }   
  
    private static class S4Holder {   
        static S4 instance = new S4();   
    }   
  
  
    public static S4 getInstance() {   
        return S4Holder.instance;   
    }   
}  
{% endcodeblock %}


作为对象的创建模式[GOF95]， 单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。这个类称为单例类。

注：本文乃阎宏博士的《Java与模式》一书的第十五章。

引言

单例模式的要点

单例单例

显然单例模式的要点有三个；一是某各类只能有一个实例；二是它必须自行创建这个事例；三是它必须自行向整个系统提供这个实例。在下面的对象图中，有一个"单例对象"，而"客户甲"、"客户乙" 和"客户丙"是单例对象的三个客户对象。可以看到，所有的客户对象共享一个单例对象。而且从单例对象到自身的连接线可以看出，单例对象持有对自己的引用。


资源管理

一些资源管理器常常设计成单例模式。

在计算机系统中，需要管理的资源包括软件外部资源，譬如每台计算机可以有若干个打印机，但只能有一个Printer Spooler， 以避免两个打印作业同时输出到打印机中。每台计算机可以有若干传真卡，但是只应该有一个软件负责管理传真卡，以避免出现两份传真作业同时传到传真卡中的情况。每台计算机可以有若干通信端口，系统应当集中管理这些通信端口，以避免一个通信端口同时被两个请求同时调用。

需要管理的资源包括软件内部资源，譬如，大多数的软件都有一个（甚至多个）属性（properties）文件存放系统配置。这样的系统应当由一个对象来管理一个属性文件。

需要管理的软件内部资源也包括譬如负责记录网站来访人数的部件，记录软件系统内部事件、出错信息的部件，或是对系统的表现进行检查的部件等。这些部件都必须集中管理，不可政出多头。

这些资源管理器构件必须只有一个实例，这是其一；它们必须自行初始化，这是其二；允许整个系统访问自己这是其三。因此，它们都满足单例模式的条件，是单例模式的应用。

一个例子：Windows 回收站

Windows 9x 以后的视窗系统中都有一个回收站，下图就显示了Windows 2000 的回收站。


在整个视窗系统中，回收站只能有一个实例，整个系统都使用这个惟一的实例，而且回收站自行提供自己的实例。因此，回收站是单例模式的应用。

双重检查成例

在本章最后的附录里研究了双重检查成例。双重检查成例与单例模式并无直接的关系，但是由于很多C 语言设计师在单例模式里面使用双重检查成例，所以这一做法也被很多Java 设计师所模仿。因此，本书在附录里提醒读者，双重检查成例在Java 语言里并不能成立，详情请见本章的附录。
单例模式的结构

单例模式有以下的特点：

- 单例类只可有一个实例。

- 单例类必须自己创建自己这惟一的实例。

- 单例类必须给所有其他对象提供这一实例。

虽然单例模式中的单例类被限定只能有一个实例，但是单例模式和单例类可以很容易被推广到任意且有限多个实例的情况，这时候称它为多例模式（Multiton Pattern） 和多例类（Multiton Class），请见"专题：多例（Multiton ）模式与多语言支持"一章。单例类的简略类图如下所示。

由于Java 语言的特点，使得单例模式在Java 语言的实现上有自己的特点。这些特点主要表现在单例类如何将自己实例化上。

饿汉式单例类饿汉式单例类是在Java 语言里实现得最为简便的单例类，下面所示的类图描述了一个饿汉式单例类的典型实现。

从图中可以看出，此类已经自已将自己实例化。

代码清单1：饿汉式单例类


{% codeblock lang:java %}
public class EagerSingleton 
{ 
private static final EagerSingleton m_instance = 
new EagerSingleton(); 
/** 
* 私有的默认构造子 
*/ 
private EagerSingleton() { } 
/** 
* 静态工厂方法 
*/ 
public static EagerSingleton getInstance() 
{
return m_instance; 
}
} 
{% endcodeblock %}

读者可以看出，在这个类被加载时，静态变量m_instance 会被初始化，此时类的私有构造子会被调用。这时候，单例类的惟一实例就被创建出来了。

Java 语言中单例类的一个最重要的特点是类的构造子是私有的，从而避免外界利用构造子直接创建出任意多的实例。值得指出的是，由于构造子是私有的，因此，此类不能被继承。

懒汉式单例类

与饿汉式单例类相同之处是，类的构造子是私有的。与饿汉式单例类不同的是，懒汉式单例类在第一次被引用时将自己实例化。如果加载器是静态的，那么在懒汉式单例类被加载时不会将自己实例化。如下图所示，类图中给出了一个典型的饿汉式单例类实现。

代码清单2：懒汉式单例类

{% codeblock lang:java %}
package com.javapatterns.singleton.demos;
public class LazySingleton
{
private static LazySingleton
m_instance = null;
/**
* 私有的默认构造子，保证外界无法直接实例化
*/
private LazySingleton() { }
/**
* 静态工厂方法，返还此类的惟一实例
*/
synchronized public static LazySingleton
getInstance()
{
if (m_instance == null)
{
m_instance = new LazySingleton();
}
return m_instance;
}
}
{% endcodeblock %}

读者可能会注意到，在上面给出懒汉式单例类实现里对静态工厂方法使用了同步化，以处理多线程环境。有些设计师在这里建议使用所谓的"双重检查成例"。必须指出的是，"双重检查成例"不可以在Java 语言中使用。不十分熟悉的读者，可以看看后面给出的小节。

同样，由于构造子是私有的，因此，此类不能被继承。饿汉式单例类在自己被加载时就将自己实例化。即便加载器是静态的，在饿汉式单例类被加载时仍会将自己实例化。单从资源利用效率角度来讲，这个比懒汉式单例类稍差些。

从速度和反应时间角度来讲，则比懒汉式单例类稍好些。然而，懒汉式单例类在实例化时， 必须处理好在多个线程同时首次引用此类时的访问限制问题，特别是当单例类作为资源控制器，在实例化时必然涉及资源初始化，而资源初始化很有可能耗费时间。这意味着出现多线程同时首次引用此类的机率变得较大。

饿汉式单例类可以在Java 语言内实现， 但不易在C++ 内实现，因为静态初始化在C++ 里没有固定的顺序，因而静态的m_instance 变量的初始化与类的加载顺序没有保证，可能会出问题。这就是为什么GoF 在提出单例类的概念时，举的例子是懒汉式的。他们的书影响之大，以致Java 语言中单例类的例子也大多是懒汉式的。实际上，本书认为饿汉式单例类更符合Java 语言本身的特点。

登记式单例类

登记式单例类是GoF 为了克服饿汉式单例类及懒汉式单例类均不可继承的缺点而设计的。本书把他们的例子翻译为Java 语言，并将它自己实例化的方式从懒汉式改为饿汉式。只是它的子类实例化的方式只能是懒汉式的， 这是无法改变的。如下图所示是登记式单例类的一个例子，图中的关系线表明，此类已将自己实例化。


代码清单3：登记式单例类

{% codeblock lang:java %}
import java.util.HashMap;
public class RegSingleton
{
static private HashMap m_registry = new HashMap();
static
{
RegSingleton x = new RegSingleton();
m_registry.put( x.getClass().getName() ， x);
}
/**
* 保护的默认构造子
*/
protected RegSingleton() {}
/**
* 静态工厂方法，返还此类惟一的实例
*/
static public RegSingleton getInstance(String name)
{
if (name == null)
{
name = "com.javapatterns.singleton.demos.RegSingleton";
}
if (m_registry.get(name) == null)
{
try
{
m_registry.put( name，
Class.forName(name).newInstance() ) ;
}
catch(Exception e)
{
System.out.println("Error happened.");
}
}
return (RegSingleton) (m_registry.get(name) );
}
/**
* 一个示意性的商业方法
*/
public String about()
{
return "Hello， I am RegSingleton.";
}
}
{% endcodeblock %}

它的子类RegSingletonChild 需要父类的帮助才能实例化。下图所示是登记式单例类子类的一个例子。图中的关系表明，此类是由父类将子类实例化的。


下面是子类的源代码。

代码清单4：登记式单例类的子类


{% codeblock lang:java %}
import java.util.HashMap;
public class RegSingletonChild extends RegSingleton
{
public RegSingletonChild() {}
/**
* 静态工厂方法
*/
static public RegSingletonChild getInstance()
{
return (RegSingletonChild)
RegSingleton.getInstance(
"com.javapatterns.singleton.demos.RegSingletonChild" );
}
/**
* 一个示意性的商业方法
*/
public String about()
{
return "Hello， I am RegSingletonChild.";
}
}  
{% endcodeblock %}

在GoF 原始的例子中，并没有getInstance() 方法，这样得到子类必须调用的getInstance(String name)方法并传入子类的名字，因此很不方便。本章在登记式单例类子类的例子里，加入了getInstance() 方法，这样做的好处是RegSingletonChild 可以通过这个方法，返还自已的实例。而这样做的缺点是，由于数据类型不同，无法在RegSingleton 提供这样一个方法。由于子类必须允许父类以构造子调用产生实例，因此，它的构造子必须是公开的。这样一来，就等于允许了以这样方式产生实例而不在父类的登记中。这是登记式单例类的一个缺点。

GoF 曾指出，由于父类的实例必须存在才可能有子类的实例，这在有些情况下是一个浪费。这是登记式单例类的另一个缺点。



GoF 曾指出，由于父类的实例必须存在才可能有子类的实例，这在有些情况下是一个浪费。这是登记式单例类的另一个缺点。

在什么情况下使用单例模式


使用单例模式的条件


使用单例模式有一个很重要的必要条件：


在一个系统要求一个类只有一个实例时才应当使用单例模式。反过来说，如果一个类可以有几个实例共存，那么就没有必要使用单例类。但是有经验的读者可能会看到很多不当地使用单例模式的例子，可见做到上面这一点并不容易，下面就是一些这样的情况。


例子一


问：我的一个系统需要一些"全程"变量。学习了单例模式后，我发现可以使用一个单例类盛放所有的"全程"变量。请问这样做对吗？


答：这样做是违背单例模式的用意的。单例模式只应当在有真正的"单一实例"的需求时才可使用。


一个设计得当的系统不应当有所谓的"全程"变量，这些变量应当放到它们所描述的实体所对应的类中去。将这些变量从它们所描述的实体类中抽出来， 放到一个不相干的单例类中去，会使得这些变量产生错误的依赖关系和耦合关系。


例子二


问：我的一个系统需要管理与数据库的连接。学习了单例模式后，我发现可以使用一个单例类包装一个Connection 对象，并在finalize（）方法中关闭这个Connection 对象。这样的话，在这个单例类的实例没有被人引用时，这个finalize（）对象就会被调用，因此，Connection 对象就会被释放。这多妙啊。


答：这样做是不恰当的。除非有单一实例的需求，不然不要使用单例模式。在这里Connection 对象可以同时有几个实例共存，不需要是单一实例。


单例模式有很多的错误使用案例都与此例子相似，它们都是试图使用单例模式管理共享资源的生命周期，这是不恰当的。


单例类的状态


有状态的单例类


一个单例类可以是有状态的（stateful），一个有状态的单例对象一般也是可变（mutable） 单例对象。


有状态的可变的单例对象常常当做状态库（repositary）使用。比如一个单例对象可以持有一个int 类型的属性，用来给一个系统提供一个数值惟一的序列号码，作为某个贩卖系统的账单号码。当然，一个单例类可以持有一个聚集，从而允许存储多个状态。


没有状态的单例类


另一方面，单例类也可以是没有状态的（stateless），仅用做提供工具性函数的对象。既然是为了提供工具性函数，也就没有必要创建多个实例，因此使用单例模式很合适。一个没有状态的单例类也就是不变（Immutable） 单例类； 关于不变模式，读者可以参见本书的"不变（Immutable ）模式"一章。


多个JVM 系统的分散式系统


EJB 容器有能力将一个EJB 的实例跨过几个JVM 调用。由于单例对象不是EJB，因此，单例类局限于某一个JVM 中。换言之，如果EJB 在跨过JVM 后仍然需要引用同一个单例类的话，这个单例类就会在数个JVM 中被实例化，造成多个单例对象的实例出现。一个J2EE应用系统可能分布在数个JVM 中，这时候不一定需要EJB 就能造成多个单例类的实例出现在不同JVM 中的情况。


如果这个单例类是没有状态的，那么就没有问题。因为没有状态的对象是没有区别的。但是如果这个单例类是有状态的，那么问题就来了。举例来说，如果一个单例对象可以持有一个int 类型的属性，用来给一个系统提供一个数值惟一的序列号码，作为某个贩卖系统的账单号码的话，用户会看到同一个号码出现好几次。


在任何使用了EJB、RMI 和JINI 技术的分散式系统中，应当避免使用有状态的单例模式。


多个类加载器


同一个JVM 中会有多个类加载器，当两个类加载器同时加载同一个类时，会出现两个实例。在很多J2EE 服务器允许同一个服务器内有几个Servlet 引擎时，每一个引擎都有独立的类加载器，经有不同的类加载器加载的对象之间是绝缘的。


比如一个J2EE 系统所在的J2EE 服务器中有两个Servlet 引擎：一个作为内网给公司的网站管理人员使用；另一个给公司的外部客户使用。两者共享同一个数据库，两个系统都需要调用同一个单例类。如果这个单例类是有状态的单例类的话，那么内网和外网用户看到的单例对象的状态就会不同。除非系统有协调机制，不然在这种情况下应当尽量避免使用有状态的单例类。