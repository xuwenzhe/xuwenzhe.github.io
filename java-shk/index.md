# Java零基础教程（一.概论）


## 1. 硬件介绍
### 1.1 CPU（中央处理器）
冯诺伊曼体系结构：冯诺伊曼被人们称为“计算机之父”。计算机由输入设备，输出设备，存储器，运算器，控制器构成。现代计算机中的CPU负责其中的运算与控制功能。

赫兹：现代计算机的计算速度以千兆赫（GHz）来表述。英文进制：KHz，MHz，GHz

Intel i7-6700HQ：“6”第六代，HQ标压版本，U低压版本

摩尔定律：每一美元所能买到的电脑性能，每隔18-24个月翻一倍以上。

### 1.2 存储
#### 硬盘
硬盘存储的数据断电不丢失（持久化设备）。在windows系统中，A/B盘的位置是预留给软驱的（软驱已经淘汰）。
#### 内存
Read-Access Memory，内存存储数据速度比硬盘快10倍以上，其部分解决了CPU运算过快，而硬盘数据存取太慢的问题。断电会导致数据丢失

bit：二进制表示0/1

byte：8 bits，内存中每个byte拥有唯一的地址。

进制：1KB = 1024B, 1MB = 1024KB, 1GB = 1024MB, 1T = 1024GB, 1PB = 1024TB, EB,ZB,YB,以此类推。

ROM：手机的“硬盘”，例如16GB，128GB。但是经常被大众（误）叫做手机内存。

### 1.3 输入输出
输入设备： 鼠标键盘； 输出设备： 显示器打印机

1920x1080：像素矩阵

2K屏幕：比如2244x1080（2244>2000）

屏幕尺寸：屏幕对角线长度

像素密度：sqrt(长度像素数^2 + 宽度像素数^2) / 屏幕尺寸

### 1.4 通信设备

## 2. 计算机发展史
图灵：计算机之父，人工智能之父

冯诺伊曼：计算机之父，博弈论之父

www（万维网）：简称web。www可以让Web客户端（如浏览器）访问浏览Web服务器上的页面。是一个由许多互相连接的超文本组成的系统，通过互联网访问。在这个系统中，每个有用的事物，称为一样资源，并且由一个全局统一资源标识符（URI）标识；这些资源通过超文本传输协议HTTP（Hypertext Transfer Protocol）传送给用户，用户通过点击链接来获得资源。

万维网vs因特网vs互联网：他们是包含关系。万维网是无数个网络站点和网页集合。因特网除了万维网，还包括电子邮件等。

BS架构，CS架构：browser server（例如百度搜索）； client server（例如QQ）

## 3. Java语言概述
### 3.1 编程语言
机器语言 -> 汇编语言 -> 高级语言（面向过程）-> 高级语言（面向对象）

执行效率越来越低，但开发效率越来越高。

TIOBE语言热度排行榜

### 3.2 Java语言的历史

Java由SUN（Stanford University Network）于1995年推出的编程语言。

* 1991 - Green项目，最初命名Oak（橡树）
* 1994 - 开发组意识到Oak适合互联网（可以在Web浏览器中运行，使得用户可以使用图形界面进行交互）
* 1996 - 发布JDK1.0
* 2004 - 发布JDK1.5，因为具有里程碑特性，更名为JDK5.0
* 2005 - J2SE改名为JavaSE，J2EE改名为JavaEE，J2ME改名为JavaME
* 2009 - Oracle收购SUN
* 2014 - 发布JDK8.0，是继JDK5.0以来变化最大的版本，也是业界常用的版本。

其他常用于后台开发的语言：Java，PhP，Python，Go，NodeJS

JavaSE：桌面应用（例如QQ）

JavaEE：企业互联网后台开发

JavaME：移动端开发

现在JavaSE不常开发桌面应用了，之前SE与EE是并列关系，但是这里我们把SE当作EE的基础知识。

Java应用领域：

1. 企业级应用
2. Android平台应用
3. 大数据平台开发

### 3.3 Java语言特性
特性：

1. 面向对象（封装，继承，多态）
2. 健壮性（去掉指针，提供了内存管理机制，但Java程序也会出现内存泄漏和内存溢出的问题！）
3. 跨平台性（Write Once, Run Anywhere）只需要安装一个Java虚拟机（JVM），JVM有不同操作系统对应的版本Win，Linux，Mac等。

### 3.4 环境搭建
常见简称：

* JDK：（Java Development Kit）
* JRE：（Java Runtime Environment）运行环境
* JDK = JRE + 开发工具集（例如Javac编译工具等）
* JRE = JVM + JavaSE标准类库

`jdk-8u131`: JDK8.0，“u”-update，131补丁

Steps：

