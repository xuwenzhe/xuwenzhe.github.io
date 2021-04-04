# Java零基础教程（三.面向对象-封装Encapsulation）


## 1. 类和对象

### 1.1 面向对象的思想
```java
package com.atguigu.java;

/*
 *  一· Java面向对象学习的三条主线
 * 1. Java类及类的成员：属性，方法，构造器，代码块，内部类
 * 2. 面向对象的三大特征：封装性，继承性，多态性，（抽象性）
 * 3. 其他关键字：this, super, static, final, abstract, interface, package, import等
 * 
 * 二·“人把大象装进冰箱”
 * 1. 面向对象：强调的是功能行为，以函数为最小单位，考虑怎么做。
 *     1）把冰箱门打开
 *     2）抬起大象，塞进冰箱
 *     3）把冰箱门关闭
 * 2. 面向对象：强调具备了功能的对象，以类/对象为最小单位，考虑[谁]来做。
 *     人 {
 *         打开(冰箱) {
 *             冰箱.开开();
 *         }
 *         抬起(大象) {
 *             大象.进入(冰箱);
 *         }
 *         关闭(冰箱) {
 *             冰箱.闭合();
 *         }
 *     }
 *     
 *     冰箱 {
 *         开开(){}
 *         闭合(){}
 *     }
 *     
 *     大象 {
 *         进入(冰箱){}
 *     }
 *     程序员从面向过程的执行者转化成了面向对象的指挥者
 * 
 * 三·面向对象的两个要素
 * 类：对一类事物的描述，是抽象的，概念上的定义
 * 对象：是实际存在的该类事物的每个个体，因而也称为实例（instance）
 * > 面向对象程序设计的重点是类的设计
 * > 设计类，就是设计类的成员
 */

public class OOPTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

	}

}

```

### 1.2 类的属性和方法

```java
package com.atguigu.java;

/*
 * 一·设计类，其实就是设计类的成员
 * 
 * 属性 = 成员变量 = field
 * 方法 = 成员方法 = 函数 = method
 * 
 * 创建类的对象 = 类的实例化
 * 
 * 二·类和对象的使用（面向对象思想落地）
 * 1. 创建类，设计类的成员
 * 2. 创建类的对象
 * 3. 通过“对象.属性”或“对象.方法”调用对象的结构
 * 
 * 三·如果创建了一个类的多个对象，则每个对象都独立的拥有一套类的属性（非static）
 * 意味着：如果我们修改一个对象的属性a，则不影响另外一个对象属性a的值。
 */

// 测试类
public class PersonTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		// 2. 创建Person类的对象
		Person p1 = new Person();
		// Scanner scanner = new Scanner(System.in);
		
		// 调用对象的结构：属性，方法
		// 调用属性： “对象.属性”
		p1.name = "Tom";
		p1.isMale = true;
		System.out.println(p1.name);
		
		// 调用方法：“对象.方法”
		p1.eat();
		p1.sleep();
		p1.talk("Chinese");
		
		// ********************************
		Person p2 = new Person();
		System.out.println(p2.name);// null
		System.out.println(p2.isMale);// false
		
		// ********************************
		// 将p1变量保存的对象地址值赋给p3，导致p1和p3指向了堆空间的同一个对象实体
		Person p3 = p1;
		System.out.println(p3.name);//Tom
		
		p3.age = 10;
		System.out.println(p1.age);//10
	}

}

// 1. 创建类
class Person {
	// 属性
	String name;
	int age;
	boolean isMale;
	
	// 方法
	public void eat() {
		System.out.println("人可以吃饭");
	}
	
	public void sleep() {
		System.out.println("人可以睡觉");
	}
	
	public void talk(String language) {
		System.out.println("人可以说话，使用的是： " + language);
	}
}
```

### 1.3 属性 vs. 局部变量

