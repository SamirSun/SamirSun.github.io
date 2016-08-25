---
layout: post
title: 策略模式
description: 策略模式的一个小样例，以及结合枚举类型的使用小技巧
keywords: strategy、策略模式
categories: 设计模式
---

<h2>概念</h2>
<blockquote>
Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.
</blockquote>
策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

1、抽象出一个策略角色，一般为一个接口或者一个抽象类。<br/>
2、定义一系列的具体策略实现，实现前面定义的接口或者是继承抽象类。<br/>
3、定义一个上下文（context）类，内部维护一个策略类的实例。主要负责决定具体使用哪一种策略，或者用户指定具体的策略<br/>
<h2>例子</h2>
<hr/>现在根据上面的描述，一起看一个经典的例子吧。根据不同的会员等级，获取不同的折扣价格：<br/>
一、定义一个策略角色接口：<br/>
<pre>
<code>public interface DiscountBase {
	// 策略方法
	public double getPrice(double price);
}</code></pre>
二、定义一系列具体的实现类<br/>
这里定义一个图通会员8折，金牌会员9折，后续如果需要添加砖石会员啥的，同样的增加新的实现类就好了。这里很好的体现了OCP原则。<br/>
<pre>
<code>public class DiscountGold implements DiscountBase {
	public double getPrice(double price) {
		System.out.println("进入金牌会员处理流程 ... ");
		return price*0.9;
	}
}
public class DiscountNormal implements DiscountBase {
	public double getPrice(double price) {
		System.out.println("进入普通会员处理流程 ... ");
		return price*0.8;
	}
}</code>
</pre>
三、定义一个上下文（context）类<br/>
充当决策者的角色，当没有指定折扣策略时，默认是普通会员，也可以由客户端指定折扣策略<br/>
<pre>
<code>public class DiscountContext {
	private DiscountBase base;
	public DiscountContext() {
	}
	public DiscountContext(DiscountBase base) {
		this.base = base;
	}
	public double getDiscountPrice(double price) {
		if (base == null) {
			base = new DiscountNormal();
		}
		return this.base.getPrice(price);
	}
	public void changeDiscount(DiscountBase base) {
		this.base = base;
	}
}</code>
</pre>
四、最后写一个客户端看下吧<br/>
<pre>
<code>public class Client {
	public static void main(String[] args) {
		double price = 100;
		//未指定折扣策略，默认使用普通会员折扣策略
		DiscountContext context = new DiscountContext();
		double discountPrice = context.getDiscountPrice(price);
		System.out.println("折扣价格为："+discountPrice);
		//修改为指定折扣策略
		context.changeDiscount(new DiscountGold());
		discountPrice = context.getDiscountPrice(price);
		System.out.println("折扣价格为："+discountPrice);
		//直接初始化时指定折扣策略
		DiscountContext context2 = new DiscountContext(new DiscountGold());
		double discountPrice2 = context2.getDiscountPrice(price);
		System.out.println("折扣价格为："+discountPrice2);
	}
}</code>
</pre>
输出结果：
<pre><code>进入普通会员处理流程 ... 
折扣价格为：80.0
进入金牌会员处理流程 ... 
折扣价格为：90.0
进入金牌会员处理流程 ... 
折扣价格为：90.0</code>
</pre>

这时如果我们需要新增一个砖石会员使用新的折扣，只需新建一个类实现DiscountBase接口实现getDiscount方法即可。客户端需要使用新的策略时，调用changeDiscount方法切换为新的折扣策略。<br/>

缺点就是客户端和策略类是耦合的，在使用时必须知道所有的折扣策略，决定使用哪一个策略类。<br/>

<h2>结合枚举的使用</h2>
最近在重构的时候，有些固定的常量之类的东西之间还是存在一些关系的，忽然就想到是否可以把这些关系直接放到枚举中呢。这块之前也没看到过，歪歪原创吧呵呵，可能是书读的少，千万别砸我啊。。。<br/>
继续前面的例子，我们在使用的时候如何根据会员的等级来决定使用何种策略呢？除了ifelse，来看看如何使用枚举吧

<pre><code>/*新建一个会员枚举类，并和具体策略类之间建立对应关系**/
public enum MemberEnum {
	MEB_NORMAL("普通会员",new DiscountNormal()),
	MEB_GOLD("金牌会员",new DiscountGold()),
	;
	DiscountBase discount;
	String desc;
	private MemberEnum(String desc,DiscountBase discount){
		this.discount = discount;
		this.desc = desc;
	}
}</code></pre>
同样写一个客户端看下一下：
<pre>
<code>public class ClientEnum {
	public static void main(String[] args) {
		double price = 100;
		MemberEnum meb = MemberEnum.MEB_GOLD;
		double discountPrice = meb.discount.getPrice(price);
		System.out.println(meb.desc+"折扣价格为："+discountPrice);
		
		meb = MemberEnum.MEB_NORMAL;
		discountPrice = meb.discount.getPrice(price);
		System.out.println(meb.desc+"折扣价格为："+discountPrice);
	}
}</code></pre>
输出结果：
<pre>
进入金牌会员处理流程 ... 
金牌会员折扣价格为：90.0
进入普通会员处理流程 ... 
普通会员折扣价格为：80.0
</pre>
当然如果需要加会员等级或者其他的对应关系，都可以在枚举中直接加上，建一个私有构造器即可。是不是很好用！一点小技巧吧，欢迎交流<a href="www.javaman.cn">www.javaman.cn</a>
