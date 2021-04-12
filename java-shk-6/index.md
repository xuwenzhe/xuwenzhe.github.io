# Java零基础教程（六.面向对象-重要补充）


## 1. static关键字

### 1.1 static关键字的使用

1. static: 静态
2. static可以用来修饰：属性，方法，代码块，内部类
3. 使用static修饰属性: 静态变量（是属性，不是局部变量）或者叫类变量
	* 属性，按是否使用static修饰，有分为静态属性 vs 非静态属性（实例变量）
		- 实例变量：我们创建类类的多个对象，每个对象都独立的拥有一套类中的非静态属性。当修改其中一个对象中的非静态属性时，不会导致其他对象中同样的属性值的修改。
		- 静态变量：我们创建类类的多个对象，多个对象共享同一个静态变量。当通过某一个对象修改静态变量时，会导致其他对象调用此静态变量时，是修改过了的。
	* static修饰属性的其他说明：
		- 静态变量随着类的加载而加载。可以通过“类.静态变量”的方式进行调用
		- 静态变量的加载要早于对象的创建。
		- 由于类只会加载一次，则静态变量在内存中也只会存在一份：存在方法区的静态域中。
		-	
			    调用  类变量      实例变量
				类     yes        no
				对象   yes        yes

	* 静态属性举例： `System.out`; `Math.PI`;
             
4. 使用static修饰方法: 静态方法
	
	* 随着类的加载而加载，可以通过“类.静态方法”的方式进行调用
	*
			调用  静态方法      非静态方法
			类     yes        no
			对象   yes        yes

	* 静态方法中，只能调用静态的方法或属性; 非静态方法中，既可以调用非静态的方法或属性，也可以调用静态的方法或属性
     
5. static注意点：
	
	* 在静态的方法内，不能使用this关键字，super关键字
	* 关于静态属性和静态方法的使用，大家都可以从生命周期的角度来理解

6. 开发中，如何确定一个属性是否要声明为static的？

	* 属性是可以被多个对象所共享的，不会随着对象的不同而不同的。
	* 类中的常量也常常声明为static，比如PI

   开发中，如何确定一个方法是否要声明为static的？

	* 操作静态属性的方法，通常设置为static的
	* 工具类中的方法，习惯上声明为static的。比如：Math，Arrays，Collections

```java
package com.atguigu.java1;

public class StaticTest {
	public static void main(String[] args) {
		Chinese c1 = new Chinese();
		c1.name = "姚明";
		c1.age = 40;
		
		Chinese c2 = new Chinese();
		c2.name = "马龙";
		c2.age = 30;
		
		c1.nation = "CHN";
		System.out.println(c2.nation);
		
		// 编译不通过
		// Chinese.name = "张继科";
		
		Chinese.show();
	}
}

class Chinese {
	String name;
	int age;
	static String nation;
	
	public void eat() {
		System.out.println("Chinese eat");
	}
	
	public static void show() {
		System.out.println("I am Chinese");
		// 不能调用非静态结构
//		eat();
//		name = "Tom";
		// 可以调用静态结构
		System.out.println(nation);
	}
}
```

再来看一个应用static的例子

```java
package com.atguigu.java1;

public class CircleTest {

	public static void main(String[] args) {
		Circle c1 = new Circle();
		Circle c2 = new Circle();
		System.out.println("c1 ID: " + c1.getId());
		System.out.println("c2 ID: " + c2.getId());
		System.out.println("total number of circles: " + Circle.getTotal());
	}
}

class Circle {
	private double radius;
	private int id;
	
	private static int total = 0; // 记录创建圆的个数
	private static int init = 1001; // static声明的属性被所有对象所共享
	
	public Circle() {
		id = init++;
		total++;
	}
	
	public Circle(double radius) {
		this();
		this.radius = radius;
	}
	
	public double getRadius() {
		return radius;
	}

	public static int getTotal() {
		return total;
	}

	public void setRadius(double radius) {
		this.radius = radius;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public double findArea() {
		return 3.14 * radius * radius;
	}
}
```

### 1.2 `static`应用 - 单例模式（Singleton）

单例设计模式

1. 所谓类的单例设计模式，就是采用一定的方法保证在整个的软件系统中，对某个类只能存在一个对象实例。
2. 如何实现？饿汉式 vs 懒汉式
3. 区分 饿汉式 和 懒汉式

	* 饿汉式：
		- 坏处：对象加载时间过长
		- 好处：饿汉式是线程安全的
	* 懒汉式：
		- 好处：延迟对象的创建。
		- 目前的写法坏处：线程不安全。 -> 到多线程内容时，再修改

```java
package com.atguigu.java2;

public class SingletonTest1 {
	
	public static void main(String[] args) {
		// 饿汉式
		Bank bank1 = Bank.getInstance();
		Bank bank2 = Bank.getInstance();
		System.out.println(bank1 == bank2);
		
		// 懒汉式
		Order order1 = Order.getInstance();
		Order order2 = Order.getInstance();
		System.out.println(order1 == order2);
	}
}

class Bank {
	// 1. 私有化类的构造器
	private Bank() {
		
	}
	
	// 2. 内部创建类的对象
	// 4. 要求此对象也必须声明为静态的
	private static Bank instance = new Bank();
	
	// 3. 提供公共的静态的方法，返回类的对象
	public static Bank getInstance() {
		return instance;
	}
}

class Order {
	// 1. 私有化构造器
	private Order() {
		
	}
	
	// 2. 声明当前类对象，没有初始化
	// 4. 此对象也必须声明为static的
	private static Order instance = null;
	
	// 3. 声明public，static的返回当前类对象的方法
	public static Order getInstance() {
		if (instance == null) {
			instance = new Order();
		}
		return instance;
	}
}
```

