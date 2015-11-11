---
layout: post
title: 'Java单例模式'
date: '2015-11-11'
header-img: "img/post-bg-web.jpg"
tags:
     - Java设计模式
author: 'lcy'
---
### 什么是单例模式？
&emsp;&emsp;单例模式能保证在一个JVM里某个类只有一个对象存在。在某些情况下，这种模式是很有用的。
<h3>单例模式的实现</h3>
<h4>一、非多线程环境下</h4>
&emsp;&emsp;1.私有化构造方法，防止类被实例化。
&emsp;&emsp;2.类中持有一个静态私有实例(私有是为了防止被引用，静态是为了方便创建实例方法的调用)，此变量一开始可以置为null或者直接初始化，若为前者，可以实现延迟加载。
&emsp;&emsp;3.创建一个工程方法创建实例(静态)
&emsp;&emsp;代码如下:

	public class Singleton {
		
		//私有静态实例 这里采用延迟加载方式 开始置为null
		private static Singleton instance = null;
		
		//私有化构造方法 防止类被实例化
		private Singleton() {
			
		}
		
		//创建实例的方法
		public static Singleton getInstance() {
			if(instance == null) instance = new Singleton();
			return instance;
		}
	
	}
	
<h4>二、多线程环境下的单例</h4>
&emsp;&emsp;在多线程环境下，假设一开始instance为null，这时候有两个线程调用上述的工程方法getInstance，这时候就有可能在堆内存中创建了两个单例对象，而instance变量指向的仅仅是第二个，第一个对象过一段时间会被GC回收，这无疑会增加内存的浪费。
&emsp;&emsp;解决方案1：使用synchronized关键字
&emsp;&emsp;首先考虑的是对getInstance方法加锁，代码如下：

	public static synchronized Singleton getInstance() {
		if(instance == null) instance = new Singleton();
		return instance;
	}

&emsp;&emsp;但是使用synchronized关键字对一个方法加锁等于是对调用这个方法的对象上锁，他在对象每次执行这个方法时都会对对象上锁，而实际上我们仅仅需要在第一个instance未完成实例化的时候上锁，所以代码改成下面无疑更加合适。

	public static Singleton getInstance() {
		if(instance == null) {
			synchronized(instance) {
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}

&emsp;&emsp;需要注意的是为什么synchronized同步代码块里面的代码还要判断一下instance是否为空，我们假设一开始instance为空，线程A和B同时调用getInstance方法，他们同时通过最外层的instance==null的判断，这时候只能有一个线程执行同步代码块里的代码，剩下的线程因为instance对象被锁，只能阻塞，若是A执行完成，这时候B会去执行同步代码块的内容，所以B必须判断一下instance是否为空，不为空才能去执行。

&emsp;&emsp;上述代码的问题：java指令中赋值和创建对象的操作是分开执行的，JVM并不能保证他们的执行顺序，所以有可能JVM创建完了单例对象，并且让instance变量指向他之后，再去完成对象的初始化。假设AB两个线程同时到达同步代码块，A进入执行，B阻塞，A进入判断instance==null，所以对执行对象的创建操作，因为JVM无法保证创建和赋值操作的顺序，所以可能单例对象创建完成，并且instance变量指向他，但是单例对象没有进行初始化，这时候A退出同步代码块，B结束阻塞进入同步代码块，发现isntance不为空，所以结束synchronized块并将结果返回调用他的程序，假设他的程序这时调用instance变量，因为instance没有完成初始化所以会出现错误。

&emsp;&emsp;解决方案2：使用内部类，因为JVM机制保证了类的加载过程是线程互斥的，这样我们获取单例对象的时候就能够保证instance实例只被创建了一次，并且instance变量指向的堆内存已经完成初始化。
&emsp;&emsp;代码如下:

	public class Singleton {
		
		//私有化构造方法 防止类被实例化
		private Singleton() {
			
		}
		
		/* 此处使用一个内部类来维护单例 */  
		private static class SingletonFactory {  
			private static Singleton instance = new Singleton();  
		}  

		//获取实例的方法
		public static Singleton getInstance() {
			return SingletonFactory.instance;  
		}
	
	}


&emsp;&emsp;上述方式相对来说已经很完美了，但是如果在构造方法执行过程中出了问题，那么实例永远得不到创建。


> 如有任何知识产权、版权问题或理论错误，还请指正。
>
> 转载请注明原作者及以上信息。
