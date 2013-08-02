---
layout: post
title: "String,StringBuffer与StringBuilder"
date: 2013-06-04 15:21
comments: true
categories: java
---
<http://www.cnblogs.com/springcsc/archive/2009/12/03/1616330.html>

StringBuffer类和String一样，也用来代表字符串，只是由于StringBuffer的内部实现方式和String不同，所以StringBuffer在进行字符串处理时，不生成新的对象，在内存使用上要优于String类。 

所以在实际使用时，如果经常需要对一个字符串进行修改，例如插入、删除等操作，使用StringBuffer要更加适合一些。

在StringBuffer类中存在很多和String类一样的方法，这些方法在功能上和String类中的功能是完全一样的。

但是有一个最显著的区别在于，对于StringBuffer对象的每次修改都会改变对象自身，这点是和String类最大的区别。

另外由于StringBuffer是线程安全的，关于线程的概念后续有专门的章节进行介绍，所以在多线程程序中也可以很方便的进行使用，但是程序的执行效率相对来说就要稍微慢一些。

<!-- more -->

##1、StringBuffer对象的初始化

StringBuffer对象的初始化不像String类的初始化一样，Java提供的有特殊的语法，而通常情况下一般使用构造方法进行初始化。

例如：

{% codeblock lang:java %}
StringBuffer s = new StringBuffer();
{% endcodeblock %}

这样初始化出的StringBuffer对象是一个空的对象。

如果需要创建带有内容的StringBuffer对象，则可以使用：

{% codeblock lang:java %}
StringBuffer s = new StringBuffer("abc");
{% endcodeblock %}

这样初始化出的StringBuffer对象的内容就是字符串"abc"。

需要注意的是，StringBuffer和String属于不同的类型，也不能直接进行强制类型转换，下面的代码都是错误的：

{% codeblock lang:java %}
StringBuffer s = "abc";      //赋值类型不匹配
StringBuffer s = (StringBuffer)"abc";    //不存在继承关系，无法进行强转
{% endcodeblock %}

StringBuffer对象和String对象之间的互转的代码如下：

{% codeblock lang:java %}
String s = "abc";
StringBuffer sb1 = new StringBuffer("123");
StringBuffer sb2 = new StringBuffer(s);   //String转换为StringBuffer
String s1 = sb1.toString();     //StringBuffer转换为String
{% endcodeblock %}

##2、StringBuffer的常用方法

StringBuffer类中的方法主要偏重于对于字符串的变化，例如追加、插入和删除等，这个也是StringBuffer和String类的主要区别。

###a、append方法

{% codeblock lang:java %}
public StringBuffer append(boolean b)
{% endcodeblock %}

该方法的作用是追加内容到当前StringBuffer对象的末尾，类似于字符串的连接。调用该方法以后，StringBuffer对象的内容也发生改变，例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("abc");
sb.append(true);
{% endcodeblock %}

则对象sb的值将变成"abctrue"。

使用该方法进行字符串的连接，将比String更加节约内容，例如应用于数据库SQL语句的连接，例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer();
String user = "test";
String pwd = "123";
sb.append("select * from userInfo where username=").append(user).append(" and pwd=").append(pwd);
{% endcodeblock %}

这样对象sb的值就是字符串

{% codeblock %}
"select * from userInfo where username=test and pwd=123"。
{% endcodeblock %}

###b、deleteCharAt方法

{% codeblock lang:java %}
public StringBuffer deleteCharAt(int index){}
{% endcodeblock %}

该方法的作用是删除指定位置的字符，然后将剩余的内容形成新的字符串。例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("Test");
sb. deleteCharAt(1);
{% endcodeblock %}

该代码的作用删除字符串对象sb中索引值为1的字符，也就是删除第二个字符，剩余的内容组成一个新的字符串。所以对象sb的值变为"Tst"。

还存在一个功能类似的delete方法：

{% codeblock lang:java %}
public StringBuffer delete(int start,int end)
{% endcodeblock %}

该方法的作用是删除指定区间以内的所有字符，包含start，不包含end索引值的区间。例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("TestString");
sb. delete (1,4);
{% endcodeblock %}

该代码的作用是删除索引值1(包括)到索引值4(不包括)之间的所有字符，剩余的字符形成新的字符串。则对象sb的值是"TString"。

###c、insert方法

{% codeblock lang:java %}
public StringBuffer insert(int offset, boolean b)
{% endcodeblock %}

该方法的作用是在StringBuffer对象中插入内容，然后形成新的字符串。例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("TestString");
sb.insert(4,false);
{% endcodeblock %}

该示例代码的作用是在对象sb的索引值4的位置插入false值，形成新的字符串，则执行以后对象sb的值是"TestfalseString"。