## 2. main方法的回顾

main()方法的使用说明：

1. main()方法作为程序的入口
2. main()方法也是一个普通的静态方法
3. main()方法可以作为我们与控制台交互的方式。（之前Scanner）

```java
package com.atguigu.java2;

public class MainTest {
	public static void main(String[] args) {
		Main.main(new String[100]);
		for (int i = 0; i < args.length; i++) {
			System.out.println("*****" + args[i]);// run configuration
			int num = Integer.parseInt(args[i]);
			System.out.println("####" + num);
		}
	}
}


class Main {
	public static void main(String[] args) {
		args = new String[100];
		for (int i = 0; i < args.length; i++) {
			args[i] = "args_" + i;
			System.out.println(args[i]);
		}
	}
}
```

## 3. 代码块

类的成员之四：代码块（或初始化块）

1. 代码块的作用：用来初始化类和对象
2. 代码块如果有修饰的话，只能使用static。
3. 分类：静态代码块 vs 非静态代码块
4. 静态代码块
	* 内部可以有输出语句
	* 随着类的加载而执行, 而且只执行一次
	* 作用：初始化类的属性
	* 如果一个类中定义了多个静态代码块，则按照声明的先后顺序执行
	* 静态代码块的执行要优先于非静态代码块的执行
	* 静态代码块内只能调用静态的属性，静态的方法，不能调用非静态的结构
5. 非静态代码块
	* 内部可以有输出语句
	* 随着对象的创建而执行
	* 每创建一个对象，就执行一次非静态代码块
	* 作用：可以在创建对象时，对对象的属性等进行初始化
	* 如果一个类中定义了多个非静态代码块，则按照声明的先后顺序执行
	* 非静态代码块内可以调用静态的属性，静态的方法，或非静态的属性，非静态的方法。
 
对属性可以赋值的位置：

1）默认初始化

2）显式初始化

3）构构造器中初始化

4）有了对象以后，可以通过“对象.属性”或“对象.方法”的方式赋值

5）在代码块中赋值

执行的顺序：1）- 2）/ 5）- 3）- 4）

```java
package com.atguigu.java3;

public class BlockTest {
	public static void main(String[] args) {
		
		String desc = Person.desc;
		Person p1 = new Person();
		Person p2 = new Person();
	}
}

class Person {
	String name;
	int age;
	static String desc;
	
	public Person() {
		
	}
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	// static的代码块
	static {
		System.out.println("static block");
		desc = "I love coding";
	}
	// 非static的代码块
	{
		System.out.println("block");
	}
	
	public void eat() {
		System.out.println("person eat");
	}

	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}

}

```

代码块是要早于构造器执行的

```java
package com.atguigu.java3;

public class LeafTest {
	public static void main(String[] args) {
		new Leaf();
//		output:
//		Root的静态代码块
//		Mid的静态代码块
//		Leaf的静态代码块
//		Root的普通代码块
//		Root的无参构造器
//		Mid的普通代码块
//		Mid的带参构造器
//		Leaf的普通代码块
//		Leaf的无参构造器
	}
}


class Root {
	static {
		System.out.println("Root的静态代码块");
	}
	{
		System.out.println("Root的普通代码块");
	}
	public Root() {
		System.out.println("Root的无参构造器");
	}
}

class Mid extends Root {
	static {
		System.out.println("Mid的静态代码块");
	}
	{
		System.out.println("Mid的普通代码块");
	}
	public Mid() {
		System.out.println("Mid的无参构造器");
	}
	public Mid(String msg) {
		System.out.println("Mid的带参构造器");
	}
}

class Leaf extends Mid {
	static {
		System.out.println("Leaf的静态代码块");
	}
	{
		System.out.println("Leaf的普通代码块");
	}
	public Leaf() {
		super("message");
		System.out.println("Leaf的无参构造器");
	}
}
```

再看一个例子

```java
package com.atguigu.java3;

class Father {
	static {
		System.out.println("1");
	}
	{
		System.out.println("2");
	}
	public Father() {
		System.out.println("3");
	}
}


public class Son extends Father {
	static {
		System.out.println("4");
	}
	{
		System.out.println("5");
	}
	public Son() {
		System.out.println("6");
	}
	public static void main(String[] args) {
		System.out.println("7");
		new Son();
		System.out.println("*****");
		new Son();
		
		// output: 1 - 4 - 7 - 2 - 3 - 5 - 6
		// output: ***** 
		// output: 2 - 3 - 5 - 6
	}
}
```

## 4. final关键字

final:

1. final可以用来修饰的结构： 类，方法，变量
2. final用来修饰一个类: 此类不能被其他类所继承，即不能有子类了，比如：String类，System类，StringBuffer类。
3. final用来修饰一个方法：表明此方法不可以被重写，比如：Object类中getClass()。
4. final用来修饰变量：此时的“变量”就称为是一个常量
	* final修饰属性：可以考虑赋值的位置有：显式初始化，代码块中初始化, 构造器中初始化，（默认，方法位置都不赋值）
	* final修饰局部变量：尤其是使用final修饰形参时，表明此形参是一个常量。当我们调用此方法时，给常量形参赋一个实参。一旦赋值以后，就只能在方法体内使用此形参，但不能进行重新赋值。
       
