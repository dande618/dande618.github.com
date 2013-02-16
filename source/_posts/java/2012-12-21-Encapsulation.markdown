---
layout: post
title: "Java中的封装"
date: 2012-12-21 16:10
comments: true
categories: java
---
　　对象只公开了用户与之交互所需的接口，而与对象使用无关的细节能对其他对象隐藏。
<!-- more -->


　　封装（Encapsulation）是面向对象的三大特征之一（还有继承和多态），它指的是将对象的状态信息隐藏在对象内部，不允许外部程序直接访问对象内部信息，而是通过该类所提供的方法来实现对内部信息的操作和访问。封装是面向对象编程语言对客观世界的模拟。

----

__对一个类或对象实现良好的封装，可以实现以下目的：__

- 隐藏类的细节。

- 让使用者只能通过事先预定的方法来访问数据，从而可以在该方法里加入控制逻辑。

- 可进行数据检查，从而有利于保证对象信息的完整性。

- 便于修改，提高代码的可维护性。

　　封装实际上有两个方面的含义：把该隐藏的隐藏起来，把该暴露的暴露出来。这两个方面需要通过使用Java提供的访问控制符来实现。

----

　　Java提供了三个访问修饰符：private、protected和public。分别代表了三个访问控制级别，另外还有一个不加任何访问控制符的访问控制级别，提供了四个访问控制级别。

__由小到大是：__

　　private-->default-->protected-->public

- private（当前类访问权限）：如果一个类里的一个成员（包括Field、方法、构造器等）使用private来修饰，则这个成员只能在当前类的内部被访问。很显然，这个访问修饰符用于修饰Field最合适，使用它来修饰Field就可以把Field隐藏在该类的内部。

- default（包访问权限）：如果类里的一个成员（包括Field、方法、构造器等）或者一个外部类不使用任何访问控制符修饰，我们就称它为包访问权限。default访问控制的成员或外部类可以被相同包下的其他类访问。

- protected（子类访问权限）：如果一个成员（包括Field、方法、构造器等）使用protected控制符修饰，那么这个成员既可以被同一个包中的其他类访问，也可以被不同包中的子类访问。在通常情况下，如果使用protected来修饰一个方法，通常是希望其子类来重写这个方法。

- public（公共访问权限）：这是一个最宽松的访问控制级别，如果一个成员（包括Field、方法、构造器等）或者一个外部类用public访问控制符修饰，那么这个成员或外部类就可以被所有类访问，不管访问类和被访问类是否处于同一个包中，是否具有父子继承关系。


<table>
	<tr>
		<td></td>
		<td>　private　</td>
		<td>　default　</td>
		<td>　protected　</td>
		<td>　public　</td>
	</tr>
	<tr>
		<td>　同一个类中　</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
	</tr>
	<tr>
		<td>　同一个包中　</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
	</tr>
	<tr>
		<td>　子类中　</td>
		<td></td>
		<td></td>
		<td>√</td>
		<td>√</td>
	</tr>
	<tr>
		<td>　全局范围内　</td>
		<td></td>
		<td></td>
		<td></td>
		<td>√</td>
	</tr>
</table>

　　访问控制符用于控制一个类的成员是否可以被其他类访问，对于局部变量而言，其作用域就是它所在的方法，不可能被其他类访问，因此不能使用访问控制符来修饰。

　　对于外部类而言，它也可以使用访问控制符修饰，但外部类只能有两种访问控制级别：public和默认，外部类不能使用private和protected修饰，因为外部类没有处于任何类的内部，也就没有其所在类的内部、所在类的子类两个范围，因此private和protected访问控制修饰符对外部类没有意义。

　　如果一个Java源文件里定义的所有类都没有使用public修饰，则这个Java源文件的文件名可以是一切合法的文件名；但如果一个Java源文件里定义了一个public修饰的类，则这个源文件的文件名必须与public修饰的类的类名相同。
{% codeblock lang:java %}
public class Person
{
　　　　private String name;
　　　　private int age;
　　　　public void setName(String name)
　　　　{
　　　　　　　　if (name.length() > 6 || name.length() < 2)
　　　　　　　　{
　　　　　　　　　　　　System.out.println("您输入的名字不符合要求");
　　　　　　　　　　　　return;
　　　　　　　　}
　　　　　　　　else
　　　　　　　　{
　　　　　　　　　　　　this.name = name;
　　　　　　　　}
　　　　}
　　　　public String getName()
　　　　{
　　　　　　　　return this.name;
　　　　}
　　　　public void setAge(int age)
　　　　{
　　　　　　　　if (age > 105 || age < 0)
　　　　　　　　{
　　　　　　　　　　　　System.out.println(“您设置的年龄不合法”);
　　　　　　　　　　　　return;
　　　　　　　　}
　　　　　　　　else
　　　　　　　　{
　　　　　　　　　　　　this.age = age;
　　　　　　　　}
　　　　}
　　　　public int getAge()
　　　　{
　　　　　　　　return this.age;
　　　　}
}
{% endcodeblock %}
　　定义了上面的person类之后，该类的name和age两个Field只有在Person类内才可以操作和访问，在Person类之外只能通过各自对应的setter和getter方法来操作和访问它们。

__关于访问控制符的使用，存在如下几条基本原则：__

- 类的绝大部分Field都应该使用private修饰，只有一些static修饰的、类似全局变量的Field才可能考虑使用public修饰。除此之外，有些方法只是用于辅助实现该类的其他方法，这些方法被称为工具方法，工具方法也应该使用private修饰。

- 如果某个类主要做其他类的父类，该类里包含的大部分方法可能仅希望被其子类重写，而不想被外界直接调用，则应该使用protected修饰这些方法。

- 希望暴露出来给其他类自由调用的方法应该使用public修饰。因此，类的构造器通过使用public修饰，从而允许在其他地方创建类的实例。因为外部类通常希望被其他类自由使用，所以大部分外部类都使用public修饰。

