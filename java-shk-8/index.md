# Java零基础教程（八.常用类）


## 1. 字符串相关的类

### 1.1 String的使用

String：字符串，使用一对`""`引起来表示。

* String声明为final的，不可被继承
* String实现了Serializable接口：表示字符串是支持序列化的。实现了Comparable接口：表示String可以比较大小。
* String内部定义了`final char[] value`用于存储字符串数据
* String：代表不可变的字符序列。简称：不可变性。
    体现：
    1. 当对字符串重新赋值时，需要重写指定内存区赋值，不能使用原有的value进行赋值。
    2. 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值（因为value是final的）。
    3. 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
* 通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在方法区内的字符串**常量池**中。
* 字符串常量池中是不会存储相同内容的字符串的。

结论：
* 常量与常量的拼接结果在常量池中。且常量池中不会存在相同内容的常量。
* 只要其中有一个是变量，结果就在堆中
* 如果拼接的结果调用intern()方法，返回值就在常量池中。


String的实例化方式：

* 方式一：通过字面量定义的方式
* 方式二：通过new + 构造器的方式

面试题：`String s = new String("abc");`方式创建对象，在内存中创建了几个对象？

两个：一个是堆空间中new结构，另一个是char[]对应的常量池中的数据："abc"

```java
package com.atguigu.java;

import org.junit.Test;

public class StringTest {
    @Test
    public void test3() {
        String s1 = "javaEE";
        String s2 = "hadoop";

        String s3 = "javaEEhadoop";
        String s4 = "javaEE" + "hadoop";
        String s5 = s1 + "hadoop";
        String s6 = "javaEE" + s2;
        String s7 = s1 + s2;

        System.out.println(s3 == s4);// true
        System.out.println(s3 == s5);// false
        System.out.println(s3 == s6);// false
        System.out.println(s3 == s7);// false
        System.out.println(s5 == s6);// false
        System.out.println(s5 == s7);// false
        System.out.println(s6 == s7);// false

        String s8 = s5.intern();// 返回值得到的s8使用的常量值中已经存在的"javaEEhadoop"
        System.out.println(s3 == s8);
    }
    @Test
    public void test1() {
        String s1 = "abc"; // 字面量的定义方式
        String s2 = "abc";
//        s1 = "hello";
//        System.out.println(s1); // hello
//        System.out.println(s2); // abc
        System.out.println(s1 == s2); // true
        String s3 = "abc";
        s3 += "def";
        System.out.println(s3);// abcdef
        System.out.println(s2);// abc
        String s4 = "abc";
        s4 = s4.replace('a','m');
        System.out.println(s4);
    }

    @Test
    public void test2() {
        // 通过字面量定义的方式：此时的s1和s2的数据javaEE声明在方法区中的字符串常量池中。
        String s1 = "javaEE";
        String s2 = "javaEE";
        // 通过new + 构造器的方式：此时的s3和s4保存的地址值，是数据在堆空间中开辟空间以后对应的地址值。
        String s3 = new String("javaEE");
        String s4 = new String("javaEE");
        // 解释：s1和s2指向字符串常量池中javaEE内存地址，
        // s3和s4分别指向堆中各创建的对象，两个对象里面各自的value变量都指向了之前的常量池中javaEE内存地址。
        System.out.println(s1 == s2);// true
        System.out.println(s1 == s3);// false
        System.out.println(s1 == s4);// false
        System.out.println(s3 == s4);// false
    }
}
```

再来看一个String值不可变性的例子：

```java
package com.atguigu.exer;

public class StringTest {

    String str = new String("good");
    char[] ch = {'t', 'e', 's', 't'};

    public void change(String str, char ch[]) {
        str = "test ok"; // 如果改为this.str，str就变为test ok了。
        ch[0] = 'b';
    }
    public static void main(String[] args) {
        StringTest ex = new StringTest();
        ex.change(ex.str, ex.ch);
        System.out.println(ex.str);// good
        System.out.println(ex.ch);// best
    }
}

```

### 1.2 String常用方法