`static final`: static修饰属性，方法，代码块，内部类。final修饰属性，方法，类。其用来修饰属性表示全局常量。 

自己用final通常就用来修饰属性，别人用来修饰方法和类时，能看懂。

```java
package com.atguigu.java3;

public class FinalTest {
	
	final int width = 10;
	final int LEFT;
	final int RIGHT;
//	final int DOWN;
	
	{
		LEFT = 1;
	}
	public FinalTest() {
		RIGHT = 2;
	}
	public FinalTest(int n) {
		RIGHT = n;
	}
	
//	public void setDown(int down) {
//		this.DOWN = down;
//	}
	
	public void doWidth() {
//		width = 20;
	}
	
	public void show() {
		final int NUM = 10;// 常量
//		NUM += 20;
	}
	
	public void show(final int num) {
//		num = 20; // 编译错误
	}
	
	public static void main(String[] args) {
	}
}

final class FinalA {
	
}

//class B extends FinalA{
//	
//}

class AA {
	public final void show() {
		
	}
}

class BB extends AA {
//	public final void show() {
//		
//	}
}
```

看一道题

```java
package com.atguigu.java3;

public class FinalExerciseTest {
	public static void main(String[] args) {
		Other o = new Other();
		new FinalExerciseTest().addOne(o);
	}
	public void addOne(final Other o) {
//		o = new Other(); // 错误，o是final，不能指向新目标
		o.i++;// 正确，o指向对象的i是变量，可以变化
		System.out.println(o.i);// 1
	}
}


class Other {
	public int i;
}
```


## 5. 抽象类

### 5.1 abstract关键字的使用

1. abstract：抽象的
2. abstract可以用来修饰的结构：类，方法
3. abstract修饰类：抽象类
	* 此类不能实例化
	* 抽象类中一定有构造器，便于子类实例化时调用（涉及：子类对象实例化的全过程）
	* 开发中，都会提供抽象类的子类，让子类对象实例化，完成相应操作
4. abstract修饰方法：抽象方法
	* 抽象方法只有方法的声明，没有方法体
	* 包含抽象方法的类，一定是一个抽象类。反之，抽象类中可以没有抽象方法。
	* 若子类重写了父类中所有的抽象方法后，此子类方可实例化
	* 若子类没有重写父类中的所有的抽象方法，则此子类也是一个抽象类，需要使用abstract修饰

```java
package com.atguigu.java;

public class AbstractTest {
	public static void main(String[] args) {
		
		// 一旦Person类抽象了，就不可实例化
//		Person p1 = new Person();
//		p1.eat();
	}
}


abstract class Person {
	String name;
	int age;
	
	public Person() {
		
	}
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	// 不是抽象方法
//	public void eat() {
//	}
	// 抽象方法
	public abstract void eat();
	
	public void walk() {
		System.out.println("person walk");
	}
}

class Student extends Person {
	public Student(String name, int age) {
		super(name, age);
	}
	public void eat() {
		System.out.println("student eat");
	}
}
```

abstract使用上的注意点：

1. abstract不能用来修饰：属性，构造器等结构
2. abstract不能用来修饰私有方法（private方法子类不能看作重写），静态方法（子父类都为static不能看作重写），final的方法，final的类

```java
package com.atguigu.java;

public class AbstractTest1 {

}
```

### 5.2 匿名子类

```java
package com.atguigu.java;

/*
 * 抽象类的匿名子类
 */
public class PersonTest {
	public static void main(String[] args) {
		method(new B()); //  匿名对象
		
		// 创建了一个匿名子类的对象
		A a = new A() {

			@Override
			public void method1() {
				System.out.println("11111");
			}

			@Override
			public void method2() {
				System.out.println("22222");
			}};
			
		method(a);
		System.out.println("==============");
		// 匿名子类的匿名对象
		method(new A() {

			@Override
			public void method1() {
				System.out.println("333");
			}

			@Override
			public void method2() {
				System.out.println("444");
			}});
	}
	
	public static void method(A a) {
		a.method1();
		a.method2();
	}
}


abstract class A {
	public abstract void method1();
	public abstract void method2();
}

class B extends A {

	@Override
	public void method1() {
		System.out.println("b method1");
	}

	@Override
	public void method2() {
		System.out.println("b method2");
	}
	
}
```

### 5.3 abstract应用 - 模板方法设计模式

```java
package com.atguigu.java;
/*
 * 抽象类的应用：模板方法的设计模式
 */
public class TemplateTest {
	public static void main(String[] args) {
		Template t = new SubTemplate();
		t.spendTime();
	}
}

abstract class Template {
	public void spendTime() {
		long start = System.currentTimeMillis();
		code();
		long end = System.currentTimeMillis();
		System.out.println("elapsed time: "+ (end - start));
	}
	
	public abstract void code();
}

class SubTemplate extends Template {

	@Override
	public void code() {
		for (int i = 2; i <= 1000; i++) {
			boolean isFlag = true;
			for (int j = 2; j <= Math.sqrt(i); j++) {
				if (i % j == 0) {
					isFlag = false;
					break;
				}
			}
			if (isFlag) {
				System.out.println(i);
			}
		}
	}
	
}
```

再来看一个模板方法设计模式的例子

