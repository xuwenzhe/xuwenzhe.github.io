# Java零基础教程（五.面向对象-多态Polymorphism）


## 1. 多态性使用和理解

面向对象特征之三：多态性

1. 理解多态性：可以理解为一个事物的多种形态。
2. 何为多态性：对象的多态性，父类的引用指向子类的对象（或子类的对象赋给父类的引用）
3. 多态的使用：虚拟方法调用

   有了对象的多态性以后，我们在编译期，只能调用父类中声明的方法，但在运行期，我们实际执行的是子类重写父类的方法。

   总结：编译看左边，运行看右边。
   
   便于站在高处(more generalized)指挥，而不局限于实施细节
   
4. 多态性的使用前提：1）类的继承关系 2）方法的重写
5. 对象的多态性，只适用于方法，不适用于属性。子类不会覆盖父类的同名属性，而是内存都有，编译和运行都看左边。

虚拟方法调用：（Virtual Method Invocation）

子类中定义了与父类同名同参数的方法，在多态情况下，将此时父类的方法称为虚拟方法，父类根据赋给它的不同子类对象，动态调用属于子类的该方法。这样的方法调用在编译期是无法确定的。


```java
package com.atguigu.java4;

public class PersonTest {

	public static void main(String[] args) {
		Person p1 = new Person();
		p1.eat();
		
		Man man = new Man();
		man.eat();
		man.earnMoney();
		
		// **************************
		// 对象的多态性：父类的引用指向子类的对象
		Person p2 = new Man();
		//Person p3 = new Woman();
		// 多态的使用：当调用子父类同名同参数方法时，实际执行的是子类重写父类的方法 --- 虚拟方法调用
		p2.eat();
		p2.walk();
		// wrong
		// p2.earnMoney()
	}

}

```

多态性使用例子

```java
package com.atguigu.java4;

import java.sql.Connection;

// 多态性使用
// 举例一
public class AnimalTest {
	public static void main(String[] args) {
		AnimalTest test = new AnimalTest();
		test.func(new Dog());
		test.func(new Cat());
	}
	
	public void func(Animal animal) {
		animal.eat();
		animal.shout();
	}
}

class Animal {
	public void eat() {
		System.out.println("animal eat");
	}
	public void shout() {
		System.out.println("animal shout");
	}
}

class Dog extends Animal {
	public void eat() {
		System.out.println("dog eat");
	}
	public void shout() {
		System.out.println("dog shout");
	}
}

class Cat extends Animal {
	public void eat() {
		System.out.println("cat eat");
	}
	public void shout() {
		System.out.println("cat shout");
	}
}

// 举例二
class Order {
	public void method(Object obj) {
		
	}
}

// 举例三
class Driver {
	public void doData(Connection conn) { // conn = new MySqlConnection(); new 
		// 规范的步骤去操作数据
//		conn.method1();
//		conn.method2();
//		conn.method3();
	}
}
```

面试：

Q：多态性是编译时行为还是运行时行为？
A：运行时行为

方法的重载与重写

1. 二者的定义细节：略
2. 从编译和运行的角度看：
重载，是指允许存在多个同名方法，而这些方法的参数不同。编译器根据方法不同的参数表，对同名方法的名称做修饰。对于编译器而言，这些同名方法就成了不同的方法。**他们的调用地址在编译期就绑定了**。Java的重载是可以包括父类和子类的，即子类可以重载父类的同名不同参数的方法。所以：对于重载而言，在方法调用之前，编译器就已经确定了所要调用的方法，这称为“早绑定”或“静态绑定”；

而对于多态，只有等到方法调用的那一刻，届时运行器才会确定所要调用的具体方法，这称为“晚绑定”或“动态绑定”。

引用一句Bruce Eckel的话：

	> “不要犯傻，如果它不是晚绑定，它就不是多态。” - Bruce Eckel

