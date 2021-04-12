# Java零基础教程（四.面向对象-继承inheritance）


## 1. 继承性的使用

#### 面向对象的特征之二：继承性
 
一·继承性的好处 why？

1. 减少了代码的冗余，提高了代码的复用性
2. 便于功能的扩展
3. 为之后多态性的使用，提供了前提

二·继承性的格式：`class A extends B{}`

* A: 子类，派生类，subclass
* B: 父类，超类，基类，superclass

1. 体现：一旦子类A继承父类B以后，子类A中就获取了父类B中声明的所有的结构：属性，方法。
   特别地，父类中声明为private的属性或方法，子类继承父类以后，仍然认为获取了父类中私有的结构。
   只是因为封装性的影响，使得子类不能直接调用父类的结构而已。权限修饰符解决的是调用的问题。继承性解决的是能不能获取的问题。
2. 子类继承父类以后，还可以声明自己特有的属性或方法，实现功能的扩展。
   子类和父类的关系，不同于子集和集合的关系。
     
extends：扩展
     
三·Java中关于继承性的规定

1. 一个类可以被多个子类继承
2. Java中类的单继承性：一个类只能有一个父类
3. 子父类是相对的概念。
4. 子类直接继承的父类，称为直接父类。间接继承的父类，称为间接父类。
5. 子类继承父类以后，就获取了直接父类以及所有间接父类中声明的属性和方法。
   
四· Object类

1. 如果我们没有显式地声明一个类的父类的话，则此类继承于`java.lang.Object`类
2. 所有的java类（除java.lang.Object类之外）都直接或间接的继承于`java.lang.Object`类
3. 意味着，所有的java类具有`java.lang.Object`类声明的功能。

```java
package com.atguigu.java;

public class ExtendsTest {
	public static void main(String[] args) {
		Person p1 = new Person();
		p1.setAge(1);
		
		Student s1 = new Student();
		s1.eat();
		s1.sleep();
		
	}
}
```

#### 实例化过程

子类对象实例化的全过程

1. 从结果上来看：（继承性）
   子类继承父类以后，就获取了父类中声明的属性或方法。创建子类的对象，在堆空间中，就会加载所有父类中声明的属性。
  
2. 从过程上来看：
   当我们通过子类的构造器创建子类对象时，我们一定会直接或间接地调用其父类的构造器，进而调用父类的父类的构造器，直到调用了`java.lang.Object`类中空参的构造器为止。正因为加载过所有的父类的结构，所以才可以看到内存中有父类中的结构，子类对象才可以考虑进行调用。
    
明确：虽然创建子类对象时，调用了父类的构造器，但是自始自终就创建过一个对象，即为`new`的子类对象。

## 2. 在子类中重写方法

### 2.1 方法重写的规定

方法的重写(override / overwrite)

1. 重写：子类继承父类以后，可以对父类中同名同参的方法，进行覆盖操作
2. 应用：重写以后，当创建子类对象以后，通过子类对象调用子父类中的同名同参的方法时，实际执行的是子类重写父类的方法。

面试题：区分方法的重载与重写

3. 重写的规定：
   方法的声明： `权限修饰符 返回值类型 方法名(形参列表) throws 异常类型 {方法体}`
        
    约定俗成：子类中的叫重写的方法，父类中的叫被重写的方法
    * 子类重写的方法的方法名和形参列表与父类被重写的方法的方法名和形参列表相同。
    * 子类重写的方法的权限修饰符不小于父类被重写的方法的权限修饰符(类比：摊煎饼覆盖)
      
      > cannot reduce the visibility of the inherited method from the superclass
      
       **特殊情况**：子类不能重写父类中声明为private权限的方法，可以理解为子类重新写了一个方法
                  太小了，覆盖不了
                 https://stackoverflow.com/a/39072326
    * 返回值类型：

       1. 父类被重写的方法的返回值类型是`void`，则子类重写的方法的返回值类型只能是`void`
       2. 父类被重写的方法的返回值类型是A类型，则子类重写的方法的返回值类型可以是A类或者A类的子类
       3. 父类被重写的方法的返回值类型是基本数据类型，则子类重写的方法的返回值类型必须是**相同**的基本数据类型
    
    * 子类重写的方法抛出的异常类型**不大于**父类被重写的方法抛出的异常类型。（具体放到异常处理时候讲）
   
	子类和父类中的同名同参数的方法要么都声明为`非static`的（考虑重写），要么都声明为`static`的（不是重写）