```java
package com.atguigu.java;
// 抽象类的应用：模板方法的设计模式
public class TemplateMethodTest {
	public static void main(String[] args) {
		BankTemplateMethod m1 = new DrawMoney();
		BankTemplateMethod m2 = new DrawMoney();
		
		m1.process();
		m2.process();
	}
}

abstract class BankTemplateMethod {
	public void takeNumber() {
		System.out.println("take number");
	}
	public abstract void transact(); // 办理具体业务，钩子方法
	
	public void evaluate() {
		System.out.println("give us a feedback! thanks");
	}
	 public final void process() {
		 takeNumber();
		 transact(); // 像个钩子，具体执行时，挂哪个子类，就执行哪个子类的实现代码
		 evaluate();
	 }
}

class DrawMoney extends BankTemplateMethod {

	@Override
	public void transact() {
		System.out.println("I want to draw my money out of my account");
	}
}

class ManageMoney extends BankTemplateMethod {

	@Override
	public void transact() {
		System.out.println("I want a private client to help me manage my money");
	}
}
```

## 6. 接口

一方面，有时必须从几个类中派生出一个子类，继承它们所有的属性和方法。但是，Java不支持多重继承。有了借口，就可以得到多重继承的效果。

另一方面，有时必须从几个类中抽取出一些共同的行为特征，而它们之间又没有“is-a”的关系，仅仅是具有相同的行为特征而已。例如：鼠标，键盘，打印机，扫描仪，摄像头，充电器，手机，移动硬盘等都支持USB连接。

接口就是规范，定义的是一组规则，体现了现实世界中“如果你是/要...则必须能”的思想。继承是一个“是不是”的关系，而接口实现则是“能不能”的关系。

接口的本质是契约，标准，规范，就像我们的法律一样。制订好后大家都要遵守。

### 6.1 接口的使用

1. 接口使用interface来定义
2. Java中，接口和类是并列的两个结构
3. 如何定义接口：定义接口中的成员
	* JDK7及以前：只能定义全局常量和抽象方法
		- 全局常量：`public static final`的...但是书写时，可以省略不写
		- 抽象方法：`public abstract`的
	* JDK8: 除了定义全局常量和抽象方法之外，还可以定义静态方法，默认方法（略）   
4. 接口中不能构造构造器的！意味着接口不可以实例化
5. Java开发中，接口通过让类去实现（implements）的方式来使用.
	* 如果实现类覆盖了接口中的所有抽象方法，则此实现类就可以实例化
	* 如果实现类没有覆盖接口中所有的抽象方法，则此实现类仍为一个抽象类。
6. Java类可以实现多个接口 -> 弥补了Java单继承性的局限性
   
   格式： `class AA extends BB implements CC, DD, EE`  
7. 接口与接口之间可以继承，而且可以多继承
8. 接口的具体使用，体现多态性
9. 接口，实际上可以看作是一种规范。

面试题：抽象类与接口有哪些异同？

```java
package com.atguigu.java1;

public class InterfaceTest {
	public static void main(String[] args) {
		Plane p = new Plane();
		p.fly();
		p.stop();
	}
}

interface Attackable {
	void attack();
}

interface Flyable {
	// 全局常量
	public static final int MAX_SPEED = 7900;// 第一宇宙速度
	int MIN_SPEED = 1; // 省略了public static final
	
	// 抽象方法
	public abstract void fly();
	
	void stop(); // 省略了public abstract
}

class Plane implements Flyable {

	@Override
	public void fly() {
		System.out.println("plane fly");
	}

	@Override
	public void stop() {
		System.out.println("plane stop");
	}
}

abstract class Kite implements Flyable {

	@Override
	public void fly() {
		System.out.println("kite fly");
	}
}

class Bullet implements Flyable, Attackable, CC {

	@Override
	public void attack() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void fly() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void stop() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void method1() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void method2() {
		// TODO Auto-generated method stub
		
	}
	
}
// **********************
interface AA {
	void method1();
}
interface BB {
	void method2();
}

interface CC extends AA, BB {
	
}
```

USB例子

接口的使用

1. 接口使用上也满足多态性
2. 接口，实际上就是定义了一种规范
3. 开发中， 体会面向接口编程！

```java
package com.atguigu.java1;

public class USBTest {
	public static void main(String[] args) {
		Computer c = new Computer();
		// 1. 创建了接口的非匿名实现类的非匿名对象
		Printer p = new Printer();
		c.transferData(p);
		
		// 2. 创建了接口的非匿名实现类的匿名对象
		c.transferData(new Printer());
		
		// 3. 创建了接口的匿名实现类的非匿名对象
		USB phone = new USB() {

			@Override
			public void start() {
				System.out.println("phone start");
			}

			@Override
			public void stop() {
				System.out.println("phone stop");
			}
			
		};
		c.transferData(phone);
		
		// 4. 创建了接口匿名实现类的匿名对象
		c.transferData(new USB() {

			@Override
			public void start() {
				System.out.println("mp3 start");
			}

			@Override
			public void stop() {
				System.out.println("mp3 stop");
			}
			
		});
	}
}

class Computer {
	public void transferData(USB usb) {
		usb.start();
		System.out.println("transfering data...");
		usb.stop();
	}
}

interface USB {
	// 定义了长，宽，最大最小的传输速度等
	
	void start();
	
	void stop();
}

class Flash implements USB {

	@Override
	public void start() {
		System.out.println("flash start");
	}

	@Override
	public void stop() {
		System.out.println("flash stop");
	}
}

class Printer implements USB {

	@Override
	public void start() {
		System.out.println("printer start");
	}

	@Override
	public void stop() {
		System.out.println("printer stop");
	}
}
```

