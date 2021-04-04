# Java零基础教程（二.基本语法）


## 1. 关键字

数据类型：`class`, `interface`, `enum`, `byte`, `short`, `int`, `long`, `float`, `double`, `char`, `boolean`, `void`

数值：`true`, `false`, `null`

流程控制：`if`, `else`, `switch`, `case`, `default`, `while`, `do`, `for`, `break`, `continue`, `return`

访问权限：`private`, `protected`, `public`

类，函数，变量的修饰符：`abstract`, `final`, `static`, `synchronized`

类与类关系：`extends`, `implements`

实例的创建，引用，判断：`new`, `this`, `super`, `instanceof`

异常处理：`try`, `catch`, `finally`, `throw`, `throws`

包：`package`, `import`

其他：`native`, `strictfp`, `transient`, `volatile`, `assert`

## 2. 标识符

标识符（aka变量方法类名）的命名规范：

* 包：都小写 `xxxyyy`
* 类,接口：`XxxYyy`
* 变量方法：`xxxYyy`
* 常量：`XXX_YYY_ZZZ`

## 3. 变量

### 3.1 数据类型

包含变量类型，变量名，和存储的值。变量都定义在其作用域内，在作用域内，它是有效的，除了作用域就失效了。

* 基本数据类型：整数类型`byte`（-128～127）, `short`（两字节-2^15～2^15-1）, `int`(四字节，-21亿～21亿), `long`（八字节，必须以小写或大写L结尾）； 浮点类型`float`（四字节，必须以f或F结尾，精确到7位有效数字，范围-3.403e38～3.403e38）, `double`（八字节，精度为float两倍，范围-1.798e308～1.798e308）;字符型`char`（两字节），布尔型`boolean`;
* 引用数据类型：类（例如String类），接口，数组

ASCII字符集：8bit表示

Unicode字符集：将世界上所有的符号都纳入其中。每一个符号都给予一个独一无二的编码，使用Unicode没有乱码的问题。`char c = \u0043`(字母C)。

UTF-8: 互联网上使用最广的一种Unicode的实现方式，采用变长编码方式，使用1～6个字节表示一个符号。

### 3.2 基本数据类型之间的转换

#### 自动类型提升

当容量小的数据类型的变量与容量大的数据类型的变量做运算时，结果自动提升为容量大的数据类型。

所表示数据范围（容量）排序：`byte`， `char`， `short` < `int` < `long` < `float` < `double`,注意这里是范围大小，而不是所占空间大小（float四字节小于long的八字节）。

特别的，当`byte`，`char`，`short`之间做运算时，结果为`int`型。

```java
// Example
short s1 = 10;
byte b1 = 10;
char c1 = 'a';
// char c = c1 + s1; // 编译报错
// char c = c1 + b1; // 编译报错
// short s = b1 + s1; // 编译报错
// short s = b1 + b1; // 编译报错
long l = 123213; // 成功，自动类型提升
// long l1 = 12313213123232; // 编译报错
long l1 = 12313213123232L; // 成功
//float f = 12.3; // 编译报错，double往float转了
// 整形常量默认为int型，浮点型常量默认为double型
```

#### 强制类型转换
是自动类型提升的逆操作，将范围大的值向范围小的变量转换

```java
// Example
double d = 12.3;
// int i = d; // 编译报错
int i = (int)d; // 成功，但截断损失精度
int i2 = 128;
byte b = (byte)i2; // 成功，但是b = -128 (二进制最高位的原因) 
```

### 3.3 String数据类型
String类型变量的使用：

1. String属于引用数据类型
2. 声明String类型变量时，使用一对“”
3. String可以和8种基本数据类型做运算，且运算只能是连接运算“+”
4. 运算的结果仍然是String类型。

```java
String s1 = "Hello World!";
// 练习：判断+是相加还是连接
char c = 'a';
int num = 10;
String str = "hello";
System.out.println(c + num + str); // 107hello
System.out.println(c + str + num); // ahello10
System.out.println(c + (num + str)); // a10hello
System.out.println((c + num) + str); // 107hello
System.out.println(str + num + c); // hello10a
```

```java
System.out.println("*    *");           // *    *
System.out.println('*' + '\t' + '*');   // 93, 因为是char之间的数值运算
System.out.println('*' + "\t" + '*');   // *    *
System.out.println('*' + '\t' + "*");   // 51*, 第一个相加执行的是数值运算
System.out.println('*' + ('\t' + "*")); // *    *
```

### 3.4 进制转换
整数的四种常见进制：二进制（0b或0B），十进制，八进制（以数字0开头表示），十六进制（0x或0X）