String常用方法
* `int Length()`: 返回字符串的长度： `return value.length`
* `char charAt(int index)`: 返回某索引处的字符 `return value[index]`
* `boolean isEmpty()`: 判断是否是空字符串：`return value.length == 0`
* `String toLowerCase()`: 使用默认语言环境，将String中的所有字符转换为小写
* `String toUpperCase()`: 使用默认语言环境，将String中的所有字符转换为大写
* `String trim()`: 返回字符串的副本，忽略前导空白和尾部空白
* `boolean equals(Object obj)`: 比较字符串的内容是否相同
* `boolean equalsIgnoreCase(String anotherString)`: 与equals方法类似，忽略大小写
* `String concat(String str)`: 将指定字符串连接到此字符串的结尾。等价于用"+"
* `int compareTo(String anotherString)`: 比较两个字符串的大小
* `String substring(int beginIndex)`: 返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。
* `String substring(int beginIndex, int endIndex)`: 返回一个新字符串，它是此字符串从beginIndex开始截取到endIndex(不包含)的一个子字符串。
* `boolean endsWith(String suffix)`: 测试此字符串是否以指定的后缀结束
* `boolean startsWith(String prefix)`: 测试此字符串是否以指定的前缀开始
* `boolean startsWith(String prefix, int toffset)`:测试此字符串从指定索引开始的子字符串是否以指定的前缀开始
* `int indexOf(String str)`: 返回指定子字符串在此字符串中第一次出现处的索引
* `int indexOf(String str, int fromIndex)`: 返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始找
* `int lastIndexOf(String str)`: 返回指定子字符串在此字符串中最右边出现处的索引
* `int lastIndexOf(String str, int fromIndex)`: 返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索

注：indexOf和lastIndexOf方法如果未找到都是返回-1

* `String replace(char oldChar, char newChar)`: 返回一个新的字符串，它是通过newChar替换此字符串中出现的所有oldChar得到的。
* `String replace(CharSequence target, CharSequence replacement)`:使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。
* `String replaceAll(String regex, String replacement)`:使用给定的replacement替换此字符串所有匹配给定的正则表达式的子字符串。
* `String replaceFirst(String regex, String replacement)`:使用给定的replacement替换此字符串匹配给定的正则表达式的第一个子字符串。
* `boolean matches(String regex)`:告知此字符串是否匹配给定的正则表达式。
* `String[] split(String regex)`: 根据给定正则表达式的匹配拆分此字符串
* `String[] split(String regex, int limit)`: 根据匹配给定的正则表达式来拆分此字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中。


```java
package com.atguigu.java;

import org.junit.Test;

public class StringMethodTest {
    @Test
    public void test4() {
        String str1 = "北京尚硅谷教育北京";
        System.out.println(str1.replace('北','东'));
        System.out.println(str1.replace("北京", "上海"));
    }
    @Test
    public void test3() {
        String str1 = "helloworld";
        System.out.println(str1.endsWith("ld"));// true
        System.out.println(str1.startsWith("He"));// false
        System.out.println(str1.startsWith("ll",2));// true

        System.out.println(str1.contains("wo"));// true
        System.out.println(str1.indexOf("lo"));//3
        System.out.println(str1.indexOf("lo", 5));//-1
        System.out.println("hellorworld".lastIndexOf("or"));//7
        System.out.println("hellorworld".lastIndexOf("or",6));//4

        // 什么情况下，indexOf(str)和lastIndexOf(str)返回值相同
        // 仅出现一次，或没出现
    }
    @Test
    public void test2() {
        String s1 = "HelloWorld";
        String s2 = "helloworld";
        System.out.println(s1.equals(s2));
        System.out.println(s1.equalsIgnoreCase(s2));

        String s3 = "abc";
        String s4 = s3.concat("def");
        System.out.println(s4);

        String s5 = "abc";
        String s6 = new String("abe");
        System.out.println(s5.compareTo(s6));//-2,涉及到字符串排序

        String s7 = "北京尚硅谷教育";
        System.out.println(s7.substring(2,5));// 尚硅谷
    }
    @Test
    public void test1() {
        String s1 = "HelloWorld";
        System.out.println(s1.length());
        System.out.println(s1.charAt(0));
        System.out.println(s1.charAt(9));
//        s1 = "";
        System.out.println(s1.isEmpty());

        String s2 = s1.toLowerCase();
        System.out.println(s1);//s1不可变的，仍然为原来的字符串
        System.out.println(s2);

        String s3 = "  he llo wo rld  ";
        String s4 = s3.trim();
        System.out.println(s4);

    }
}
```