###d、reverse方法

{% codeblock lang:java %}
public StringBuffer reverse()
{% endcodeblock %}

该方法的作用是将StringBuffer对象中的内容反转，然后形成新的字符串。例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("abc");
sb.reverse();
{% endcodeblock %}

经过反转以后，对象sb中的内容将变为"cba"。

###e、setCharAt方法

{% codeblock lang:java %}
public void setCharAt(int index, char ch)
{% endcodeblock %}

该方法的作用是修改对象中索引值为index位置的字符为新的字符ch。例如：

{% codeblock lang:java %}
StringBuffer sb = new StringBuffer("abc");
sb.setCharAt(1,'D');
{% endcodeblock %}

则对象sb的值将变成"aDc"。

###f、trimToSize方法

{% codeblock lang:java %}
public void trimToSize()
{% endcodeblock %}

该方法的作用是将StringBuffer对象的中存储空间缩小到和字符串长度一样的长度，减少空间的浪费。

总之，在实际使用时，String和StringBuffer各有优势和不足，可以根据具体的使用环境，选择对应的类型进行使用。

#String,StringBuffer与StringBuilder的区别

- __String 字符串常量__

- __StringBuffer 字符串变量（线程安全）__

- __StringBuilder 字符串变量（非线程安全）__

简要的说， String 类型和 StringBuffer 类型的主要性能区别其实在于 String 是不可变的对象, 因此在每次对 String 类型进行改变的时候其实都等同于生成了一个新的 String 对象，然后将指针指向新的 String 对象，所以经常改变内容的字符串最好不要用 String ，因为每次生成对象都会对系统性能产生影响，特别当内存中无引用对象多了以后， JVM 的 GC 就会开始工作，那速度是一定会相当慢的。

而如果是使用 StringBuffer 类则结果就不一样了，每次结果都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，再改变对象引用。所以在一般情况下我们推荐使用 StringBuffer ，特别是字符串对象经常改变的情况下。而在某些特别情况下， String 对象的字符串拼接其实是被 JVM 解释成了 StringBuffer 对象的拼接，所以这些时候 String 对象的速度并不会比 StringBuffer 对象慢，而特别是以下的字符串对象生成中， String 效率是远要比 StringBuffer 快的：

{% codeblock lang:java %}
String S1 = "This is only a" + " simple" + " test";
StringBuffer Sb = new StringBuilder("This is only a").append(" simple").append(" test");
{% endcodeblock %}

你会很惊讶的发现，生成 String S1 对象的速度简直太快了，而这个时候 StringBuffer 居然速度上根本一点都不占优势。其实这是 JVM 的一个把戏，在 JVM 眼里，这个

{% codeblock lang:java %}
String S1 = "This is only a" + " simple" + "test"; 
{% endcodeblock %}

其实就是：

{% codeblock lang:java %}
String S1 = "This is only a simple test"; 
{% endcodeblock %}

所以当然不需要太多的时间了。但大家这里要注意的是，如果你的字符串是来自另外的 String 对象的话，速度就没那么快了，譬如：


{% codeblock lang:java %}
String S2 = "This is only a";
String S3 = " simple";
String S4 = " test";
String S1 = S2 +S3 + S4;
{% endcodeblock %}

这时候 JVM 会规规矩矩的按照原来的方式去做

<span style="color:red">在大部分情况下 __StringBuffer > String__</span>

##StringBuffer

Java.lang.StringBuffer线程安全的可变字符序列。一个类似于 String 的字符串缓冲区，但不能修改。虽然在任意时间点上它都包含某种特定的字符序列，但通过某些方法调用可以改变该序列的长度和内容。

可将字符串缓冲区安全地用于多个线程。可以在必要时对这些方法进行同步，因此任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

StringBuffer 上的主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据。每个方法都能有效地将给定的数据转换成字符串，然后将该字符串的字符追加或插入到字符串缓冲区中。append 方法始终将这些字符添加到缓冲区的末端；而 insert 方法则在指定的点添加字符。

例如，如果 z 引用一个当前内容是"start"的字符串缓冲区对象，则此方法调用 z.append("le") 会使字符串缓冲区包含"startle"，而 z.insert(4, "le") 将更改字符串缓冲区，使之包含"starlet"。

<span style="color:red">在大部分情况下 __StringBuilder > StringBuffer__</span>

##java.lang.StringBuilder

java.lang.StringBuilder一个可变的字符序列是5.0新增的。此类提供一个与 StringBuffer 兼容的 API，但不保证同步。该类被设计用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。如果可能，建议优先采用该类，因为在大多数实现中，它比 StringBuffer 要快。两者的方法基本相同。