三码：

* 原码：直接将一个数值换成二进制数，最高位是符号位。
* 负数的反码：是对原码按位取反，只是最高位（符号位）确定为1。
* 负数的补码：其反码加1。
* 正数的原码，反码，补码相同。负数的补码是其反码+1。

```
00001110: 十进制14
10001110: -14的原码，第一位是符号位
11110001: -14的反码，除第一位外按位取反
11110010: -14的补码，计算机底层以补码形式存储数据。
```

```
求10111011储存的是什么数？
去补10111010
取反11000101
所以储存的是-(64+4+1) = -69

特别地，10000000储存的是-128
```

## 4. 运算符
### 4.1 算数运算符

加减乘除（/）取模（%），前自增，后自增，前自减，后自减，字符串连接（+）

```java
int num1 = 12;
int num2 = 5;
System.out.println(num1 / num2); // 2
System.out.println(num1 / num2 * num2); // 10
double res = num1 / num2;
System.out.println(res); // 2.0
System.out.println(num1 / (num2 + 0.0)); // 2.4
```

```java
System.out.println(12 % 5); // 2
System.out.println(-12 % 5); // -2
System.out.println(12 % -5); // 2
System.out.println(-12 % -5); // -2
// 取模的符号与被模数（12/-12）相同
```

```java
int a1 = 10;
int b1 = ++a1;

int a2 = 10;
int b2 = a2++;

//执行后，a1是11，b1是11，a2是11，b2是10
```

```java
//注意点
short s1 = 10;
// s1 = s1 + 1; // 编译失败，int值放入short变量
s1++; //编译通过，自增不改变本身变量数据类型
```

```java
byte b1 = 127;
b1++;
System.out.println(b1); // -128
```
### 4.2 赋值运算符
`=`， `+=`， `-=`， `*=`， `/=`， `%=`

```java
// option 1
int i1 = 10;
int j1 = 10;
// option 2
int i2, j2;
i2 = j2 = 10;
// option 3
int i3 = 10; j3 = 20;
```

```java
short s1 = 10;
// s1 = s1 + 2; // 编译失败
s1 += 2;
System.out.println(s1); // 12

// 所以开发中希望变量+2
// option1: num = num + 2;
// option2 (推荐): num += 2;

// 所以开发中希望变量+1
// option1: num = num + 1;
// option2: num += 1;
// option3 (推荐): num++;
```

```java
int n = 10;
n += (n++) + (++n);
System.out.println(n); // n = n + (n++) + (++n) = 10 + 10 + 12 = 32
```

### 4.3 比较运算符
`==`, `!=`, `<`, `>`, `<=`, `>=`, `instanceof`

### 4.4 逻辑运算符
`&`, `&&`, `|`, `||`, `!`（非）, `^`（异或）

### 4.5 位运算符
`<<`, `>>`, `>>>`, `&`, `|`, `^`（异或运算）, `~`（取反）

区分`逻辑&`和`位运算&`：`逻辑&`计算的是boolean型，`位运算&`计算的是数值。

总结

1. 位运算符操作的都是整形的数据
2. 在一定范围内，每向左移一位，相当于 `*2`
3. 在一定范围内，每向右移一位，相当于 `/2`

```java
// 交换两个变量的值
// option1: 推荐
int tmp = num1;
num1 = num2;
num2 = num1;

// option2: 可能超出存储范围
num1 = num1 + num2;
num2 = num1 - num2;
num1 = num1 - num2;

// option3: trick
num1 = num1 ^ num2;
num2 = num1 ^ num2;
num1 = num1 ^ num2;
```

### 4.6 三元运算符
`(condition) ? caseTrue : caseFalse;`

## 5. 流程控制

```java
import java.util.Scanner;
class IfTest {
	public static void main(String[] args) {
		Scanner scan = new Scanner(System.in);
		System.out.println("Input exam score: (0-100)");
		int score = scan.nextInt();

		if (score == 100) {
			System.out.println("win a BMW");
		} else if (score > 80) {
			System.out.println("win a iPhone");
		} else if (score >= 60) {
			System.out.println("win a iPad");
		} else {
			System.out.println("no prize");
		}
	}
}
```

总结：

1. 如果多个条件表达式之间互斥，哪个判断在上面在下面，无所谓。
2. 如果多个条件表达式之间有交集，需要考虑清楚谁在上。
3. 如果多个条件表达式之间有包含，通常情况下，范围小的在上面，否则范围小的可能没机会执行。