### 6.2 interface应用 - 代理模式

```java
package com.atguigu.java1;
/*
 * 接口的应用：代理模式
 */
public class NetworkTest {
	public static void main(String[] args) {
		Server s = new Server();
		ProxyServer proxyServer = new ProxyServer(s);
		proxyServer.browse();
	}
}
interface Network {
	public void browse();
}

// 被代理类
class Server implements Network {

	@Override
	public void browse() {
		System.out.println("真实的服务器访问网络");
	}
	
}

// 代理类
class ProxyServer implements Network {
	
	private Network work;
	
	public ProxyServer(Network work) {
		this.work = work;
	}
	
	public void check() {
		System.out.println("联网之前的检查工作");
	}

	@Override
	public void browse() {
		check();
		work.browse();
	}
	
}
```

再来看一个代理模式的例子：明星与经纪人

```java
package com.atguigu.java1;
/*
 * 代理模式另外一个例子
 */
public class StaticProxyTest {
	public static void main(String[] args) {
		Proxy s = new Proxy(new RealStar());
		s.confer();
		s.signContract();
		s.bootTicket();
		s.sing();
		s.collectMoeny();
		
		// output
//		经纪人面谈
//		经纪人签合同
//		经纪人订票
//		sing... sing... sing...
//		经纪人收钱
	}
}

interface Star {
	void confer(); // 面谈
	void signContract(); // 签合同
	void bootTicket(); // 订票
	void sing(); // 唱歌
	void collectMoeny();  // 收钱
}

// 被代理类
class RealStar implements Star {

	@Override
	public void confer() {
	}

	@Override
	public void signContract() {	
	}

	@Override
	public void bootTicket() {	
	}

	@Override
	public void sing() {
		System.out.println("sing... sing... sing...");
	}

	@Override
	public void collectMoeny() {	
	}
}

// 代理类，经纪人
class Proxy implements Star {
	private Star real;
	public Proxy(Star real) {
		this.real = real;
	}

	@Override
	public void confer() {
		System.out.println("经纪人面谈");
	}
	@Override
	public void signContract() {
		System.out.println("经纪人签合同");
	}
	@Override
	public void bootTicket() {
		System.out.println("经纪人订票");
	}
	@Override
	public void sing() {
		real.sing();
	}
	@Override
	public void collectMoeny() {
		System.out.println("经纪人收钱");
	}}
```

一道考题

```java
package com.atguigu.java1;

public class C extends B implements A {
	public static void main(String[] args) {
		new C().pX();
	}
	public void pX() {
		System.out.println(super.x); // 1
		System.out.println(A.x);// 0
	}
}

interface A {
	int x = 0;
}

class B {
	int x = 1;
}
```


### 6.3 Java8中interface新特性

```java
package com.atguigu.java8;

public class SubClassTest {
	public static void main(String[] args) {
		SubClass s = new SubClass();
		// 知识点1: 接口中定义的静态方法，只能通过接口来调用
		// s.method1();// 编译报错
		CompareA.method1();
		// 知识点2: 通过实现类的对象，可以调用接口中的默认方法。
		// 如果实现类重写了接口中的默认方法，调用时，仍然调用的是重写以后的方法
		s.method2();
		// 知识点3： 如果子类（或实现类）继承的父类和实现的接口中声明类同名同参数的方法
		// 那么子类在没有重写此方法的情况下，默认调用的是父类中的同名同参数的方法。-> 类优先原则
		// 知识点4: 如果实现类实现了多个接口，而这多个接口中定义了同名同参数的默认方法，
		// 那么在实现类没有重写此方法的情况下，报错。 -> 接口冲突
		// 这就需要我们必须在实现类中重写此方法。
		s.method3();
	}
}

class SubClass extends SuperClass implements CompareA, CompareB {
	public void method2() {
		System.out.println("SubClass - method2");
	}
	
	// 知识点5:如何在子类（或实现类)的方法中调用父类，接口中被重写的方法
	public void myMethod() {
		method3();// 调用自己定义的重写的方法
		super.method3();// 调用的是父类中声明的
		// 调用接口中默认的方法
		CompareA.super.method3();
		CompareB.super.method3();	
	}
}

class SuperClass {
	public void method3() {
		System.out.println("SuperClass - method3");
	}
}


/*
 * JDK8: 除了定义全局常量和抽象方法之外，还可以定义静态方法，默认方法
 */
interface CompareA {
	// 静态方法
	public static void method1() {
		System.out.println("CompareA - method1");
	}
	// 默认方法
	public default void method2() {
		System.out.println("CompareA - method2");
	}
	// public可以省略
	default void method3() {
		System.out.println("CompareA - method3");
	}
}

interface CompareB {
	default void method3() {
		System.out.println("CompareB - method3");
	}
}
```

### 6.4 接口冲突的解决方式

```java
package com.atguigu.java8;

public class Man extends Father implements Filial, Spoony {
	public void help() {
		System.out.println("接口冲突，该救谁呢");
		Filial.super.help();
		Spoony.super.help();
		super.help();
	}
	
	public static void main(String[] args) {
		Man m = new Man();
		m.help();
	}
}


class Father {
	public void help() {
		System.out.println("儿子，救我媳妇！");
	}
}

interface Filial {
	default void help() {// 孝顺的
		System.out.println("救妈妈");
	}
}

interface Spoony {// 痴情的
	default void help() {
		System.out.println("救媳妇");
	}
}
```