注意下面`s.walk()`的调用

```java
package com.atguigu.java1;

public class PersonTest {
	public static void main(String[] args) {
		
		Student s = new Student("CS");
		
		s.eat();// student eat
		s.walk(10); // person walk, person show, student eat
		// https://stackoverflow.com/a/6896598
	}
}
/*
class Person {
	
	String name;
	int age;
	
	public Person() {
		super();
	}

	public Person(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}

	public void eat() {
		System.out.println("person eat");
	}

	public void walk(int distance) {
		System.out.println("person walk " + distance + "km");
		show();
		eat();
	}
	
	private void show() {
		System.out.println("person show");
	}
	
	public Object info() {
		return null;
	}
}

class Student extends Person {
	
	String major;


	public Student() {
		super();
	}

	public Student(String major) {
		this.major = major;
	}
	
	public void study() {
		System.out.println("major is " + major);
	}
	
	public void eat() {
		System.out.println("student eat");
	}
	
	public void show() {
		System.out.println("student show");
	}
	
	public String info() {
		return null;
	}
}
*/
```

### 2.2 再回顾：权限修饰符

`private`只能自己访问, default可以跨类（同包下）, `protected`子类可以访问（可以跨包）, `public`都可以访问。

### 2.3 `super`关键字
super关键字的使用

1. super理解为：父类的
2. super可以用来调用: 属性，方法，构造器
3. super的使用
   * 我们可以在子类的方法或构造器中。通过使用“super.属性”或“super.方法”的方式，显式地调用父类中声明的属性或方法。但是，通常情况下，我们习惯省略“super.”
   * 特殊情况：当子类和父类中定义了**同名**的属性时，我们要想在子类中调用父类中声明的属性，则必须显式地使用“super.属性”的方式，表明调用的是父类中声明的属性。
   * 特殊情况：当子类重写了父类中的方法以后，我们要想在子类中调用父类中声明的方法时，则必须显式地
              使用“super.方法”的方式，表明调用的是父类中声明的方法。

4. super调用构造器
   * 我们可以在子类的构造器中显式地使用“super(形参列表)”的方式，调用父类中声明的指定构造器
   * “super(形参列表)”的使用，必须声明在子类构造器的**首行**。
   * 我们在类的构造器中，针对于“this(形参列表)”或“super(形参列表)”**只能二选一**，不能同时出现
   * 在构造器首行，没有显式地声明“this(形参列表)”或“super(形参列表)”，则默认调用的是父类中空参构造器
   * 在类的多个构造器中，至少有一个类的构造器中使用了“super(形参列表)”，调用父类中的构造器


```java
package com.atguigu.java3;

public class SuperTest {

	public static void main(String[] args) {
		Student s = new Student();
		s.study();// student study, student eat, person eat
	}

}

/*
public class Person {
	String name;
	int age;
	int id;// 身份证
	
	public Person() {
		
	}
	
	public Person(String name) {
		this.name = name;
	}
	
	public Person(String name, int age) {
		this(name);
		this.age = age;
	}
	
	public void eat() {
		System.out.println("person eat");
	}
	
	public void walk() {
		System.out.println("person walk");
	}
}

package com.atguigu.java3;

public class Student extends Person {
	String major;
	int id;//学号
	
	public Student() {
		
	}
	
	public Student(String major) {
		this.major = major;
	}
	
	public void eat() {
		System.out.println("student eat");
	}
	
	public void study() {
		System.out.println("student study");
		this.eat();
		super.eat();
	}
	
}

*/
```