```java
package com.atguigu.java;

/*
 * 类中属性的使用
 * 
 * 属性 vs 局部变量
 * 1. 相同点：
 *     1）定义变量的格式：数据类型 变量名 = 变量值
 *     2）先声明，后使用
 *     3）变量都有其对应的作用域
 *     
 * 2. 不同点：
 *     1） 在类中声明位置的不同
 *     属性：直接定义在类的一对{}
 *     局部变量：声明在方法内，方法形参，代码块内，构造器形参，构造器内部的变量
 *     
 *     2）关于权限修饰符的不同
 *     属性：可以在声明属性时，指明其权限，使用权限修饰符。
 *     常用的权限修饰符：private, public, 缺省, protected ---> 封装性
 *     目前，大家声明属性时，都是用缺省就可以了
 *     局部变量：不可以使用权限修饰符。
 *     
 *     3）默认初始化的情况：
 *     属性：类的属性，根据其类型，都有默认初始化值。
 *         整型(byte, short, int, long) 0
 *         浮点型(float, double) 0.0
 *         字符型(char), 0 或 '\u0000'
 *         boolean: false
 *         引用数据类型(类，数组，接口) null
 *         
 *     局部变量：没有默认初始化值。
 *         意味着，我们在调用局部变量之前，一定要显式复制。
 *         特别地：形参在调用时，我们再赋值即可。
 *         
 *     4）在内存中家在的位置不同：
 *     属性：加载到堆空间中（非static）
 *     局部变量：加载到栈空间
 * 
 */

public class UserTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		User u1 = new User();
		System.out.println(u1.name);
		System.out.println(u1.age);
		System.out.println(u1.isMale);
	}

}

class User {
	// 属性
	String name;
	public int age;
	boolean isMale;
	
	public void talk(String language) {// language: 形参，也是局部变量
		System.out.println("我们使用" + language + "进行交流");
	}
	
	public void eat() {
		String food = "烙饼";// 局部变量，不赋值会报错
		System.out.println("北方人喜欢吃: " + food);
	}
}
```

### 1.4 类方法的声明和使用

```java
package com.atguigu.java;
/*
 * 类中方法的声明和使用
 * 
 * 方法：描述类应该具有的功能
 * 比如：Math类: sqrt() random()
 *      Scanner类: nextXxx()
 *      Arrays类:  sort(), binarySearch(), toString(), equals()
 * 
 * 1. 举例：
 * public void eat(){}
 * public void sleep(int hour){}
 * public String getName(){}
 * public String getNation(String nation){}
 * 
 * 2. 方法的声明： 权限修饰符 返回值类型 方法名(形参列表) {
 *                   方法体
 *               }
 *     static, final, abstract 来修饰的方法，后面再讲
 *     
 * 3. 说明：
 *     1） 关于权限修饰符：默认方法的权限修饰符先都使用public
 *         Java规定的4种权限修饰符：private, public, 缺省, protected ---> 封装性细说
 *     2） 返回值类型：有返回值 vs 没有返回值
 *         2.1）如果方法有返回值，则必须在方法声明时，指定返回值的类型。
 *              同时方法中，需要使用return关键字类返回指定类型的变量或常量："return 数据"。
 *              如果方法没有返回值，则方法声明时，使用void来表示。通常，没有返回值的方法中，就不需要return。
 *              但是如果使用的话，只能“return;”表示结束此方法的意思。
 *         2.2）我们定义方法该不该有返回值？
 *              题目要求
 *              凭经验：具体问题具体分析
 *     3） 方法名：属于标识符，遵循标识符的规则和规范，“见名知意”
 *     4） 形参列表： 方法可以声明0个，1个，或多个形参。
 *         4.1) 格式：数据类型1 形参1, 数据类型2 形参2, ...
 *         4.2) 我们定义方法时，该不该定义形参？
 *              题目要求
 *              凭经验：具体问题具体分析
 *     5） 方法体：方法功能的体现
 *         
 * 4. return关键字的使用：
 *    1）使用范围：使用在方法体中
 *    2）作用：
 *         结束方法
 *         针对于有返回值类型的方法，使用"return 数据"方法返回所要的数据。
 *    3）注意点：return关键字后面不可以声明执行语句。 
 *    
 * 5. 方法的使用中，可以调用当前类的属性或方法
 *       特殊的：方法A中有调用了方法A：递归方法。
 *    方法中不可以定义方法
 */
public class CustomerTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

	}

}


class Customer {
	
	String name;
	int age;
	boolean isMale;
	
	public void eat() {
		System.out.println("客户吃饭");
	}
	
	public void sleep(int hour) {
		System.out.println("休息了" + hour + "个小时");
	}
	
	public String getName() {
		return name;
	}
	
	public String getNation(String nation) {
		String info = "我的国籍是：" + nation;
		return info;
	}
}
```

### 1.5 匿名对象

```java
package com.atguigu.java;
/*
 * 匿名对象的使用
 * 1. 理解： 我们创建的对象，没有显式地赋给一个变量名，即为匿名对象。
 * 2. 特征：匿名对象只能调用一次。
 * 3. 使用：如下
 */

public class InstanceTest {
	public static void main(String[] args) {
		new Phone().price = 1999;
		new Phone().showPrice();
		
		PhoneFactory pf = new PhoneFactory();
		// 匿名对象的使用
		pf.show(new Phone());
	}
}

class PhoneFactory {
	public void show(Phone phone) {
		phone.showPrice();
	}
}

class Phone {
	double price;
	
	public void showPrice() {
		System.out.println("价格为：" + price);
	}
}
```