```java
int number = 2;
switch (number) {
case 0:
	System.out.println("zero");
	break;
case 1:
	System.out.println("one");
	break;
case 2:
	System.out.println("two");
	break;
default:
	System.out.println("other");
	break;
}
```

总结：

1. 根据switch的表达式中的值，依次匹配各个case的常量。一旦匹配成功，则进入相应的case结构中，调用其执行语句。当调用完执行语句以后，则依然继续向下执行其他case结构中的执行语句，直到遇到break关键字或此switch-case结构末尾结束为止。
2. break可以使用在switch-case结构中，表示一旦执行到此关键字，就跳出switch-case结构。
3. switch结构中的表达式，只能是如下的6种数据结构之一：byte，short，char，int，枚举类型（JDK5.0新增），String类型（JDK7.0新增）。
4. case之后只能声明常量，不能声明范围。
5. break关键字是可选的。
6. default类似于if-else结构中的else。default结构是可选的。
7. 如果switch-case结构中的多个case执行语句相同，则可以考虑合并。
8. 凡是可以使用switch-case结构的地方，都可以转换为if-else。反之不成立。
9. 我们写分支结构时，当发现既可以使用switch-case，（同时，switch中表达式的取值情况不太多），又可以使用if-else时，我们优先选择使用switch-case。原因：switch-case执行效率稍高。

```java
for (①；②；④) {
	③;
}
// 1-2-3-4-2-3-4-...-2

①;
while (②) {
	③;
	④;
}
// 1-2-3-4-2-3-4-...-2

do {
	③;
	④;
} while (②);
// 1-3-4-2-3-4-...-2
```

```java
class PrimeNumberTest {
	public static void main(String[] args) {
		boolean isFlag = true;
		int count = 0;

		// 获取当前时间距离1970-01-01 00:00:00 的毫秒数
		long start = System.currentTimeMillis();
		for (int i = 2; i <= 100000; i++) {
			for (int j = 2; j <= Math.sqrt(i); j++) {
				if (i % j == 0) {
					isFlag = false;
					break;
				}
			}
			if (isFlag == true) {
				count++;
			}
			isFlag = true;
		}
		long end = System.currentTimeMillis();
		System.out.println("prime number count: " + count);
		System.out.println("elapsed time: " + (end - start));
	}
}
```

|   | 使用范围  |  不同 |  相同 |
|---|---|---|---|
| break  |  switch-case，循环结构中 | 结束当前循环  |  关键字后面不能声明执行语句 |
| continue  | 循环结构中  | 结束当次循环  | 关键字后面不能声明执行语句  |

```java
label: for (int i = 2; i < 100000; i++) {
	for (int j = 2; j < Math.sqrt(i); j++) {
		if (i % j == 0) {
			// break; 默认跳出包裹此关键字的最近一层循环。
			// continue;
			// break label; //结束指定标识的一层循环结构
			// continue label; //结束指定标识的一层循环结构的当次循环
			continue label;
		}
		count++;
	}
}
```

循环语句的四个组成部分

1. 初始化部分
2. 循环条件部分
3. 循环体部分
4. 迭代部分

总结：

1. 写while循环千万小心不要丢了迭代条件。一旦丢了，就可能导致死循环。
2. 我们写程序，要避免出现死循环。
3. for循环和while循环是可以互相转化的。区别：for循环和while循环的初始化条件部分的作用范围不同。
4. do-while循环至少会执行一次循环体。

## 6. 数组

### 6.1 Array的概念与基础使用

1. 数组的理解： 数组（Array），是多个相同数据类型按照一定顺序排列的集合，并使用一个名字命名， 并通过编号的方式对这些数据进行统一管理。
2. 数据相关的概念：数组名，元素，下标（索引），数组的长度（元素的个数）
3. 数据的特点：
	* 数组是有序排列的。
	* 数组属于引用数据类型的变量，引用数据类型有数组，类，接口。String是一个类。数组的元素，既可以是基本数据类型，也可以是引用数据类型。
	* 创建数组对象会在内存中开辟一整块连续的空间。
	* 数组的长度一旦确定，就不能修改。
4. 数组的分类：
	* 按照维数：一维数组，二维数组。。。
	* 按照元素类型：基本数据类型元素的数组，引用数据类型元素的数组
5. 一维数组的使用
	* 一维数组的声明和初始化
	* 如何调用数组的指定位置的元素
	* 如何获取数组的长度
	* 如何遍历数组
	* 数组元素的默认初始化值
	* 数组的内存解析