### 1.3 String与其他数据类型的转换

涉及到String类与其他结构之间的转换

String与基本数据类型，包装类之间的转换（复习）:

* String -> 基本数据类型，包装类：调用包装类的静态方法：parseXxx(str)
* 基本数据类型，包装类 -> String：调用String重载的valueOf(xxx)

String与char[]之间的转换：

* String -> char[]: 调用String的toCharArray()
* char[] -> String: 调用String的构造器

String与byte[]之间的转换：

* 编码：String -> byte[]:调用String的getBytes()
* 解码：bytes[] -> String: 调用String的构造器

关于编码解码的说明：

* 编码：字符串 -> 字节 （看得懂 -> 看不懂的二进制数据）
* 解码：编码的逆过程，字节 -> 字符串（看不懂的二进制数据 -> 看得懂）

注意：解码时，要求解码使用的字符集必须与编码时使用的字符集一致，否则会出现乱码。

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.UnsupportedEncodingException;
import java.util.Arrays;

/*

 */
public class StringTest1 {
    @Test
    public void test4() {
        String s1 = "javaEEhadoop";
        String s2 = "javaEE";
        String s3 = s2 + "hadoop";
        System.out.println(s1 == s3);// false
        final String s4 = "javaEE";// 这里s4是一个常量
        String s5 = s4 + "hadoop";
        System.out.println(s1 == s5);// true
    }
    @Test
    public void test3() throws UnsupportedEncodingException {
        String str1 = "abc123中国";
        byte[] bytes = str1.getBytes();// 使用默认的字符集，utf-8一个汉字对应3bytes，gbk字符集一个汉字对应2bytes
        System.out.println(Arrays.toString(bytes));
        // [97, 98, 99, 49, 50, 51, -28, -72, -83, -27, -101, -67]

        System.out.println(new String(bytes));// 使用默认的字符集，进行解码
        byte[] gbks = str1.getBytes("gbk");
        System.out.println(new String(gbks));// 出现乱码。原因：编码集和解码集不一致
        System.out.println(new String(gbks, "gbk"));
    }
    @Test
    public void test2() {
        String str1 = "abc123";
        char[] charArray = str1.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            System.out.println(charArray[i]);
        }

        char[] arr = new char[]{'h','e','l','l','o'};
        System.out.println(new String(arr));
    }
    @Test
    public void test1() {
        String str1 = "123";
        int num = Integer.parseInt(str1);
        System.out.println(String.valueOf(num));
        String str2 = num + "";
        System.out.println(str1 == str2);//str1在常量池中，str2在堆中
    }
}
```

### 1.4 String, StringBuilder, StringBuffer

关于StringBuffer和StringBuilder的使用

String，StringBuffer，StringBuilder三者的异同：

* String：不可变的字符序列；底层使用char[]存储
* StringBuffer：可变的字符序列：线程安全的，效率低；底层使用char[]存储
* StringBuilder：可变的字符序列：jdk5.0新增的，线程不安全的，效率高；底层使用char[]存储

源码分析：

	String str = new String();// new char[0];
	String str1 = new String("abc");// new char[]{'a','b','c'};

	StringBuffer sb1 = new StringBuffer();// new char[16];底层创建了一个长度是16的数组。
	sb1.append('a');// value[0] = 'a';
	sb1.append('b');// value[1] = 'b';
	StringBuffer sb2 = new StringBuffer("abc");// char[] value = new char[""abc".length() + 16]

问题1. System.out.println(sb2.length());

问题2. 扩容问题：如果要添加的数据底层数组盛不下了，那就需要扩容底层数组。默认情况下，扩容为原来容量的2倍 + 2， 同时将原有数组中的元素复制到新的数组中。

指导意义：开发中建议大家使用：StringBuffer(int capacity) 或 StringBuilder(int capacity)

StringBuffer类的常用方法

* `StringBuffer append(xxx)`: 提供了很多的append()方法，用于进行字符串拼接
* `StringBuffer delete(int start, int end)`: 删除指定位置的内容
* `StringBuffer replace(int start, int end, string str)`: 把[start,end)位置替换为str
* `StringBuffer insert(int offset, xxx)`: 在指定位置插入xxx
* `StringBuffer reverse()`: 把当前字符序列逆转
* `public int indexOf(String str)`
* `public String substring(int start, int end)`
* `public int length()`
* `public char charAt(int n)`
* `public void setCharAt(int n, char ch)`

总结：

* 增：append(xxx)
* 删：delete(int start, int end)
* 改：setCharAt(int n, char ch) / replace(int start, int end, string str)
* 查：charAt(int n)
* 插：insert(int offset, xxx)
* 长度：length()
* 遍历：for() + charAt() / toString()

对比String, StringBuffer, StringBuilder三者效率：
从高到低排序：StringBuilder > StringBuffer > String

```java
package com.atguigu.java;

