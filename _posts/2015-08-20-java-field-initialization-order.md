---
author: huang
title:  java 中初始化成员变量的顺序
featimg: avatar.jpg
tags: [text]
category: [standard]
---

从jvm的角度出发, 当一个类被jvm加载后, jvm 会以如下顺序初始化:

* 先**默认初始化**所有只被声明，未被赋值的 instance variable，包含 static 和 non-static的。
* 对有赋值操作的成员变量，按顺序对 static 的成员变量进行**显示初始化**。
* 执行 static 代码块。
* **注意 static 部分只会被jvm执行一次！**
* 按顺序对 non-static 的成员变量进行显示初始化。
* 执行 non-static 代码块。
* 执行构造函数。

当一个类的实例被第二次创建的时候, jvm 会第二次加载这个类，进而创建出一个新的该类的实例， 在此过程中， 所有(注意是**所有**) static 的初始化过程不会被 jvm 再次执行， 而只是执行non-static 的初始化过程

### 对于有继承关系的类的初始化：
对于有继承管的类，因为其内部“包裹”着它的父类的所有成员变量，所以初始化的过程是先初始化父类中的成员变量，然后再初始化子类中的成员变量，顺序仍和上面介绍的一样。

| ------------- | --------------- | 
|   SuperClass	:|	static variable |
|   SuperClass	:|	static block    |
|   SubClass	:|	static variable |
|   SubClass	:|	static block    |
|   	main()  |
|   SuperClass	:|	non static variable |
|   SuperClass	:|	non static block    |
|   SuperClass	:|	constructor     |
|   SubClass	:|	non static variable |
|   SubClass	:|	non static block    |
|   SubClass	:|	constructor     |

[想看 code 点我去结尾](#jump)<span id="jumpBack"></span>

main()函数的执行看起来有些奇怪，虽然subClass的创建是在main()函数之内的，但它的第一行指令的执行却发生在superClass和subClass的static成员初始化之后。

<span id="jump"> [点我回去刚才位置](#jumpBack) </span>


	class SuperClass {
		int nsSuper = superInitNonStatic();
		static int sSuper = superInitStatic();
	
		private int superInitNonStatic() {
			System.out.println("SuperClass	:	non static variable");
			return 1;
		}
	
		private static int superInitStatic() {
			System.out.println("SuperClass	:	static variable");
			return 1;
		}
		
		{
			System.out.println("SuperClass	:	non static block");
		}
		
		static {
			System.out.println("SuperClass	:	static block");
		}
		
		SuperClass() {
			System.out.println("SuperClass	:	constructor");
		}
	}
	
	public class SubClass extends SuperClass{
		int nsSub = subInitNonStatic();
		static int sSub = subInitStatic();
	
		private int subInitNonStatic() {
			System.out.println("SubClass	:	non static variable");
			return 2;
		}
	
		private static int subInitStatic() {
			System.out.println("SubClass	:	static variable");
			return 2;
		}
		
		{
			System.out.println("SubClass	:	non static block");
		}
		
		static {
			System.out.println("SubClass	:	static block");
		}
		
		SubClass() {
			System.out.println("SubClass	:	constructor");
		}
		
		public static void main(String[] args) {
			System.out.println("	main()");
			SubClass sub = new SubClass();
	
		}
	}