### 6.5 抽象类和接口有哪些共同点和区别？

* 相同点：不能实例化，都可以被继承
* 不同点：
	- 抽象类：有构造器。单继承。 
	- 接口：不能声明构造器。多继承。

## 7. 内部类

类的内部成员之五：内部类（例子：人与大脑）

1. Java中允许将一个类A声明在另一个类B中，则类A就是内部类，类B称为外部类。
2. 内部类的分类：成员内部类（静态，非静态） vs 局部内部类（方法内，代码块内，构造器内）
3. 成员内部类：
	* 一方面，作为外部类的成员：
		- 调用外部类的结构
		- 可以被static修饰
		- 可以被4种不同的权限修饰 
	* 另一方面，作为一个类出现：
		- 类内可以定义属性，方法，构造器等
		- 可以被final修饰，表示此类不能被继承。言外之意，不使用final，就可以被继承
		- 可以被abstract修饰  
4. 关注如下的3个问题
	* 如何实例化成员内部类的对象
	* 如何在成员内部类中区分调用外部类的结构
	* 开发中，局部内部类等使用

```java
package com.atguigu.java2;

public class InnerClassTest {
	public static void main(String[] args) {
		// 创建Dog实例（静态的成员内部类）
		Person.Dog dog = new Person.Dog();
		dog.show();
		// 创建Bird实例（非静态的成员内部类）
//		Person.Bird bird = new Person.Bird();
		Person p = new Person();
		Person.Bird bird = p.new Bird();
		bird.sing();
		
		System.out.println("===============");
		bird.display("黄鹂");
	}
}

class Person {
	String name = "小明";
	int age;
	
	public void eat() {
		System.out.println("person eat");
	}
	// 静态成员内部类
	static class Dog {
		String name;
		int age;
		public void show() {
			System.out.println("卡拉是条狗");
//			eat();// 编译报错，调用非静态 
		}
	}
	// 非静态成员内部类
	class Bird {
		String name = "杜鹃";
		public void sing() {
			System.out.println("我是一只小小鸟");
			eat();
			Person.this.eat();// 调用外部类的非静态属性
		}
		
		public void display(String name) {
			System.out.println(name); // 方法的形参
			System.out.println(this.name); // 内部类的属性
			System.out.println(Person.this.name); // 外部类的属性
		}
	}
	
	public void method() {
		class AA {
			
		}
	}
	
	{
		class BB {
			
		}
	}
	
	public Person() {
		class CC {
			
		}
	}
}
```

开发中使用内部类的情况

```java
package com.atguigu.java2;

public class InnerClassTest1 {
	// 开发中很少见
	public void method() {
		class AA {
			
		}
	}
	
	// 开发中用内部类的情况：
	// 返回一个实现了Comparable接口的类的对象
	public Comparable getComparable() {
		// 创建一个实现了Comparable接口的类: 局部内部类
		// 方式一： 标准的方式
//		class MyComparable implements Comparable {
//
//			@Override
//			public int compareTo(Object o) {
//				// TODO Auto-generated method stub
//				return 0;
//			}
//			
//		}
//		
//		return new MyComparable();
		
		// 方式二：匿名
		return new Comparable() {

			@Override
			public int compareTo(Object o) {
				// TODO Auto-generated method stub
				return 0;
			}
			
		};
	}
}
```

## 8. 异常处理
### 8.1 异常体系结构与常见异常

Java虚拟机无法解决的严重问题。如：JVM系统内部错误，资源耗尽等严重情况。比如：StackOverflowError， OOM。一般不编写针对性的代码进行处理。

```java
package com.atguigu.java;

public class ErrorTest {
	public static void main(String[] args) {
		// 1. 栈溢出：java.lang.StackOverflowError
//		main(args);
		// 2. 堆溢出：java.lang.OutOfMemoryError
//		Integer[] arr = new Integer[1024*1024*1024];
	}
}

```

异常的体系结构

	java.lang.Throwable
	       | --- java.lang.Error:一般不编写针对性的代码进行处理
	       | --- java.lang.Exception: 可以进行异常的处理
	               | --- 编译时异常（checked）
	                         | --- IOException
	                                   | --- FileNotFoundException
	                         | --- ClassNotFoundException
	               | --- 异常时异常（unchecked）
	                         | --- NullPointerException
	                         | --- ArrayIndexOutofBoundsException
	                         | --- ClassCastException
	                         | --- NumberFormatException
	                         | --- InputMismatchException
	                         | --- ArithmeticException
 
面试题：常见的异常都有哪些？举例说明

```java
package com.atguigu.java;

import java.io.File;
import java.io.FileInputStream;
import java.util.Date;
import java.util.Scanner;

import org.junit.Test;

public class ExceptionTest {
	
	// **************** 以下是编译时异常 **********************
    @Test
    public void test7() {
    	File file = new File("hello.txt");
    	FileInputStream fis = new FileInputStream(file);
    	
    	int data = fis.read();
    	while (data != -1) {
    		System.out.println((char)data);
    		data = fis.read();
    	}
    	fis.close();
    }
	
	// **************** 以下是运行时异常 **********************
	// ArithmeticException
	@Test
	public void test6() {
		int a = 10;
		int b = 0;
		System.out.println(a / b);
	}
	
	// InputMismatchException
	@Test
	public void test5(){
		Scanner scanner = new Scanner(System.in);
		int score = scanner.nextInt();
		scanner.close();
	}
	// NumberFormatException
	@Test
	public void test4() {
		String str = "abc";
		int num = Integer.parseInt(str);
	}
	
	// ClassCastException
	@Test
	public void test3() {
		Object obj = new Date();
		String str = (String)obj;
		System.out.println(str);
	}
	
	//ArrayIndexOutofBoundsException
	@Test
	public void test2() {
		// 举例1
//		int[] arr = new int[10];
//		System.out.println(arr[10]);
		// 举例2
		String str = "abc";
		System.out.println(str.charAt(3));
	}
	
	// NullPointerException
	@Test
	public void test1() {
		// 举例1
//		int[] arr = null;
//		System.out.println(arr[3]);
		// 举例2
		String str = "abc";
		str = null;
		System.out.println(str.charAt(0));
	}
}
```