import org.junit.Test;

public class StringBufferBuilderTest {
    @Test
    public void test1() {
        StringBuffer sb1 = new StringBuffer("abc");
        sb1.setCharAt(0,'m');
        System.out.println(sb1); // mbc

        StringBuffer sb2 = new StringBuffer();
        System.out.println(sb2.length());//0
    }

    @Test
    public void test2() {
        StringBuffer s1 = new StringBuffer("abc");
        s1.append(1);
        s1.append('1');
        System.out.println(s1);
        s1.delete(2,4);
        System.out.println(s1);//ab1

        s1.replace(2,4,"hello");
        s1.insert(2, false);
        System.out.println(s1);// abfalsehello
        s1.reverse();
        System.out.println(s1);// olleheslafba
        String s2 = s1.substring(1,3);
    }

    @Test
    public void test3() {
        long startTime = 0L;
        long endTime = 0L;
        String text = "";
        StringBuffer buffer = new StringBuffer("");
        StringBuilder builder = new StringBuilder("");

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            buffer.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuffer的执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            builder.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuilder的执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            text = text + i;
        }
        endTime = System.currentTimeMillis();
        System.out.println("String的执行时间：" + (endTime - startTime));
    }
}
```

## 2. JDK 8之前的日期时间API (Optional)

JDK 8之前日期和时间的API测试

	java.util.Date类
	      ｜ --- java.sql.Date类

* 两个构造器的使用
   1. 构造器一：Date():创建一个对应当前时间的Date对象
   2. 构造器二：创建指定毫秒数的Date对象
* 两个方法的使用
   1. toString(): 显示当前的年月日时分秒
   2. getTime(): 获取当前Date对象对应的毫秒数(时间戳)

* java.sql.Date对应着数据库中的日期类型的变量
   1. 如何实例化
   2. 如何将java.util.Date对象转换为java.sql.Date对象


```java
package com.atguigu.java;

import org.junit.Test;

import java.util.Date;

public class DateTimeTest {
    @Test
    public void test1() {
        long time = System.currentTimeMillis();
        //返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差
        // 称为时间戳
        System.out.println(time);
    }
    @Test
    public void test2() {
        // 构造器一：Date(): 创建一个对应当前时间的Date对象
        Date date1 = new Date();
        System.out.println(date1.toString());//Sat Apr 24 11:34:27 PDT 2021
        System.out.println(date1.getTime());//1619289267341
        // 构造器二：Date
        Date date2 = new Date(1619289267341L);
        System.out.println(date2.toString());

        java.sql.Date date3 = new java.sql.Date(1619289267341L);
        System.out.println(date3);//2021-04-24

        // 如何将java.util.Date对象转换为java.sql.Date对象
        // 情况一：向下转型
        Date date4 = new java.sql.Date(1619289267341L);
        java.sql.Date date5 = (java.sql.Date) date4;
        // 情况二：
        Date date6 = new Date();
//        java.sql.Date date7 = (java.sql.Date) date6;// 报错ClassCastException
        java.sql.Date date7 = new java.sql.Date(date6.getTime());
    }
}

```

jdk 8之前的日期时间API测试

* System类中currentTimeMillis();
* java.util.Date和子类java.sql.Date
* SimpleDateFormat
* Calendar

注意：
* 获取月份时：一月是0，二月是1，以此类推，12月是11
* 获取星期时：周日是1，周二是2，以此类推，周六是7

```java
package com.atguigu.java;

