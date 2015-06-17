---
layout: post
title: "单例设计模式的几种常见实现方法"
date: 2015-06-17 13:10:00
author: liuyp
categories: Design-Pattern
tags: java Design-Pattern
---

* content
{:toc}

## 最简单的设计模式

单例可以说是GOF23中最简单的一种设计模式，也经常用到。比如window的任务管理器，比如servlet对象等等。

单例要完成的就是确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个唯一的实例。就像它的定义:
Ensure a class has only one instance, and provide a global point of access to it.

最近学习整理了一下常见的单例模式实现方法（因为我见过的也不多，所有暂且认为我见过的就是常见的吧哈），大概有这么6种。




## 实现方法

实现单例就是要让对象在整个系统中是唯一的，不能被随意的实例化。还要考虑像线程安全问题，延迟加载问题等，就要看其具体的使用环境了。

懒汉式、饿汉式、双重检查锁、静态内部类、枚举、单例工厂和防范措施，代码放在[我的github的设计模式仓库](https://github.com/liuyuping459/DesignPattern/tree/master/src/com/singleton)中。

话不多说，上栗子。

### 懒汉式

	/**
	 * 懒汉式
	 * @author liu
	 * 类初始化时不初始化这个对象，延时加载，真要用时再加载
	 */
	public class SingletonDeom1 {
		private static SingletonDeom1 instance;
		
		private SingletonDeom1() {
		}
		
		//方法有同步，效率略低
		public synchronized SingletonDeom1 getInstance() {
			if (null == instance) {
				instance = new SingletonDeom1();
				return instance;
			}
			return instance;
		}
	}

### 饿汉式

	/**
	 * 饿汉式
	 * @author liu
	 * 类加载时立即初始化这个对象，类加载线程安全，没有延时加载
	 */
	public class SingletonDemo2 {
		private static SingletonDemo2 instance = new SingletonDemo2();
		
		private SingletonDemo2() {
		}
		
		//没有同步，效率略高
		public SingletonDemo2 getInstance() {
			return instance;
		}
	}

### 双重检查锁

	/**
	 * 双重检查锁
	 * @author liu
	 * 同步快在if内部，第一次同步，之后不用同步。
	 * JVM底层实现的问题，有时执行顺序会出问题。
	 * 比懒汉式略提高。
	 */
	public class SingletonDemo3 {
		private static SingletonDemo3 instance = null;
		
		public SingletonDemo3 getSingletonDemo3() {
			if (null == instance) {
				SingletonDemo3 demo = null;
				synchronized(SingletonDemo3.class) {
					demo = instance;
					if (null == demo) {
						synchronized(SingletonDemo3.class) {
							if (null == demo) {
								demo = new SingletonDemo3();
							}
						}
					}
					instance = demo;
				}
			}
			return instance;
		}
		
		private SingletonDemo3() {
			
		}
	}

### 枚举

	/**
	 * JVM保障枚举单例和线程安全，但无延时加载。
	 * @author liu
	 *
	 */
	public enum SingletonDemo5 {
		INSTANCE;
		
		public void function() {
			
		}
	}

是JVM直接罩着的，用起来比较方便。

### 静态内部类

	/**
	 * 静态内部类形式
	 * @author liu
	 * 不会立即加载，调用get才会加载内部类，加载类是线程安全的，延时加载又线程安全。
	 */
	public class SingletonDemo4 {
		private SingletonDemo4() {
			
		}
		
		private static class Instance {
			private static final SingletonDemo4 instance = new SingletonDemo4();
		}
		
		public SingletonDemo4 getSingletonDemo4() {
			return Instance.instance;
		}
	}

加强版懒汉式，是比较好用的一种。

### 单例工厂

	/**
	 * 单例类
	 * @author liu
	 * 要创建这个单例类的单例对象
	 */
	public class SingletonDemo7_1 {
		private SingletonDemo7_1() {
			
		}
		public void doSomething() {
			System.out.println("开始干活");
		}
	}

	/**
	 * 用工厂方法实现单例
	 * @author liu
	 * 当然要通过正常的渠道，其他类通过反射也是可以获得单例类的其他对象的。
	 */
	public class SingletonDemo7_2 {
		private static SingletonDemo7_1 demo7_1;
		
		static{
			try {
				Class clazz = Class.forName(SingletonDemo7_1.class.getName());
				Constructor<SingletonDemo7_1> constructor = clazz.getEnclosingConstructor();
				constructor.setAccessible(true);
				demo7_1 = constructor.newInstance();
			} catch(Exception e) {
				System.out.println("报错");
			}
		}
		
		public SingletonDemo7_1 getSingletonDemo7_1() {
			return demo7_1;
		}
	}

借助了工厂模式，单例工厂只产生同一个单例对象。

### 防范

如果是架构级的，还要考虑防范通过非正常渠道，比如反射、反序列化方法获取对象，以懒汉式为例。

	/**
	 * 防止反射和反序列化破坏
	 * @author liu
	 *
	 */
	public class SingletonDemo6 {
		private static SingletonDemo6 instance = null;
		
		//反射时设置setAccessible跳过权限，调用构造器，若instance对象不为空，则抛出异常。
		private SingletonDemo6() {
			if (instance != null) {
				throw new RuntimeException();
			}
		}
		
		//反序列化时，有readResolve方法，则直接返回此方法指定的对象，而不创建新的对象。
		private Object readResolve() {
			return instance;
		}
		
		public synchronized SingletonDemo6 getSingletonDemo6() {
			if (null == instance) {
				instance = new SingletonDemo6();
			}
			return instance;
		}
	}

## 总结

当全局只需要一个且只能有一个对象的时候就用单例喽。简单说就是通过构造方法的私有化，不让别人随意创建新的对象，而是用自己创建好的唯一的对象提供给对象的调用者。

至于对是否延迟加载、线程安全和特殊情况的防范，就要看使用的具体环境了。当然，如果需要的话也可以从单例模式扩展为多例。

---
最后

thanks for 《设计模式之禅》（秦小波 著）