1. 安装JDK8.0（不建议安装在系统C盘下，下一步。。。下一步）
2. 安装JRE（理论上JDK安装包里包含JRE，但是Eclipse需要识别单独的JRE）
3. 配置环境变量（使得`javac`等命令可以在任意目录执行）

`path`环境变量：Windows操作系统执行命令时，索要搜寻的路径

Administrator的用户变量适用于当前登陆用户，而系统变量则适用于所有用户。

## 4. 第一个程序`HelloWorld`

程序执行的流程：`javac`编译生成`.class`文件，`java`运行`.class`文件。

```bash
$ javac HelloWorld.java # 生成 HelloChina.class
$ java HelloChina
```

```java
class HelloChina {
	public static void main(String[] args) {
		System.out.println("Hello, World!");
	}
}
```

总结：

1. 一个java文件中可以声明多个class， 但只能最多有一个类声明为public，而且public只能加在与文件名相同的类前面。
2. 程序的入口是main方法，格式是固定的。
3. 程序行以分号结尾。
4. 编译以后，会生成一个或多个class文件，文件名与源文件中类名相同。
5. 单行注释`//`, 多行注释`/**/`, javadoc文档注释`javadoc -d myHello -author -version`

## 5. Eclipse使用
先创建包（例如：`com.atguigu.contact`）,再创建类（例如`HelloWorld.java`）。
运行，右键`Run As`

快捷键

```java
package com.atguigu.java;

import java.util.ArrayList;

/*
 * Eclipse中的快捷键
 * 1. 补全代码的声明: alt + /; ctrl + space
 * 2. 快速修复: ctrl + 1; cmd + 1
 * 3. 批量导包: ctrl + shift + o; cmd + shift + o
 * 4. 单行注释: ctrl + /; cmd + /
 * 5. 多行注释: ctrl + shift + /; ?
 * 6. 取消多行注释: ctrl + shift + \; ?
 * 7. 复制指定行的代码: ctrl + alt + down/up; cmd + alt + down/up
 * 8. 删除指定行: ctrl + d; cmd + d
 * 9. 上下移动行: alt + up/down; alt + up/down
 * 10. 切换到下一行代码空位: shift + enter; shift + enter
 * 11. 切换到上一行代码空位: ctrl + shift + enter; cmd + shift + enter
 * 12. 如何查看源码: ctrl + 选中指定结构; cmd + 选中指定结构
 *     如何搜索源码: ctrl + shift + t; cmd + shift + t
 * 13. 退回到前一个编辑的页面: alt + left; 
 * 14. 进入到下一个编辑的页面: alt + right;
 * 15. 光标选中指定的类，查看继承树结构: ctrl + t; cmd + t
 * 16. 复制代码: ctrl + c; cmd + c
 * 17. 撤销: ctrl + z; cmd + z
 * 18. 反撤销: ctrl + y; cmd + y
 * 19. 剪切: ctrl + x; cmd + x
 * 20. 粘贴: ctrl + v; cmd + v
 * 21. 保存: ctrl + s; cmd + s
 * 22. 全选: ctrl + a; cmd + a
 * 23. 格式化代码: ctrl + shift + f; cmd + shift + f
 * 24. 选中数行，整体往后移动: tab; tab
 * 25. 选中数行，整体往前移动: shift + tab; shift + tab
 * 26. 在当前类中，现实类结构，并支持搜索指定的方法，属性等: ctrl + o; cmd + o
 * 27. 批量修改指定的变量名，方法名，类名等: alt + shift + r; ?
 * 28. 选中的结构大小写切换，变成大写: ctrl + shift + x; cmd + shift + x
 * 29. 选中的结构大小写切换，变成小写: ctrl + shift + y; cmd + shift + y
 * 30. 调出生成getter/setter/构造器结构: alt + shift + s; ?
 * 31. 显式当前选择资源（工程或文件）的属性: alt + enter; cmd + enter
 * 
 * 
 * 32. 快速查找， 参照选中的word快速定位到下一个: ctrl + k; cmd + k
 * 33. 关闭当前窗口: ctrl + w
 * 34. 关闭所有窗口: ctrl + shift + w
 * 35. 查看指定的结构使用过的地方: ctrl + alt + g; cmd + alt + g
 * 36. 查找和替换: ctrl + f; cmd + f
 * 37. 最大化当前的view: ctrl + m
 * 38. 直接定位到当前行首位: home
 * 39. 直接定位到当前行末尾：end
 * 
 * 
 */
public class EclipseKeys {
	
	final double PROJECT_ACCCOUNT_ID = 3.14;
	public static void main(String[] args) {

		ArrayList list = new ArrayList();

		int n = 1;
		n = 2;
		n = 3;
	}
}

class User {
	private int id;
	private String name;
	
}
```

添加注释模版
Window -> Preferences -> Java -> Code Style -> Code Templates

























