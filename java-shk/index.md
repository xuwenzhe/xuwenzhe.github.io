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

1. 补全代码的声明：`alt + /`
2. 快速修复：`ctrl + 1`
3. 单行注释：`ctrl + /`
4. 多行注释：`ctrl + shift + /`
5. 取消多行注释：`ctrl + shift + \`
6. 复制指定行的代码：`command + option + down`或`command + option + up`
7. 删除指定行的代码：`command + d`
8. 上下移动代码：`option + up`或`option + down` 
9. 切换到下一行代码空位：`shift + enter`
10. 切换到上一行代码空位：`ctrl + shift + enter`
11. 查看原码：`command + 选中`
12. 退回到前一个编辑的页面：`alt + left`
13. 自动补全main，sysout：`ctrl + space`
14. 使用定义好的注释模版: `/** + Enter`

添加注释模版
Window -> Preferences -> Java -> Code Style -> Code Templates

