```java
package com.atguigu.java5;

import java.util.Random;

public class InterviewTest {
	public static void main(String[] args) {
		int key = new Random().nextInt(3);
		System.out.println(key);
		Animal animal = getInstance(key);
		animal.eat();
	}
	
	public static Animal getInstance(int key) {
		switch (key) {
		case 0:
			return new Cat();
		case 1:
			return new Dog();
		default:
			return new Sheep();
		}
	}
}

class Animal {
	protected void eat() {
		System.out.println("animal eat");
	}
}

class Cat extends Animal {
	protected void eat() {
		System.out.println("cat eat");
	}
}
class Dog extends Animal {
	protected void eat() {
		System.out.println("dog eat");
	}
}
class Sheep extends Animal {
	protected void eat() {
		System.out.println("sheep eat");
	}
}

```

再来看一个例子

练习：

1. 若子类重写了父类方法，就意味着子类里定义的方法彻底覆盖了父类里的同名方法，系统将不可能把父类里的方法转移到子类中。编译看左边，运行看右边

对于实例**变量**不存在这样的现象，即使子类里定义了与父类完全相同的实例变量，这个实例变量依然不可能覆盖父类中定义的实例变量：编译运行都看左边

```java
package com.atguigu.java;

public class FieldMethodTest {
	public static void main(String[] args) {
		Sub s = new Sub();
		System.out.println(s.count);// 20
		s.display(); //20
		
		Base b = s;//多态性
		// == 对于引用数据类型来讲，比较的是两个引用数据类型变量的地址值是否相同
		System.out.println(b == s);// true
		System.out.println(b.count);//10
		b.display(); //20
	}
}


class Base {
	int count = 10;
	
	public void display() {
		System.out.println(this.count);
	}
}

class Sub extends Base {
	int count = 20;
	
	public void display() {
		System.out.println(this.count);
	}
}
```

## 2. 向下转型与instanceof关键字

### 2.1 向下转型

有了对象的多态性以后，内存中实际上是加载了子类特有的属性和方法的，但是由于变量声明为父类类型，导致编译时，只能调用父类中声明的属性和方法。子类特有的属性和方法不能调用。
		
如何才能调用子类特有的属性和方法？使用强制类型转换符，向下转型

		     较高级的基本数据类型（double）
		     ｜             ^
	    强制类型转化          |
		     ｜         自动类型提升
		     v             ｜
		     较低级的基本数据类型（int）
		
		
		     父类（Person）
		     ｜         ^
	    向下转型         |
		     ｜      向上转型：多态
		     v         ｜
		     子类（Student）


### 2.2 instanceof关键字使用

`a instanceof A`：判断对象a是否是类A的实例。如果是返回true，如果不是返回false。

使用情景：为了避免在向下转型时出现ClassCastException的异常，我们在向下转型之前，先进行`instanceof`的判断，一旦返回true，就进行向下转型。如果返回false，不进行向下转型。

如果`a instanceof A`返回true，则`a instanceof B`也返回true。其中类B是类A的父类。


```java
package com.atguigu.java;

public class PersonTest {

	public static void main(String[] args) {
		Person p1 = new Person();
		p1.eat();
		
		Man man = new Man();
		man.eat();
		man.earnMoney();
		
		// **************************
		// 对象的多态性：父类的引用指向子类的对象
		Person p2 = new Man();
		//Person p3 = new Woman();
		// 多态的使用：当调用子父类同名同参数方法时，实际执行的是子类重写父类的方法 --- 虚拟方法调用
		p2.eat();
		p2.walk();
		// wrong
		// p2.earnMoney()
		
		// **************************
		// 不能调用子类所特有的方法，属性：编译时，p2是Person类型。
		System.out.println("************************");
		Man m1 = (Man)p2;
		m1.earnMoney();
		
		// 使用强转时，可能出现ClassCastException的异常
		// Woman w1 = (Woman)p2;
		// w1.goShopping();
		// 使用instanceof判断
		if (p2 instanceof Woman) {
			Woman w1 = (Woman)p2;
			w1.goShopping();
		}
		if (p2 instanceof Man) {
			m1 = (Man)p2;
			m1.earnMoney();
		}
		
		// 练习：
		// 问题一： 编译时通过，运行时不通过
		// 举例一：
//		Person p3 = new Woman();
//		Man m3 = (Man)p3;
		// 举例二：
//		Person p4 = new Person();
//		Man m4 = (Man)p4;
		
		// 问题二：编译通过，运行时也通过
		Object obj = new Woman();
		Person p = (Person)obj;
		
		// 问题三：编译不过
//		Man m5 = new Woman();
	}
}
```