### 8.2 异常处理机制一: try-catch-finally

一·异常的处理：抓抛模型(转院比喻)

* 过程一：“**抛**”：程序在正常执行的过程中，一旦出现异常，就会在异常代码处生成一个对应异常类的对象，并将此对象抛出。一旦抛出对象以后，其后的代码就不再执行。

	关于异常对象的产生：
	- 系统自动生成的异常对象
	- 手动地生成一个异常对象，并抛出（throw）不同于throws

* 过程二：“**抓**”：可以理解为异常的处理方式： 1）try-catch-finally 2) throws

二·try-catch-finally的使用

	try {
	    // 可能出现异常的代码
	} catch(异常类型1 变量名1) {
	    // 处理异常的方式1
	} catch(异常类型2 变量名2) {
	    // 处理异常的方式2
	} catch(异常类型3 变量名3) {
	    // 处理异常的方式3
	} 
	...
	finanlly {
	    //一定会执行的代码
	}

说明：

1. finally是可选的.
2. 使用try将可能出现异常代码包装起来，在执行过程中，一旦出现异常，就会出现一个对应异常类的对象，
   根据此对象的类型，去catch中进行匹配。
3. 一旦try中的异常对象匹配到某一个catch时，就进入catch中进行异常的处理。一旦处理完成，就跳出当前的
  try-catch结构（在没有写finally的情况）。继续执行其后的代码
4. catch中的异常类型如果没有子父类关系，则谁声明在上，谁声明在下无所谓。
   catch中的异常类型如果满足子父类关系，则要求子类一定声明在父类的上面，否则，编译报错
5. 常用的异常对象处理的方式： 1）String getMessage()  2) printStackTrace()
6. 在try结构中声明的变量，在出了try结构以后，就不能再被调用
7. try-catch-finally结构可以嵌套

体会1：使用try-catch-finally处理编译时异常，使得程序在编译时就不再报错，但是运行时仍可能报错。相当于我们使用try-catch-finally将一个编译时可能出现的异常，延迟到运行时出现。
    
体会2: 开发中，由于运行时异常比较常见，所以我们通常就不针对运行时异常编写try-catch-finally了。针对于编译时异常，我们说一定要考虑异常的处理。

```java
package com.atguigu.java;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

import org.junit.Test;

public class ExceptionTest1 {
	
	@Test
    public void test2() {
		try {
			File file = new File("hello.txt");
	    	FileInputStream fis = new FileInputStream(file);
	    	
	    	int data = fis.read();
	    	while (data != -1) {
	    		System.out.println((char)data);
	    		data = fis.read();
	    	}
	    	fis.close();
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
    	
    }
	
	@Test
	public void test1() {
		String str = "123";
		str = "abc";
		try {
			int num = Integer.parseInt(str);
			System.out.println("hello --- 1");
		} catch (NumberFormatException e) {
//			System.out.println("出现数值转换异常了，不要着急");
			// String getMessage()
			System.out.println(e.getMessage());
			// printStackTrace();
			e.printStackTrace();
		} catch (NullPointerException e) {
			System.out.println("出现空指针异常了，不要着急");
		} catch (Exception e) {
			System.out.println("出现异常了，不要着急");
		}
		System.out.println("hello --- 2");
	}
}
```

finally具体使用

try-catch-finally中finally的使用：

1. finally是可选的
2. finally中声明的是一定会被执行的代码。即使catch中又出现异常了，try中有return语句，catch中有return语句等情况。
3. 像数据库连接，输入输出流，网络编程Socket等资源，JVM是不能自动回收的，我们需要自己手动地进行资源的释放。此时的资源释放，就需要声明在finally中。

```java
package com.atguigu.java;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

import org.junit.Test;

public class FinallyTest {
	
	@Test
	public void test2() {
		FileInputStream fis = null;
		try {
			File file = new File("hello.txt");
			fis = new FileInputStream(file);
			
			int data = fis.read();
			while (data != -1) {
				System.out.println((char)data);
				data = fis.read();
			}
			
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				if (fis != null)
					fis.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
	
	@Test
	public void testMethod() {
		int num = method();
		System.out.println(num);
		// output:
		// 我一定会被执行
		// 3
	}
	
	public int method() {
		try {
			int[] arr = new int[10];
			System.out.println(arr[10]);
			return 1;
		} catch (ArrayIndexOutOfBoundsException e) {
			e.printStackTrace();
			return 2;
		} finally {
			System.out.println("我一定会被执行");
			return 3;
		}
	}

	@Test
	public void test1() {
		try {
			int a = 10;
			int b = 0;
			System.out.println(a / b);
		} catch (ArithmeticException e) {
//			e.printStackTrace();
			int[] arr = new int[10];
			System.out.println(arr[10]);
		} catch (Exception e) {
			e.printStackTrace();
		} 
//		System.out.println("执行");
		finally {
			System.out.println("执行了finally");
		}
	}
}

```