import org.junit.Test;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class DateTimeTest {
    /*
    SimpleDateFormat的使用：SimpleDateFormat对日期Date类的格式化和解析
    1. 两个操作：
    1.1 格式化：日期 ---> 字符串
    1.2 解析：格式化的逆过程，字符串 ---> 日期

    2. SimpleDateFormat的实例化
     */

    @Test
    public void testSimpleDateFormat() throws ParseException {
        //实例化SimpleDateFormat
        SimpleDateFormat sdf = new SimpleDateFormat();

        //格式化：日期 ---> 字符串
        Date date = new Date();
        System.out.println(date);

        String format = sdf.format(date);
        System.out.println(format);

        //解析：格式化的逆过程，字符串 ---> 日期
        String str = "4/24/21, 11:54 PM";
        Date date1 = sdf.parse(str);
        System.out.println(date1);

        System.out.println("=========按照指定的方式格式化和解析：调用带参的构造器=========");
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        System.out.println(sdf1.format(date));
        // 解析：要求字符串必须是符合SimpleDateFormat识别的格式（通过构造器参数体现）
        // 否则抛异常
        System.out.println(sdf1.parse("2021-04-24 11:57:20"));
    }
    @Test
    public void testExer() throws ParseException {
        // 练习一：字符串转换为java.sql.Date
        String birth = "2020-09-28";
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        Date date = sdf.parse(birth);
        java.sql.Date birthDate = new java.sql.Date(date.getTime());
        System.out.println(birthDate);
    }

    /*
    Calendar日历类的使用
     */
    @Test
    public void testCalendar() {
        // 1. 实例化
        // 方式一：创建其子类（GregorianCalendar）的对象
        // 方式二：调用其静态方法getInstance()
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar.getClass());

        // 2. 常用方法
        // get()
        int days = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(days);//25
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));//115
        // set()
        calendar.set(Calendar.DAY_OF_MONTH, 22);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));//22
        // add()
        calendar.add(Calendar.DAY_OF_MONTH, -3);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));//19
        // getTime(): 日历类--->Date
        Date date = calendar.getTime();
        System.out.println(date);//Mon Apr 19 16:16:17 PDT 2021
        // setTime(): Date--->日历类
        Date date1 = new Date();
        calendar.setTime(date1);
        System.out.println(calendar.getTime());//Sun Apr 25 16:16:17 PDT 2021
    }
}
```


## 3. JDK 8中新日期时间API

LocalDate, LocalTime, LocalDateTime的使用：LocalDateTime相较于LocalDate和LocalTime使用频率要高，类似于Calendar。

Instant的使用。类似于java.util.Date类。

DateTimeFormatter:格式化或解析日期，时间。类似于SimpleDateFormat。

```java
package com.atguigu.java;

import org.junit.Test;

import java.time.*;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.time.temporal.TemporalAccessor;

public class JDK8DateTimeTest {

    @Test
    public void test1() {
        // now(): 获取当前的日期，时间，日期+时间
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();

        System.out.println(localDate);
        System.out.println(localTime);
        System.out.println(localDateTime);

        // of():设置指定的年月日时分秒。没有偏移量
        LocalDateTime localDateTime1 = LocalDateTime.of(2020,10,6,13,23,43);
        System.out.println(localDateTime1);

        //getXxx()
        System.out.println(localDateTime.getDayOfMonth());//25
        System.out.println(localDateTime.getDayOfWeek());//SUNDAY

        // 体现不可变性
        // withXxx():设置相关属性
        LocalDate localDate1 = localDate.withDayOfMonth(22);
        System.out.println(localDate);//2021-04-25
        System.out.println(localDate1);//2021-04-22

        LocalDateTime localDateTime2 = localDateTime.plusMonths(3);
        System.out.println(localDateTime2);
    }

    @Test
    public void test2() {
        // now(): 获取本初子午线对应的标准时间
        Instant instant = Instant.now();
        System.out.println(instant);

        // 添加时间的偏移量
        OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println(offsetDateTime);

        // 获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数
        long milli = instant.toEpochMilli();
        System.out.println(milli);

        // ofEpochMilli():通过给定的毫秒数，获取Instant实例
        Instant instant1 = Instant.ofEpochMilli(1619394493520L);
        System.out.println(instant1);
    }