## 3. Object类与equals(),toString()方法

### 3.1 Object类

`java.lang.Object`类

1. Object类是所有Java类的根父类
2. 如果在类的声明中未使用extends关键字指明其父类，则默认父类为`java.lang.Object`类
3. Object类中的功能(属性，方法)就具有通用性。
	* 属性：无
	* 方法：`equals()`, `toString()`, `getClass()`, `hashCode()`, `clone()`, `finalize()`, `wait()`, `notify()`, `notifyAll()`
4. Object类只声明了一个空参构造器

面试题

final, finally, finalize的区别？

```java
package com.atguigu.java1;

public class ObjectTest {
	public static void main(String[] args) {
		Order order = new Order();
		System.out.println(order.getClass().getSuperclass());
	}
}

class Order {
	
}
```

### 3.2 equals()使用
面试题：`==`和`equals()`区别

一·回顾`==`的使用

运算符

1. 可以使用在基本数据类型变量和引用数据类型变量中
2. 如果比较的是基本数据类型变量：比较两个变量保存的数据是否相等。（不一定类型要相同）
   
   如果比较的是引用数据类型变量：比较两个对象的地址值是否相同，即两个引用是否指向同一个对象实体

   补充：==符号使用时，必须保证符号左右两边的变量类型一致，否则编译报错。
   
二·`equals()`方法的使用：

1. 是一个方法，而非运算符
2. 只能适用于引用数据类型
3. Object类中equals()的定义：
	
		public boolean equals(Object obj) {
			return (this == obj);
		}

   说明：Object类中定义的`equals()`和`==`的作用是相同的：比较两个对象地址值是否相同，即是否都指向同一个对象实体
   
4. 像String, Date, File, 包装类等都重写了Object类中的`equals()`方法，重写以后，比较的不是两个引用地址是否相同，而是比较两个对象的“实体内容”是否相同。
5. 通常情况下，我们自定义的类如果使用`equals()`的话，也通常是比较两个对象的“实体内容”是否相同。那么，我们就需要对Object类中的`equals()`进行重写，重写的原则：比较两个对象的实体内容是否相同。

```java
package com.atguigu.java1;

public class EqualsTest {
	public static void main(String[] args) {
		int i = 10;
		int j = 10;
		double d = 10.0;
		System.out.println(i == j);// true
		System.out.println(i == d);// true
		
		char c = 10;
		System.out.println(i == c);// true
		
		char c1 = 'A';
		char c2 = 65;
		System.out.println(c1 == c2);// true
		
		String str1 = new String("atguigu");
		String str2 = new String("atguigu");
		System.out.println(str1 == str2);// false
		System.out.println(str1.equals(str2));// true
	}
}
```


### 3.3 `toString()`使用

Object类中toString()的使用

1. 当我们输出一个对象的引用时，实际上就是调用当前对象的toString()

2. Object类中toString()的定义

	    public String toString() {
	        return getClass().getName() + "@" + Integer.toHexString(hashCode());
	    }

3. 像String, Date, File, 包装类等都重写了Object类中的toString()方法。使得在调用对象的toString()时，返回“实体内容”信息
   
4. 自定义类也可以重写toString()方法，当调用此方法时，返回对象的“实体内容”。

## 4. 包装类，基本数据类型，String三者间的转换

包装类的使用

1. Java提供了8种基本数据类型对应的包装类，使得基本数据类型的变量具有类的特征
2. 掌握的：基本数据类型，包装类，String三者间的相互转换

	* 基本数据类型 -> 包装类：调用包装类的构造器, 基本数据类型无法利用多态
	* 包装类 -> 基本数据类型：调用包装类的xxxValue，包装类不能加减乘除

JDK 5.0新特性：自动装箱与自动拆箱

	* 基本数据类型，包装类 -> String类型: String.valueOf()
	* String类型 -> 基本数据类型，包装类: 调用包装类的parseXxx()