### 1.6 方法的重载

```java
package com.atguigu.java1;

/*
 * 方法的重载（overload）loading...
 * 1. 定义：在同一个类中，允许存在一个以上的同名方法，只要它们的参数个数或者参数类型不同即可。
 *       “两同一不同”：同一个类，相同方法名；参数列表不同：参数个数不同，参数类型不同
 *               
 * 2. 举例
 *    Arrays类中重载的sort() / binarySearch()
 *    
 * 3. 判断是否是重载
 *    跟方法的权限修饰符，返回值类型，形参变量名，方法体没有关系。
 *    
 * 4. 在通过对象调用方法时，如何确定某一个指定的方法：
 *    方法名 -> 参数列表
 */
public class OverLoadTest {

	// 如下的4个方法构成了重载
	public void getSum(int i, int j) {
		System.out.println("1");
	}
	public void getSum(double d1, double d2) {
		System.out.println("2");
	}
	public void getSum(String s, int i) {
		System.out.println("3");
	}
	public void getSum(int i, String s) {
		System.out.println("4");
	}
	
	// 下面的都不算重载
//	public int getSum(int i, int j) {
//		
//	}
//	public void getSum(int m, int n) {
//		
//	}
//	private void getSum(int i, int j) {
//		
//	}
}

```

### 1.7 可变个数形参

```java
package com.atguigu.java1;

/*
 * 可变个数形参的方法
 *   1. jdk 5.0 新增的内容
 *   2. 具体使用：
 *     1） 可变个数形参的格式： 数据类型 ... 变量名
 *     2） 当调用可变个数形参的方法时，传入参数的个数可以是0，1，2...
 *     3） 可变个数形参的方法与本类中方法名相同，形参不同的方法之间构成重载
 *     4） 可变个数形参的方法与本类中方法名相同，形参类型也相同的数组之间不构成重载。换句话说，二者不能共存
 *     5） 可变个数形参在方法的形参中，必须声明在末尾
 *     6） 可变个数形参在方法的形参中，最多只能声明一个可变形参。
 */
public class MethodArgsTest {

	public static void main(String[] args) {
		
		MethodArgsTest test = new MethodArgsTest();
		test.show(12);//1
		test.show("hello");//2
		test.show("hello", "world");//3
		test.show();//3
		
		
	}
	public void show(int i) {
		System.out.println("1");
	}
	public void show(String s) {
		System.out.println("2");
	}
	public void show(String ... strs) {
		System.out.println("3");
	}
//	public void show(String[] strs) {
//		
//	}
}

```

### 1.8 方法的值传递机制

```java
package com.atguigu.java1;

/*
 * 方法的形参的值传递机制：值传递
 * 1. 形参：方法定义时，声明的小括号内的参数
 *    实参：方法调用时，实际传递给形参的数据
 * 
 * 2. 值传递机制：
 * 如果参数是基本数据类型，此时赋值的是变量所保存的数据值。
 * 如果参数是引用数据类型，此时赋值的是变量所保存的数据的地址值。
 */
public class ValueTransferTest1 {
	public static void main(String[] args) {
		
		int m = 10;
		int n = 20;
		// 交换两个变量的值
		int tmp = m;
		m = n;
		n = tmp;
		System.out.println("m = " + m + ", n = " + n);
		
		ValueTransferTest1 test = new ValueTransferTest1();
		m = 10;
		n = 20;
		test.swap(m,  n);
		System.out.println("m = " + m + ", n = " + n);

		System.out.println("**************************");
		Data data = new Data();
		data.m = 10;
		data.n = 20;
		test.swap(data);
		System.out.println("m = " + data.m + ", n = " + data.n);

	}
	
	public void swap(int m, int n) {
		int tmp = m;
		m = n;
		n = tmp;
	}
	public void swap(Data data) {
		int tmp = data.m;
		data.m = data.n;
		data.n = tmp;
	}
}

class Data {
	int m;
	int n;
}
```


## 2. 面向对象特征之一：封装与隐藏
为什么需要封装？封装的作用和含义？

* 我要用洗衣机，只需要按一下开关和洗涤模式就可以了。有必要了解洗衣机内部的结构吗？有必要碰电动机吗？
* 我要开车，...

我们程序设计追求“高内聚，低耦合”。

* 高内聚：类的内部数据操作细节自己完成，不允许外部干涉；
* 低耦合：仅对外暴露少量的方法用于使用。