### 8.3 异常处理机制二: throws

异常处理的方式二：`throws + 异常类型`

1. `throws + 异常类型`写在方法的声明处。指明此方法执行时，可能会抛出的异常类型。一旦当方法体执行时，出现异常，仍会在异常代码出生成一个异常类的对象，此对象满足throws后异常类型时，就会被抛出。异常代码后续的代码，就不再执行。
2. 体会：try-catch-finally: 真正的将异常给处理掉了。throws的方式只是将异常抛给了方法的调用者。并没有真正将异常处理掉。(将病人转到上级医院)
        
3. 开发中如何选择使用try-catch-finally 还是使用throws？
	* 如果父类中被重写的方法没有throws方式处理异常，则子类重写的方法也不能使用throws，意味着：如果子类重写的方法中有异常，必须使用try-catch-finally方式处理。
	* 执行的方法a中，先后又调用了另外的几个方法，这几个方法是递进关系执行的。我们建议这几个方法使用throws的方式进行处理。而执行的方法a可以考虑使用try-catch-finally进行处理。

```java
package com.atguigu.java;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class ExceptionTest2 {
	
	public static void main(String[] args) {
		try {
			method2();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public static void method3() {
		try {
			method2();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public static void method2() throws IOException {
		method1();
	}
	
	public static void method1() throws FileNotFoundException, IOException {
		File file = new File("hello.txt");
    	FileInputStream fis = new FileInputStream(file);
    	
    	int data = fis.read();
    	while (data != -1) {
    		System.out.println((char)data);
    		data = fis.read();
    	}
    	fis.close();
	}
}
```

```java
package com.atguigu.java1;

import java.io.FileNotFoundException;
import java.io.IOException;

/*
 * 子类重写的规则之一：
 * 子类重写的方法抛出的异常类型
 */
public class OverrideTest {
	public static void main(String[] args) {
		OverrideTest test = new OverrideTest();
		test.display(new SuperClass());
	}
	public void display(SuperClass s) {
		try {
			s.method();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}

class SuperClass {
	public void method() throws IOException {
		
	}
}

class SubClass extends SuperClass {
	public void method() throws FileNotFoundException {
		
	}
}
```

### 8.4 手动抛出异常: throw

关于异常对象的产生：

* 系统自动**生成**的异常对象
* 手动地**生成**一个异常对象，并抛出（throw）不同于throws

```java
package com.atguigu.java2;

public class StudentTest {
	public static void main(String[] args) {
		
		try {
			Student s = new Student();
			s.regist(1001);
			System.out.println(s);
		} catch (Exception e) {
			e.printStackTrace();
			System.out.println(e.getMessage());
		}
		
	}
}

class Student {
	private int id;
	public void regist(int id) throws Exception {
		if (id > 0) {
			this.id = id;
		} else {
//			System.out.println("您输入的数据非法！");
			// 手动抛出异常对象,一般为Exception或RuntimeException
//			throw new RuntimeException("您输入的数据非法！");
			throw new Exception("您输入的数据非法！");
		}
	}
}
```

### 8.5 用户自定义异常类

如何自定义异常类？

1. 继承于现有结构的：RuntimeException, Exception
2. 提供serialVersionUID
3. 提供重载的构造器

```java
package com.atguigu.java2;

public class MyException extends RuntimeException {
	
    static final long serialVersionUID = -7034897190745766939L;

	public MyException() {
		
	}
	
	public MyException(String msg) {
		super(msg);
	}
}
```

一道实践题

```java
package com.atguigu.exer;
/*
 * 编写应用程序EcmDef.java，接受命令行的两个参数，要求不能输入负数，计算两数相除。
 *   对数据类型不一致（NumberFormatException），缺少命令行参数（ArrayIndexOutOfBoundsException），
 *   除0（ArithmeticException）及输入负数（EcDef自定义的异常）进行异常处理。
 *   
 * 提示：
 * 1）在主类（EcmDef）中定义异常方法（ecm）完成两数相除功能，
 * 2）在main()方法中使用异常处理语句进行异常处理。
 * 3）在程序中，自定义对应输入负数的异常类（EcDef）。
 * 4）运行时接受参数java EcmDef 20 10 // args[0]="20", args[1]="10"
 * 5）Integer类的static方法parseInt(String s)将s转换为对应的int值。
 */
public class EcmDef {
	public static void main(String[] args) {
		try {
			int i = Integer.parseInt(args[0]);
			int j = Integer.parseInt(args[1]);
			System.out.println(ecm(i, j));
		} catch (NumberFormatException e) {
			System.out.println("数据类型不一致");
		} catch (ArrayIndexOutOfBoundsException e) {
			System.out.println("缺少命令行参数");
		} catch (ArithmeticException e) {
			System.out.println("除0");
		} catch (EcDef e) {
			System.out.println(e.getMessage());
		}
	}
	
	public static int ecm(int i, int j) throws EcDef {
		if (i < 0 || j < 0) {
			throw new EcDef("分子或分母为负数了");
		}
		return i / j;
	}
}


class EcDef extends RuntimeException {
    static final long serialVersionUID = -7034897190745766939L;

    public EcDef() {
    	
    }
    
    public EcDef(String msg) {
    	super(msg);
    }
}
```