```java
package com.atguigu.java2;

public class WrapperTest {
	public static void main(String[] args) {
		int num1 = 10;
		Integer in1 = new Integer(num1);
		System.out.println(in1.toString());
		
		Integer in2 = new Integer("123");
		System.out.println(in2.toString());
		
		Float f1 = new Float(12.3f);
		Float f2 = new Float("12.3");
		System.out.println(f1);
		System.out.println(f2);
		
		Boolean b1 = new Boolean(true);
		Boolean b2 = new Boolean("true");
		Boolean b3 = new Boolean("TrUe");
		System.out.println(b3); // true
		
		int i1 = in1.intValue();
		float f3 = f1.floatValue();
		System.out.println(i1);
		System.out.println(f3);
		
		//自动装箱
		int num2 = 10;
		Integer in3 = num2;// 自动装箱
		System.out.println(in3);
		int num3 = in3;// 自动拆箱
		
		// 基本数据类型，包装类 -> String类型：调用String重载的valueOf(Xxx xxx)
		int n1 = 10;
		// 方式1: 连接运算
		String str1 = n1 + "";
		// 方式2: 调用String的valueOf
		String s1 = String.valueOf(n1); // "10"
		String s2 = String.valueOf(new Double(12.4)); // "12.4" 
		System.out.println(s1 + ',' + s2);
		
		int x = Integer.parseInt("123");
		boolean b = Boolean.parseBoolean("true");
		System.out.println(x);
		System.out.println(b);
	}
}

```

一道考题

```java
package com.atguigu.java2;

public class InterviewTest {
	public static void main(String[] args) {
		
	}
	
	public void test1() {
		Object o1 = true ? new Integer(1) : new Double(2.0);
		System.out.println(o1); //1.0, 编译三元标识符，自动类型提升为Double，多态toString
	}
	
	public void test2() {
		Object o2;
		if (true)
			o2 = new Integer(1);
		else
			o2 = new Double(2.0);
		System.out.println(o2); // 1, 不需要自动类型提升
	}
	
	public void test3() {
		Integer i = new Integer(1);
		Integer j = new Integer(1);
		System.out.println(i == j); // false
		
		// Integer内部定义了IntegerCache结构，IntegerCache中定义了Integer[],
		// 保存了-128～127范围的整数。如果我们使用自动装箱的方式，给Integer赋值的范围在
		// -128～127范围内时，可以直接使用数组中的元素，不用再new了。目的：提高效率
		Integer m = 1;
		Integer n = 1;
		System.out.println(m == n); // true
		
		Integer x = 128; // 相当于new了一个Integer对象
		Integer y = 128; // 相当于又new了一个Integer对象
		System.out.println(x == y); // false
	}
}
```

体会包装类的引入意义

```java
package com.atguigu.java2;

import java.util.Scanner;
import java.util.Vector;

public class ScoreTest {
	public static void main(String[] args) {
		// 1. 实例化Scanner，用于从键盘获取学生成绩
		Scanner scan = new Scanner(System.in);
		
		// 2. 创建Vector对象
		Vector v = new Vector();
		
		// 3. 给Vector中添加元素
		int maxScore = 0;
		while (true) {
			System.out.println("input Student's score (negative number to end)");
			int score = scan.nextInt();
			if (score < 0)
				break;
			// before jdk5.0
//			Integer el = new Integer(score);
//			v.addElement(el); // 多态
			// after jdk5.0
			v.addElement(score); // 自动装箱
			// 4. 获取成绩最大值
			if (score > maxScore)
				maxScore = score;
		}
		
		// 5. 遍历Vector，得到每个学生成绩，并与最大值比较，得到等级
		char level;
		for (int i = 0; i < v.size(); i++) {
			Object obj = v.elementAt(i);
			// before jdk5.0
//			Integer el = (Integer)obj;
//			int score = el.intValue();
			// after jdk5.0
			int score = (int)obj;
			if (maxScore - score <= 10) {
				level = 'A';
			} else if (maxScore - score <= 20) {
				level = 'B';
			} else if (maxScore - score <= 30) {
				level = 'C';
			} else {
				level = 'D';
			}
			System.out.println("student score: " + score + ", grade: " + level);
		}
	}
}

```