```java
package com.atguigu.java;

/*
 * 面向对象的特征一：封装与隐藏
 * 一·问题的引入
 *   当我们创建一个类的对象以后，我们可以通过“对象.属性”的方式，对对象的属性进行赋值。这里赋值操作
 *   要受到属性的数据类型和存储范围的制约。除此之外，没有其他制约条件。但是，在实际问题中，我们往往需要
 *   给属性赋值加入额外的限制条件。这个条件就不能在属性声明时体现，我们只能通过方法进行限制条件的添加。
 *   （比如：set）同时，我们需要避免用户再使用“对象.属性”的方式对属性进行赋值。则需要将属性声明为私有的（private）
 *   ---> 此时，针对于属性就体现了封装性。
 *   
 * 二·封装性的体现：
 * 我们将类的属性私有化（private），同时，提供公共的（public）方法来获取（getXxx）和设置（setXxx）此属性的值
 * 
 * 拓展：封装性的体现：
 *      1） 如上
 *      2） 不对外暴露的私有的方法
 *      3） 单例模式
 *      
 * 三·封装性的体现，需要权限修饰符来配合。
 * 1. Java规定的4种权限：（从小到大）private, 缺省, protected, public
 * 2. 四种权限可以用来修饰类及类的内部结构：属性，方法，构造器，内部类
 * 3. 具体地， 四种权限都可以用来修饰类的内部结构：属性，方法，构造器，内部类
 *           修饰类的话，只能使用：缺省，public
 * 
 * 总结封装性：Java提供了4种权限修饰符来修饰类及类的内部结构，体现类及类的内部结构在被调用时的可见性大小
 */
public class AnimalTest {
	public static void main(String[] args) {
		Animal a = new Animal();
		a.name = "大黄";
		a.age = 1;
//		a.legs = 4;
		a.show();
		a.setLegs(4);
		a.show();
	}
}


class Animal {
	String name;
	int age;
	private int legs;
	
	public void eat() {
		System.out.println("动物进食");
	}
	
	public void show() {
		System.out.println("name = " + name + ", age = " + age + ", legs = " + legs);
	}
	
	public void setLegs(int l) {
		if (l >= 0 && l % 2 == 0) {
			legs = l;
		} else {
			legs = 0;
			// 抛出异常
		}
		legs = l;
	}
	
	public int getLegs() {
		return legs;
	}
}
```

|  修饰符 | 类内部  |  同一个包 | 不同包的子类  | 同一个工程  |
|---|---|---|---|---|
| private  |  yes |   |   |   |
| （缺省）  | yes  | yes  |   |   |
| protected  |  yes |  yes | yes  |   |
| public  | yes  | yes  | yes  | yes  |


## 3. 构造器

### 3.1 构造器的使用

```java
package com.atguigu.java1;

/*
 * 类的结构之三：构造器（或构造方法，constructor）的使用
 * 
 * 一·构造器的作用：
 * 1. 创建对象
 * 2. 初始化对象的的信息
 * 
 * 二·说明：
 * 1. 如果没有显式的定义类的构造器的话，则系统默认提供一个空参的构造器。
 * 2. 定义构造器的格式：权限修饰符 类名(形参列表){}
 * 3. 一个类中定义的多个构造器，彼此构成重载
 * 4. 一旦我们显式的定义了类的构造器之后，系统就不再提供默认的空参构造器。
 * 5. 一个类中，至少会有一个构造器。
 */
public class PersonTest {
	public static void main(String[] args) {
		// 创建类的对象: new + 构造器
		Person p = new Person();
		p.eat();
		Person p1 = new Person("Tom");
		System.out.println(p1.name);
	}
}

class Person {
	
	// 属性
	String name;
	int age;
	
	// 构造器
	public Person() {
		System.out.println("constructing object");
	}
	
	public Person(String n) {
		name = n;
	}
	
	// 方法
	public void eat() {
		System.out.println("人吃饭");
	}
}
```

### 3.2 属性赋值的先后顺序

```java
package com.atguigu.java1;

/*
 * 总结：属性赋值的先后顺序
 * 
 * 1. 默认初始化值
 * 2. 显式初始化
 * 3. 构造器中赋值
 * 4. 通过“对象.方法” 或 “对象.属性”的方式赋值
 * 
 * 以上操作的先后顺序：1 - 2 - 3 - 4
 * 
 */
public class UserTest {

}

class User {
	String name;
	int age = 1;
	
	public User() {
		
	}
	public User(int a) {
		age = a;
	}
}
```


### 3.3 JavaBean