```java
package com.atguigu.java;

import java.util.Iterator;

public class ArrayTest {
public static void main(String[] args) {
	// 1. 一维数组的声明和初始化
	int num; // 声明
	num = 10; // 初始化
	int id = 1001; //声明 + 初始化
	
	int[] ids; //声明
	// 1.1 静态初始化：数组的初始化和数组元素的赋值操作同时进行
	ids = new int[]{1001, 1002, 1003, 1004};
	// 1.2 动态初始化：数组的初始化和数组元素的赋值操作分开进行
	String[] names = new String[5];
	// 也是正确的写法（类型推断）
	int[] arr4 = {1,2,3,4,5};
	
	// 错误的写法：
	// int[] arr1 = new int[];
	// int[5] arr2 = new int[5];
	// int[] arr3 = new int[3]{1,2,3};
	
	// 总结：数组一旦初始化完成，其长度就确定了。
	
	// 2. 如何调用数组的指定位置的元素：通过角标的方式调用。
	// 数组的角标（或索引）从0开始，到数组的长度-1结束。
	names[0] = "aaa";
	names[1] = "bbb";
	names[2] = "ccc";
	names[3] = "ddd";
	names[4] = "eee";
	
	// 3. 如何获取数组的长度。
	// 属性：length
	System.out.println(names.length); // 5
	
	// 4. 如何遍历数组
	for (int i = 0; i < names.length; i++) {
		System.out.println(names[i]);
	}
}
}
```


```java
package com.atguigu.java;

public class ArrayTest1 {
public static void main(String[] args) {
//	5. 数组元素的默认初始化值
//	       > 数组元素是整型：0
//	       > 数组元素是浮点型：0.0
//	       > 数组元素是char型：ASCII0的char或'\u0000'(null)
//	       > 数组元素是boolean型：false
//	       > 数组元素是引用数据类型：null，String时也是null，而不是空字符串
//	
	
	int[] arr = new int[4];
	for (int i = 0; i < arr.length; i++) {
		System.out.println(arr[i]);
	}
	System.out.println("************");
	float[] arr1 = new float[4];
	for (int i = 0; i < arr1.length; i++) {
		System.out.println(arr1[i]);
	}
	System.out.println("************");
	char[] arr2 = new char[4];
	for (int i = 0; i < arr2.length; i++) {
		System.out.println("----" + arr2[i] + "====");
	}
	System.out.println("************");
	boolean[] arr3 = new boolean[4];
	for (int i = 0; i < arr3.length; i++) {
		System.out.println("----" + arr3[i] + "====");
	}
}
}
```


```java
package com.atguigu.java;

public class ArrayTest2 {
public static void main(String[] args) {
	// 1. 二维数组的声明和初始化
	// 静态初始化
	int[] arr = new int[]{1,2,3}; // 一维数组
	int[][] arr1 = new int[][]{{1,2,3},{4,5},{6,7,8}}; // 二维数组
	// 动态初始化1
	String[][] arr2 = new String[3][2];
	// 动态初始化2
	String[][] arr3 = new String[3][];
	// 错误的情况
	// String[][] arr4 = new String[][4];
	// String[4][3] arr5 = new String[][];
	// int[][] arr6 = new int[4][3]{{1,2,3},{4,5,{6,7,8}}};
	// 正确：
	int[] arr4[] = new int[][]{{1,2,3},{4,5},{6,7,8}};
}
}
```

内存解析：

1. 栈（stack）：局部变量
2. 堆（heap）：new出来的结构，对象，数组
3. 方法区：常量池，静态域

### 6.2 Arrays工具类的使用

```java
package com.atguigu.java;

import java.util.Arrays;

// java.util.Arrays: 操作数组的工具类，包含了用来操作数组的方法
public class ArraysTest {
public static void main(String[] args) {
	// 1. 判断两个数组是否相等。
	int[] arr1 = new int[]{1,2,3,4};
	int[] arr2 = new int[]{1,3,2,4};
	boolean isEquals = Arrays.equals(arr1,  arr2);
	System.out.println(isEquals);
	
	// 2. 输出数组信息
	System.out.println(Arrays.toString(arr1));
	
	// 3. 将指定值填充到数组之中
	Arrays.fill(arr1,  10);
	System.out.println(Arrays.toString(arr1));
	
	// 4. 对数组排序
	Arrays.sort(arr2);
	System.out.println(Arrays.toString(arr2));
	
	// 5. 二分查找
	int[] arr3 = new int[] {-98, -34, 2, 34, 54, 66, 79, 105, 210, 333};
	int index = Arrays.binarySearch(arr3, 211);
	System.out.println(index);
}
}
```