    @Test
    public void test3() {
        // 方式一：预定义的标准格式：
        DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
        LocalDateTime localDateTime = LocalDateTime.now();
        String str1 = formatter.format(localDateTime);
        System.out.println(localDateTime);
        System.out.println(str1);

        // 解析：字符串->日期
        TemporalAccessor parse = formatter.parse(str1);
        System.out.println(parse);

        // 方式二：本地化相关的格式。如ofLocalizedDateTime()
        // FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT
        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT);
        String str2 = formatter1.format(localDateTime);
        System.out.println(str2);

        // 方式三：自定义的格式。如 ofPattern("yyyy-MM-dd hh:mm:ss E")
        DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        String str4 = formatter2.format(LocalDateTime.now());
        System.out.println(str4);

        // 解析
        TemporalAccessor accessor = formatter2.parse("2019-02-18 03:52:09");
        System.out.println(accessor);
    }

}
```

## 4. Java比较器

Java中的对象，正常情况下，只能进行比较：== 或 != 。 不能使用>或<。但是在开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小。如何实现？使用两个接口中的任何一个：`Comparable`或`Comparator`。Comparable接口的方式一旦一定，保证Comparable接口实现类的对象在任何位置都可以比较大小，而Comparator接口属于临时性的比较。

### 4.1 Comparable接口

Comparable接口的使用举例：自然排序

* 像String，包装类等实现了Comparable接口，重写了compareTo()方法，给出了比较两个对象大小的方式
* 像String，包装类重写compareTo()方法以后，进行了从小到大的排列
* 重写compareTo(obj)的规则：
    
		如果当前对象this大于形参对象obj，则返回正整数，
		如果当前对象this小于形参对象obj，则返回负整数，
		如果当前对象this等于形参对象obj，则返回零。

* 对于自定义类来说，如果需要排序，我们可以让自定义类实现Comparable接口，重写compareTo(obj),
       在compareTo(obj)方法中指明如何排序。

### 4.2 Comparator接口

Comparator接口的使用：定制排序

* 背景：当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序。
* 重写compare(Object o1, Object o2)方法，比较o1和o2的大小：如果方法返回正整数，则表示o1大于o2;
如果返回0，表示相等；返回负整数，则表示o1小于o2。



```java
package com.atguigu.java;

import org.junit.Test;

import java.util.Arrays;
import java.util.Comparator;

public class CompareTest {

    @Test
    public void test1() {
        String[] arr = new String[]{"AA", "CC", "KK", "MM", "GG", "JJ", "DD"};
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void test2() {
        Goods[] arr = new Goods[4];
        arr[0] = new Goods("iphone", 1200);
        arr[1] = new Goods("xiaomi", 500);
        arr[2] = new Goods("huawei", 1000);
        arr[3] = new Goods("oppo", 300);
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void test3() {
        String[] arr = new String[]{"AA", "CC", "KK", "MM", "GG", "JJ", "DD"};
        Arrays.sort(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof String && o2 instanceof String) {
                    String s1 = (String) o1;
                    String s2 = (String) o2;
                    return -s1.compareTo(s2);
                }
                throw new RuntimeException("输入的数据类型不一致");
            }
        });
        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void test4() {
        Goods[] arr = new Goods[4];
        arr[0] = new Goods("iphone", 1200);
        arr[1] = new Goods("xiaomi", 500);
        arr[2] = new Goods("huawei", 1000);
        arr[3] = new Goods("oppo", 300);
        Arrays.sort(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof Goods && o2 instanceof Goods) {
                    Goods g1 = (Goods) o1;
                    Goods g2 = (Goods) o2;
                    if (g1.getName().equals(g2.getName())) {
                        return -Double.compare(g1.getPrice(), g2.getPrice());
                    } else {
                        return g1.getName().compareTo(g2.getName());
                    }
                }
                throw new RuntimeException("输入的数据类型不一致");
            }
        });
        System.out.println(Arrays.toString(arr));
    }
}
```

## 5. 其他常用类
System类，Math类，BigInteger与BigDecimal类