```java
package com.atguigu.java1;

/*
 * JavaBean 是一种Java语言写成的可重用组建。
 * 所谓JavaBean，是指符合如下标准的Java类：
 *     1） 类是公共的
 *     2） 有一个无参的公共构造器
 *     3） 有属性，且有对应的get，set方法
 */
public class Customer {
	private int id;
	private String name;
	
	public Customer() {
		
	}
	
	public void setId(int i) {
		id = i;
	}
	public int getId() {
		return id;
	}
	public void setName(String n) {
		name = n;
	}
	public String getName() {
		return name;
	}
}

```

## 4 关键字

### 4.1 `this`关键字

```java
package com.atguigu.java2;

/*
 * this 关键字的使用
 * 1. this可以用来修饰,调用：属性，方法，构造器
 * 2. this修饰属性和方法：
 *     this理解为当前对象 或 当前正在创建的对象
 *     
 *     1） 在类的方法中，我们可以使用“this.属性”或“this.方法”的方式，调用当前对象的属性和方法。
 *     但是通常情况下，我们都选择省略“this.”。特殊情况下，如果方法的形参和类的属性同名时，我们必须显式
 *     的使用"this.变量"的方式，表明此变量是属性，而非形参。
 *     
 *     2） 在类的构造器中，我们可以使用“this.属性”或“this.方法”的方式，调用当前正在创建的对象属性和方法。
 *     但是通常情况下，我们都选择省略“this.”。特殊情况下，如果构造器的形参和类的属性同名时，我们必须显式
 *     的使用"this.变量"的方式，表明此变量是属性，而非形参。
 *     
 * 3. this调用构造器
 *     1）我们在类的构造器中，可以显式的使用“this(形参列表)”方式，调用本类中指定的其他构造器。
 *     2）构造器中不能通过“this(形参列表)”方式调用自己。
 *     3）如果一个类中有n个构造器，则最多有n-1构造器中使用了“this(形参列表)”
 *     4）规定：“this(形参列表)”必须声明在当前构造器的首行
 *     5）构造器内部，最多只能声明一个“this(形参列表)”，用来调用其他的构造器
 */
public class PersonTest {

	public static void main(String[] args) {
		Person p1 = new Person();
		p1.setAge(1);
		System.out.println(p1.getAge());
	}
}

class Person {
	private String name;
	private int age;
	
	public Person() {
		this.eat();
		String info = "Person初始化时，需要考虑如下的1，2，3，4，... (共40行代码)";
		System.out.println(info);
	}
	public Person(int age) {
		this();
		this.age = age;
	}
	public Person(String name) {
		this();
		this.name = name;
	}
	public Person(String name, int age) {
		this(age);
		this.name = name;
//		this.age = age;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getAge() {
		return age;
	}
	
	public void eat() {
		System.out.println("人吃饭");
		this.study();
	}
	public void study() {
		System.out.println("人学习");
	}
}
```

### 4.2 `package`和`import`关键字

```java
package com.atguigu.java2;

import java.util.Date;
import static java.lang.System.*;

/*
 * 一·package关键字的作用（类似班级的概念）
 * 1. 为了更好地实现项目中类的管理，提供包的概念。
 * 2. 使用package声明类或接口所属的包，声明在源文件的首行
 * 3. 包，属于标识符，遵循标识符的命名规则，规范(xxxyyyzzz)，“见名知意”
 * 4. 每“.”一次，就代表一层文件目录
 * 
 * 补充：同一个包下，不能命名同名的接口，类。
 *      不同的包下，可以命名同名的接口，类。
 * 
 * 二·import关键字的使用
 * 1. 在源文件中显式的使用import结构导入指定包下的类，接口。
 * 2. 声明在包的声明和类的声明之间
 * 3. 如果需要导入多个结构，则并列写出即可
 * 4. 可以使用“xxx.*”的方式，表示可以导入xxx包下的所有结构。
 * 5. 如果使用的类或接口是java.lang包下定义的，则可以省略import结构
 * 6. 如果使用的类或接口是本包下定义的，则可以省略import接口。
 * 7. 如果在源文件中，使用了不同包下的同名类，则必须至少有一个类需要以全类名的方式显式
 * 8. 使用“xxx.*”方式表明可以调用xxx包下的所有结构。但是如果使用的是xxx子包下的结构，则仍需要显式import
 * 9. import static: 导入指定类或接口[中]的静态结构：属性或方法。
 */
public class PackageImportTest {
	public static void main(String[] args) {
		Date date = new Date();
		// 全类名
		java.sql.Date date1 = new java.sql.Date(0);
		out.println("hello");
	}
}
```























