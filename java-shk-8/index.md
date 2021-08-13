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

## 5. 枚举类
### 5.1 如何自定义枚举类

一·枚举类的使用

1. 枚举类的理解：类的对象只有有限个，确定的。我们称此类为枚举类
2. 当需要定义一组常量时，强烈建议使用枚举类
3. 如果枚举类中只有一个对象，则可以作为单例模式的实现方式。

二·如何定义枚举类

* 方式一：jdk 5之前，自定义枚举类
* 方式二：jdk 5之后，可以使用enum关键字定义枚举类

三·Enum类中的常用方法

* `values()`：返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值。
* `valueOf(String str)`：可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象。
* `toString()`：返回当前枚举类对象常量的名称。

四·使用enum关键字定义的枚举类实现接口的情况

* 情况一：实现接口，在enum类中实现抽象方法
* 情况二：让枚举类的对象分别实现接口中的抽象方法。

```java
package com.atguigu.java;

public class SeasonTest {
    public static void main(String[] args) {
        Season spring = Season.SPRING;
        System.out.println(spring.toString());
    }
}

class Season {

    // 1. 声明Season对象的属性
    private final String seasonName;
    private final String seasonDesc;

    // 2. 私有化类的构造器, 并给对象属性赋值
    private Season(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    // 3. 提供当前枚举类的多个对象 public static final的
    public static final Season SPRING = new Season("春天", "春暖花开");
    public static final Season SUMMER = new Season("夏天", "夏日炎炎");
    public static final Season AUTUMN = new Season("秋天", "秋高气爽");
    public static final Season WINTER = new Season("冬天", "冰天雪地");

    // 4. 其他诉求：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    // 5. 其他诉求：提供toString方法
    @Override
    public String toString() {
        return "Season{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }
}

```

### 5.2 关键字enum

```java
package com.atguigu.java;

import java.util.Arrays;

/*
使用enum关键字定义枚举类
说明：定义的枚举类默认继承于java.lang.Enum类
 */
public class SeasonTest1 {
    public static void main(String[] args) {
        Season1 summer = Season1.SUMMER;
        System.out.println(summer);
//        Season1{seasonName='夏天', seasonDesc='夏日炎炎'}
        System.out.println(Season1.class.getSuperclass());
//        class java.lang.Enum

        Season1[] values = Season1.values();
        System.out.println(Arrays.toString(values));
        System.out.println(Arrays.toString(Thread.State.values()));
//        [NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED]

        // valueOf(String objName): 返回枚举类中对象名是objName的对象
        Season1 winter = Season1.valueOf("WINTER");
        // 如果没有objName的枚举类对象，则抛异常：IllegalArgumentException
//        Season1 winter = Season1.valueOf("WINTER1");
        System.out.println(winter);
//        Season1{seasonName='冬天', seasonDesc='冰天雪地'}
        winter.show();
//        大约在冬季
    }
}

interface info {
    void show();
}

enum Season1 implements info {

    // 1. 提供当前枚举类的多个对象 多个对象之间用","隔开，末尾对象用";"结束
    SPRING("春天", "春暖花开") {
        @Override
        public void show() {
            System.out.println("春天在哪里");
        }
    },
    SUMMER("夏天", "夏日炎炎") {
        @Override
        public void show() {
            System.out.println("宁夏");
        }
    },
    AUTUMN("秋天", "秋高气爽") {
        @Override
        public void show() {
            System.out.println("秋天不回来");
        }
    },
    WINTER("冬天", "冰天雪地") {
        @Override
        public void show() {
            System.out.println("大约在冬季");
        }
    };

    // 2. 声明Season对象的属性
    private final String seasonName;
    private final String seasonDesc;

    // 3. 私有化类的构造器, 并给对象属性赋值
    private Season1(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    // 4. 其他诉求：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    // 5. 其他诉求：提供toString方法
    @Override
    public String toString() {
        return "Season1{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }

    @Override
    public void show() {
        System.out.println("这是一个季节");
    }
}

```

### 5.3 Enum类常用方法
### 5.4 实现接口的枚举类

## 6. 注解(Annotation)
从JDK 5开始，Java增加了对元数据（MetaData）的支持，也就是Annotation（注解）。Annotation其实就是代码里的特殊标记，这些标记可以在编译，类加载，运行时被读取，并执行相应的处理。通过使用Annotation，程序员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充信息。代码分析工具，开发工具和部署工具可以通过这些补充信息进行验证或者进行部署。

Annotation可以像修饰符一样被使用，可用于修饰包，类，构造器，方法，成员变量，参数，局部变量的声明，这些信息被保存在Annotation的“name=value”对中。

在JavaSE中，注解的使用比较简单，例如标记过时的功能，忽略警告等。在JavaEE/Android中注解占据了更重要的角色，例如用来配置应用程序等任何切面，代替Java旧版中所遗留的繁冗代码和XML配置等。

未来的开发模式都是基于注解的，JPA是基于注解的，Spring2.5以上都是基于注解的，Hibernate3.x以后也是基于注解的，现在的Struts2有一部分也是基于注解的了。注解是一种趋势，一定程度上可以说：框架 = 注解 + 反射 + 设计模式。


### 6.1 注解的使用
1. 理解Annotation
2. Annotation的使用示例

* 示例一：生成文档相关的注解
* 示例二：在编译时进行格式检查（JDK内置的三个基本注解）
    - `@Override`: 限定重写父类方法，该注解只能用于方法
    - `@Deprecated`: 用于表示所修饰的元素（类，方法等）已过时。通常是因为所修饰的结构危险或存在更好的选择。
    - `@SuppressWarnings`: 抑制编译期警告

* 示例三：跟踪代码依赖性，实现替代配置文件功能。

3. 如何自定义注解：参照@suppressWarnings定义
    - 注解声明为：@interface
    - 内部定义成员，通常使用value表示
    - 可以指定成员的默认值，使用default定义
    - 如果自定义注解没有成员，表明是一个标识作用。

如果注解有成员，在使用注解时，需要指明成员的值。
自定义注解必须配上注解的信息处理流程（使用反射）才有意义。
自定义注解通常都会指明两个元注解：Retention，Target

4. jdk提供的4种元注解(元注解：对现有的注解进行解释说明的注解)
    * `@Retention`:指定所修饰的Annotation的生命周期：SOURCE/CLASS（默认）/RUNTIME
      只有声明为RUNTIME生命周期的注解，才能通过反射获取。
    * `@Target`: 用于指定被修饰的Annotation能用于修饰哪些程序元素

    ===== 出现的频率较低 =====

    * `@Documented`：表示所修饰的注解在被javadoc解析时，保留下来。
    * `@Inherited`：被它修饰的Annotation将具有继承性。

5. 通过反射获取注解信息 --- 到反射内容时系统讲解。
6. jdk8中注解的新特性：可重复注解，类型注解

```java
package com.atguigu.java1;

import org.junit.Test;

import java.lang.annotation.Annotation;
import java.util.Arrays;

/*

 */
public class AnnotationTest {
    @Test
    public void testGetAnnotation() {
        Class clazz = Student.class;
        Annotation[] annotations = clazz.getAnnotations();
        System.out.println(Arrays.toString(annotations));
    }
}

@MyAnnotation(value = "hi")
class Person {
    private String name;
    private int age;

    @MyAnnotation
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void walk() {
        System.out.println("人走路");
    }

    public void eat() {
        System.out.println("人吃饭");
    }
}

interface Info {
    void show();
}

class Student extends Person implements Info {
    @Override
    public void walk() {
        System.out.println("学生走路");
    }

    public Student(String name, int age) {
        super(name, age);
    }

    @Override
    public void show() {
    }
}
```

```java
package com.atguigu.java1;

import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;

@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
public @interface MyAnnotation {
    String value() default "hello";
}
```


## 7. 集合

集合框架的概述

1. 集合，数组都是对多个数据进行存储操作的结构，简称Java容器。
说明：此时的存储，主要指的是内存层面的存储，不涉及到持久化的存储（.txt,.jpg,.avi,数据库中）

2. 数组在存储多个数据方面的特点：
- 一旦初始化以后，其长度就确定了。
- 数组一旦定义好，其元素的类型也就确定了，我们也就只能操作指定类型的数据了。比如：`String[] arr; int[] arr1`;

3. 数组在存储多个数据方面的缺点：
- 一旦初始化以后，其长度就不可修改。
- 数组中提供的方法非常有限，对于添加，删除，插入数据等操作，非常不便，同时效率不高。
- 获取数组中实际元素的个数的需求，数组没有现成的属性或方法可用。
- 数组存储数据的特点：有序，可重复。对于无序，不可重复的需求，不能满足。

### 7.1 Java集合框架概述
Java集合可分为Collection和Map两种体系

* Collection接口：单列数据，定义了存取一组对象的方法的集合
	- List：元素有序，可重复的集合
	- Set：元素无序，不可重复的集合
* Map接口：双列数据，保存具有映射关系“key-value对”的集合


        |--- Collection接口：单列集合，用来存储一个一个的对象
               |--- List接口：存储有序的，可重复的数据。---> "动态数组"
                       |--- ArrayList, LinkedList, Vector
               |--- Set接口：存储无序的，不可重复的数据。---> "高中讲的集合"
                       |--- HashSet, LinkedHashSet, TreeSet
        |--- Map接口：双列集合，用来存储一对（key - value）一对的数据 ---> "高中函数：y = f(x)"
               |--- HashMap, LinkedHashMap, TreeMap, Hashtable, Properties


### 7.2 Collection接口方法

```java
package com.atguigu.java2;

import org.junit.Test;
import java.util.ArrayList;
import java.util.Collection;
import java.util.Date;

public class CollectionTest {
    @Test
    public void test1() {
        Collection coll = new ArrayList();
        // add(Object e):将元素e添加到集合coll中
        coll.add("AA");
        coll.add("BB");
        coll.add(123);//自动装箱
        coll.add(new Date());

        // size(): 获取添加的元素的个数
        System.out.println(coll.size());//4

        // addAll():
        Collection coll1 = new ArrayList();
        coll1.add(456);
        coll1.add("CC");
        coll.addAll(coll1);

        System.out.println(coll.size());//6
        System.out.println(coll);

        // clear():清空集合元素
        coll.clear();

        // isEmpty():判断当前集合是否为空
        System.out.println(coll.isEmpty());
    }
}
```

```java
package com.atguigu.java;

import org.junit.Test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;
import java.util.List;

/*
Collection接口中声明的方法的测试
向Collection接口的实现类的对象中添加数据obj时，要求obj所在类要重写equals().
 */
public class CollectionTest {
    @Test
    public void test1() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new String("Tom"));
        coll.add(false);
//        Person p = new Person("Jerry", 20);
//        coll.add(p);
        coll.add(new Person("Jerry", 20));
        // contains(Object obj):判断当前集合中是否包含obj
        System.out.println(coll.contains(123));// true
        System.out.println(coll.contains(new String("Tom")));// true,判断内容

        // 本来为false, 调用equals(),实现为==。重写equals()后,返回true
        // 我们在判断时会调用obj对象所在类的equals()方法
        System.out.println(coll.contains(new Person("Jerry", 20)));

        // 2. containsAll(Collection coll1):判断形参coll1中的所有元素是否都存在于当前集合中
        Collection coll1 = Arrays.asList(123,456);
        System.out.println(coll.containsAll(coll1));// true
    }

    @Test
    public void test2() {
        // 3. remove(Object obj): 从当前集合中移除obj元素
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        coll.remove(123);
        System.out.println(coll);
        coll.remove(new Person("Jerry", 20));
        System.out.println(coll);// [456, Tom, false]

        // 4. removeAll(Collection coll1):差集：从当前集合中移除coll1中所有的元素
        Collection coll1 = Arrays.asList(456,"Tom");
        coll.removeAll(coll1);
        System.out.println(coll);//[false]
    }

    @Test
    public void test3() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        // 5. retainAll(Collection coll1): 交集：获取当前集合和coll1集合的交集，并返回给当前集合
//        Collection coll1 = Arrays.asList(123,456,789);
//        coll.retainAll(coll1);
//        System.out.println(coll);// [123, 456]

        // 6. equals(Object obj):判断当前Collection和形参Collection中元素是否都相同。List考虑顺序，Set不考虑顺序。
        Collection coll1 = new ArrayList();
        coll1.add(123);
        coll1.add(456);
        coll1.add(new Person("Jerry", 20));
        coll1.add(new String("Tom"));
        coll1.add(false);
        System.out.println(coll.equals(coll1));// true
    }

    @Test
    public void test4() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);
        //7. hashCode(): 返回当前对象的哈希值
        System.out.println(coll.hashCode());

        // 8. 集合->数组: toArray()
        Object[] arr = coll.toArray();
        System.out.println(Arrays.toString(arr));
        // 拓展：数组->集合: 调用Arrays类的静态方法asList()
        List<String> list = Arrays.asList(new String[]{"AA","BB","CC"});
        System.out.println(list);//[AA, BB, CC]

        //注意
        System.out.println("===================");
        List<Integer> arr1 = Arrays.asList(new Integer[]{123,456});
        System.out.println(arr1);//[123, 456]
        List arr2 = Arrays.asList(new int[]{123,456});
        System.out.println(arr2);//[[I@6b57696f],有且仅有一个元素，为数组
        List arr3 = Arrays.asList(new Integer[]{123,456});
        System.out.println(arr3);//[123, 456]
        List arr4 = Arrays.asList(123,456);
        System.out.println(arr4);//[123, 456]

        // 9. iterator():返回Iterator接口的实例，用于遍历集合元素。放在IteratorTest.java中测试
    }
}
```

```java
package com.atguigu.java;

import java.util.Objects;

public class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        System.out.println("Person equals");
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }
}

```

### 7.3 Iterator迭代器接口

集合元素的遍历操作，使用迭代器Iterator接口

* Iterator对象称为迭代器（设计模式的一种），主要用于遍历Collection集合中的元素。
* GOF给迭代器模式的定义为：提供一种方法访问一个容器（container）对象中各个元素，而又不需暴露该对象的内部细节。迭代器模式，就是为容器而生。类似于"公交车上的售票员"，"火车上的乘务员"，"空姐"。
* Collection接口继承了java.lang.Iterable接口，该接口有一个iterator()方法，那么所有实现了Collection接口的集合类都有一个iterator()方法，用以返回一个实现了Iterator接口的对象。
* Iterator仅用于遍历集合，Iterator本身并不提供承装对象的能力。如果需要创建Iterator对象，则必须有一个被迭代的集合。
* 集合对象每次调用iterator()方法都得到一个全新的迭代器对象，默认游标都在集合的第一个元素之前。

内部的方法：`hasNext()`和`next()`

内部定义了remove(), 可以在遍历的时候，删除集合中的元素。此方法不同于集合直接调用remove()。如果还未调用next()或在上一次调用next方法之后已经调用了remove方法，再调用remove都会报IllegalStateException

迭代器主要用来遍历Collection，不用于遍历Map


```java
package com.atguigu.java;

import org.junit.Test;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class IteratorTest {
    @Test
    public void test1() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        Iterator iterator = coll.iterator();

        // 方式一：
//        System.out.println(iterator.next());
//        System.out.println(iterator.next());
//        System.out.println(iterator.next());
//        System.out.println(iterator.next());
//        System.out.println(iterator.next());
//        // 报异常：NoSuchElementException
//        System.out.println(iterator.next());

        // 方式二：不推荐
        for (int i = 0; i < coll.size(); i++) {
            System.out.println(iterator.next());
        }

        // 方式三：推荐
        // hasNext():判断是否还有下一个元素
        while (iterator.hasNext()) {
            // next(): 1）指针下移 2）将下移以后集合位置上的元素返回
            System.out.println(iterator.next());
        }
    }

    @Test
    public void test2() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        // 错误方式一：
        Iterator iterator = coll.iterator();
        while ((iterator.next()) != null) {
            System.out.println(iterator.next());
        }
        // 错误方式二：
        while ((coll.iterator().hasNext())) {
            System.out.println(coll.iterator().next());
        }
    }

    @Test
    public void test3() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        // 删除集合中"Tom"
        Iterator iterator = coll.iterator();
        while (iterator.hasNext()) {
            Object obj = iterator.next();
            if ("Tom".equals(obj)) {
                iterator.remove();
            }
        }

        // 遍历集合
        iterator = coll.iterator();
        while (iterator.hasNext()) {
            // next(): 1）指针下移 2）将下移以后集合位置上的元素返回
            System.out.println(iterator.next());
        }

    }
}

```

```java
package com.atguigu.java;

import org.junit.Test;

import java.util.ArrayList;
import java.util.Collection;

/*
jdk 5.0新增了foreach循环，用于遍历集合，数组
 */
public class ForTest {
    @Test
    public void test1() {
        Collection coll = new ArrayList();
        coll.add(123);
        coll.add(456);
        coll.add(new Person("Jerry", 20));
        coll.add(new String("Tom"));
        coll.add(false);

        // 快捷键iter
        // for (集合元素的类型 局部变量: 集合对象)
        // 内部仍然调用了迭代器。
        for (Object obj : coll) {
            System.out.println(obj);
        }
    }

    @Test
    public void test2() {
        int[] arr = new int[]{1,2,3,4,5};
        for (int i : arr) {
            System.out.println(i);
        }
    }

    // 练习题
    @Test
    public void test3() {
        String[] arr = new String[]{"MM", "MM", "MM"};

        // 方式一：普通for赋值
//        for (int i = 0; i < arr.length; i++) {
//            arr[i] = "GG";
//        }

        // 方式二：增强for循环
        for (String s : arr) {
            s = "GG";
        }

        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }
        // MM, MM, MM
        // 方式二并没有改变原数组值。s值传递，同指向MM，但是赋值后，s指向了GG，而arr[i]仍指向MM。
    }
}
```
### 7.4 Collection子接口一：List
#### 7.4.1 List三个实现类的不同
面试题：ArrayList，LinkedList，Vector三者的异同？

* 同：三个类都是实现了List接口，存储数据的特点相同：存储有序的，可重复的数据
* 不同：
    - ArrayList:作为List接口的主要实现类，线程不安全，效率高；底层使用Object[]存储
    - LinkedList:对于频繁的插入，删除操作，使用此类效率比ArrayList高；底层使用双向链表存储
    - Vector:作为List接口的古老实现类；线程安全，效率低；底层使用Object[]存储

#### 7.4.2 实现类的源码分析
##### ArrayList
1. jdk7的情况下
    
        ArrayList list = new ArrayList(); //底层创建了长度是10的Object[]数组elementData
        list.add(123);//elementData[0] = new Integer(123);
        ...
        list.add(11);//如果此次的添加导致底层elementData数组容量不够，则扩容。
    默认情况下，扩容为原来的容量的1.5倍，同时需要将原有数组中的数据复制到新的数组中。

    结论：建议开发中使用带参的构造器：ArrayList list = new ArrayList(int capacity);

2. jdk8中ArrayList的变化:

        ArrayList list = new ArrayList();//底层Object[] elementData初始化为{}，并没有创建长度为10的数组
        list.add(123);// 第一次调用add()时，底层才创建了长度为10的数组，并将数组123添加到elementData中
    ...
    后续的添加和扩容操作与jdk7无异。
3. 小结：jdk7中的ArrayList的对象的创建类似于单例的饿汉式，而jdk8中的ArrayList的对象的创建类似于单例的懒汉式，延迟了数组的创建，节省内存。

##### LinkedList
    LinkedList list = new LinkedList();//内部声明了Node类型的first和last属性，默认为null
    list.add(123);//将123封装到Node中，创建了Node对象
其中，Node中的next和prev指针，体现了LinkedList的双向链表的说法

##### Vector
jdk7和jdk8中通过Vector()构造器创建对象时，底层都创建了长度为10的数组。在扩容方面，默认扩容为原来数组长度的2倍

#### 7.4.3 List常用方法
    void add(int index, Object ele): 在index位置插入ele元素
    boolean addAll(int index, Collection eles): 从index位置开始将eles中的所有元素添加进来
    Object get(int index): 获取指定index位置的元素
    int indexOf(Object obj): 返回obj在集合中首次出现的位置
    int lastIndexOf(Object obj): 返回obj在当前集合中末次出现的位置
    Object remove(int index): 移除指定index位置的元素，并返回此元素
    Object set(int index, Object ele): 设置指定index位置的元素为ele
    List subList(int fromIndex, int toIndex): 返回从fromIndex到toIndex位置的子集合

总结：常用方法
增：add(Object)
删：remove(int index) / remove(Object obj)
改：set(int index, Object ele)
查：get(int index)
插：add(int index, Object ele)
长度：size()
遍历：1.Iterator迭代器， 2.增强for循环, 3.普通的循环

```java
public class ListTest {
    @Test
    public void test1() {
        ArrayList list = new ArrayList();
        list.add(123);
        list.add(456);
        list.add("AA");
        list.add(new Person("Tom", 12));
        list.add(456);
        System.out.println(list);


        list.add(1, "BB");
        System.out.println(list);
        List list1 = Arrays.asList(1,2,3);
        list.addAll(list1);
        System.out.println(list.size());

        System.out.println(list.get(0));
    }

    @Test
    public void test2() {
        ArrayList list = new ArrayList();
        list.add(123);
        list.add(456);
        list.add("AA");
        list.add(new Person("Tom", 12));
        list.add(456);
        System.out.println(list.indexOf(456));
        System.out.println(list.indexOf(789));
        System.out.println(list.lastIndexOf(456));
        Object obj = list.remove(0);
        System.out.println(obj);
        list.set(1, "CC");
        System.out.println(list);
        System.out.println(list.subList(2, 4));
    }

    @Test
    public void test3() {
        ArrayList list = new ArrayList();
        list.add(123);
        list.add(456);
        list.add("AA");

        Iterator iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        System.out.println("*************");
        for (Object obj : list) {
            System.out.println(obj);
        }
        System.out.println("*************");
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }

    @Test
    public void test4() {
        // 区分List中remove(int index) 和 remove(Object obj)
        List list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);
        System.out.println(list);
        list.remove(2);
        System.out.println(list);
        list.remove(new Integer(1));
        System.out.println(list);
    }
}
```

### 7.5 Collection子接口二：Set

#### 7.5.1 Set接口的框架

    |----Collection接口：单列集合，用来存储一个一个的对象
              |----Set接口：存储无序的，不可重复的数据（高中讲的集合）
                    |----HashSet：作为Set接口的主要实现类：线程不安全的：可以存储null值
                            |----LinkedHashSet：作为HashSet的子类：遍历其内部数据时，可以按照添加顺序遍历
                                                对于频繁的遍历操作，LinkedHashSet效率高于HashSet
                    |----TreeSet：可以按照添加对象的指定属性，进行排序

* HashSet: 作为Set接口的主要实现类：线程不安全的；可以存储null值
* LinkedHashSet: 作为HashSet的子类；遍历其内部数据时，可以按照添加的顺序遍历
* TreeSet: 可以按照添加对象的指定属性，进行排序。


Set接口中没有额外定义新的方法，使用的都是collection中声明过的方法。

* 要求：向Set中添加的数据，其所在的类一定要重写hashCode()和equals()
* 要求：重写的hashCode()和equals()尽可能保持一致性：相等的对象必须具有相等的散列码
       重写两个方法的小技巧：对象中用作equals()方法比较的field，都应该用来计算hashCode值

#### 7.5.2 理解

1. Set：无序，不可重复 (以HashSet为例说明)
    
* 无序性：不等于随机性。存储的数据在底层数组中并非按照索引的顺序添加，而是根据数据的哈希值决定
* 不可重复性：保证添加的元素按照equals()判断时，不能返回true。即：相同的元素只能添加一次

2. 添加元素的过程：(以HashSet为例)
    
我们向HashSet中添加元素a，首先调用元素a所在类的hashCode()方法，计算元素a的哈希值，此哈希值接着通过某种算法计算出在HashSet底层数组中的存放位置(即：索引位置)，判断数组此位置上是否已经有元素：

* 如果此位置上没有其他元素，则元素a添加成功。---情况1
* 如果此位置上有其他元素b（或以链表形式存在的多个元素），则比较元素a与元素b的hash值，
    * 如果hash值不相同，则元素a添加成功。---情况2
    * 如果hash值相同，进而需要调用元素a所在类的equals()方法：
        * equals()返回true，元素a添加失败
        * equals()返回false，则元素a添加成功。---情况3

对于添加成功的情况2和情况3而言：元素a与已经存在指定索引位置上的数据以链表的方式存储。jdk7：元素a放到数组中，指向原来的元素。jdk8：原来的元素在数组中，指向元素a。总结：七上八下

HashSet底层：数组+链表的结构。

```java
package com.atguigu.java1;

import com.atguigu.java.Person;
import org.junit.Test;

import java.util.HashSet;
import java.util.Iterator;
import java.util.LinkedHashSet;
import java.util.Set;

public class SetTest {

    @Test
    public void test1() {
        Set set = new HashSet();
        set.add(456);
        set.add(123);
        set.add("AA");
        set.add("CC");
        set.add(new Person("Tom", 12));
        set.add(129);
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    // LinkedHashSet的使用
    // LinkedHashSet作为HashSet的子类，在添加数据的同时，每个数据还维护了两个引用，记录此数据
    // 和有一个数据。
    // 优点：对于频繁的遍历操作，效率高于HashSet
    @Test
    public void test2() {
        Set set = new LinkedHashSet();
        set.add(456);
        set.add(123);
        set.add("AA");
        set.add("CC");
        set.add(new Person("Tom", 12));
        set.add(129);
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}

```


#### 7.5.3 TreeMap

1. 向TreeSet中添加的数据，要求是相同类的对象
2. 两种排序方式，自然排序（实现Comparable接口） 和 定制排序（Comparator）
3. 自然排序中，比较两个对象是否相同的标准为：compareTo()返回0，不再是equals()；底层是通过compareTo存入二叉树，如果compareTo相等，则被看作重复元素，不予添加
4. 定制排序中，比较两个对象是否相同的标准为：compare()返回0，不再是equals()


```java
package com.atguigu.java1;

import org.junit.Test;

import java.util.Comparator;
import java.util.Iterator;
import java.util.TreeSet;

public class TreeSetTest {

    @Test
    public void test1() {
//        TreeSet set = new TreeSet();
//        set.add(123);
//        set.add(12);
//        set.add(-34);
//        set.add(0);
//        Iterator iterator = set.iterator();
//        while (iterator.hasNext()) {
//            System.out.println(iterator.next());
//        }
//        -34
//        0
//        12
//        123

        TreeSet set = new TreeSet();
        set.add(new User("Tom", 12));
        set.add(new User("Jerry", 32));
        set.add(new User("Jim", 2));
        set.add(new User("Mike", 65));
        set.add(new User("Jack", 33));

        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
    @Test
    public void test2() {
        Comparator com = new Comparator() {
            // 按照年龄从小到大排列
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof User && o2 instanceof User) {
                    User u1 = (User)o1;
                    User u2 = (User)o2;
                    return Integer.compare(u1.getAge(), u2.getAge());
                } else {
                    throw new RuntimeException("输入的数据类型不匹配");
                }
            }
        };
        TreeSet set = new TreeSet(com);

        set.add(new User("Tom", 12));
        set.add(new User("Jerry", 32));
        set.add(new User("Jim", 2));
        set.add(new User("Mike", 65));
        set.add(new User("Jack", 33));

        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
//        User{name='Jim', age=2}
//        User{name='Tom', age=12}
//        User{name='Jerry', age=32}
//        User{name='Jack', age=33}
//        User{name='Mike', age=65}
    }
}

```

关于Set的一个总结问题： 集合Collection中存储的如果是自定义的对象，需要自定义类重写哪个方法？为什么？

    List：equals()
    Set: HashSet, LinkedHashSet: equals(), hashCode()
         TreeSet: Comparable: compareTo(Object obj)
                  Comparator: compare(Object o1, Object o2)

### 7.6 Map接口

#### 7.6.1 Map实现类的结构
    |----Map:双列数据，存储key-value对的数据 （类似于高中的函数）
          |----HashMap: 作为Map的主要实现类：线程不安全的，效率高；存储null的key和value
                  |----LinkedHashMap:保证在遍历map元素时，可以按照添加的顺序实现遍历。
                                     原理：在原有的HashMap底层结构基础上，添加了一对儿指针，指向前一个和后一个
                                     对于频繁的遍历操作，此类执行效率高于HashMap
          |----TreeMap: 保证按照添加到key-value对进行排序，实现排序遍历。此时考虑key的自然排序或定制排序
                        底层使用红黑树
          |----Hashtable: 作为古老的实现类：线程安全的，效率低；不能存储null的key和value
                  |----Properties：常用来处理配置文件。key和value都是String类型


HashMap的底层：数组 + 链表（jdk7及以前）;数组 + 链表 + 红黑树（jdk8）

面试题：

1. HashMap的底层实现原理
2. HashMap和Hashtable的异同？
3. CurrentHashMap与Hashtable的异同？（暂时不讲）

#### 7.6.2 Map结构的理解

* Map中的key，无序的，不可重复的，使用Set存储所有的key --->key所在的类要重写equals()和hashCode()(以HashMap为例)
* Map中的value：无序的，可重复的，使用collection存储所有的value --->value所在的类要重写equals()(Collection要求)
* 一个键值对：key-value构成了一个Entry对象。
* Map中的entry：无序的，不可重复的，使用Set存储所有的entry

#### 7.6.3 HashMap的底层实现原理，以jdk7说明：

    HashMap map = new HashMap();
在实例化以后，底层创建了长度是16的一维数组Entry[] table

    ...可能已经执行过多次put...
    map.put(key1, value1);
* 首先，调用key1所在类的hashCode()计算key1哈希值，此哈希值经过某种算法计算以后，得到在Entry数组中的存放位置。
* 如果此位置上的数据为空，此时的key1-value1添加成功。 ----情况1
* 如果此位置上的数据不为空，（意味着此位置上存在一个或多个数据，以链表形式存在），比较key1和已经存在的一个或多个数据的哈希值：
    * 如果key1的哈希值与已经存在的数据的哈希值都不相同，此时key1-value1添加成功 ----情况2
    * 如果key1的哈希值与已经存在的某一个数据（key2-value2）的哈希值相同，继续比较：调用key1所在类的equals(key2)方法，比较：
        * 如果equals()返回false：此时key1-value1添加成功 ----情况3
        * 如果equals()返回true：使用value1替换value2。

补充：关于情况2和情况3：此时key1-value1和原来的数据以链表的方式存储

在不断地添加过程中，会涉及到扩容问题，默认的扩容方式：扩容为原来容量的2倍，并将原有的数据复制过来。

jdk8相较于jdk7在底层实现方面的不同：

1. new HashMap():底层没有创建一个长度为16的数组
2. jdk8底层的数组是：Node[], 而非Entry[]
3. 首次调用put()方法时，底层创建长度为16的数组
4. jdk7底层结构只有：数组 + 链表。 jdk8中底层结构：数组 + 链表 + 红黑树
    当数组的某一个索引位置上的元素以链表形式存在的数据个数 > 8且当前数组的长度 > 64时，此时此索引位置上的所有数据改为使用红黑树搜索。

#### 7.6.4 LinkedHashMap的底层实现原理（了解）
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }

#### 7.6.5 Map接口中定义的方法
    Object put (Object key, Object value): 将指定key-value添加到（或修改）当前map对象中
    void putAll(Map m): 将m中的所有key-value对存放到当前map中
    Object remove(Object key):移除指定key的key-value对，并返回value
    void clear():清空当前map中的所有数据
    Object get(Object key):获取指定key对应的value
    boolean containsKey(Object key):是否包含指定的key
    boolean containsValue(Object value):是否包含指定的value
    int size():返回map中key-value对的个数
    boolean isEmpty():判断当前map是否为空
    boolean equals(Object obj):判断当前map和参数对象obj是否相等
    Set keySet():返回所有key构成的Set集合
    Collection values():返回所有value构成的Collection集合
    Set entrySet():返回所有key-value对构成的Set集合

    总结：常用方法
    增：put (Object key, Object value)
    删：remove(Object key)
    改：put (Object key, Object value)
    查：get(Object key)
    插：无序
    长度：size()
    遍历：keySet()，values()，entrySet()

```java
package com.atguigu.java;

import org.junit.Test;

import java.util.*;

public class MapTest {

    @Test
    public void test1() {
        Map map = new HashMap();
        map.put(123, "AA");
        map.put(345, "BB");
        map.put(12, "CC");
        System.out.println(map);
    }

    @Test
    public void test2() {
        Map map = new HashMap();
        map.put("AA", 123);
        map.put(45, 123);
        map.put("BB", 123);
        map.put("AA", 789);
        System.out.println(map);

        Map map2 = new HashMap();
        map2.put("CC", 123);
        map2.put("DD", 345);
        map2.putAll(map);
        System.out.println(map2);

        Object value = map2.remove("CC");
        System.out.println(map2);

        map.clear();//与 map = null不同
        System.out.println(map.size()); // 0
    }

    @Test
    public void test3() {
        Map map = new HashMap();
        map.put("AA", 123);
        map.put(45, 123);
        map.put("BB", 56);
        System.out.println(map.get(45));
        System.out.println(map.containsKey("BB"));
        System.out.println(map.containsValue(123));

        map.clear();
        System.out.println(map.isEmpty());
    }

    @Test
    public void test4() {
        Map map = new HashMap();
        map.put("AA", 123);
        map.put(45, 123);
        map.put("BB", 56);

        // 遍历所有的key集：keySet()
        Set set = map.keySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }

        // 遍历所有的value集：values()
        Collection values = map.values();
        for (Object obj: values) {
            System.out.println(obj);
        }

        // 遍历所有key-value：entrySet()
        Set entrySet = map.entrySet();
        Iterator iterator1 = entrySet.iterator();
        while (iterator1.hasNext()) {
            Object obj = iterator1.next();
            Map.Entry entry = (Map.Entry) obj;
            System.out.println(entry.getKey() + "---->" + entry.getValue());
        }
    }
}
```

#### 7.6.6 TreeMap
```java
package com.atguigu.java;

import org.junit.Test;

import java.util.Comparator;
import java.util.Iterator;
import java.util.Set;
import java.util.TreeMap;

public class TreeMapTest {
    // 向TreeMap中添加key-value，要求key必须是由同一个类创建的对象
    // 因为要按照key进行排序：自然排序，定制排序
    // 自然排序
    @Test
    public void test1() {
        TreeMap map = new TreeMap();
        User u1 = new User("Tom", 23);
        User u2 = new User("Jerry", 32);
        User u3 = new User("Jack", 20);
        User u4 = new User("Rose", 18);

        map.put(u1, 98);
        map.put(u2, 89);
        map.put(u3, 76);
        map.put(u4, 100);

        Set entrySet = map.entrySet();
        Iterator iterator = entrySet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    // 定制排序
    @Test
    public void test2() {
        TreeMap map = new TreeMap(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof User && o2 instanceof  User) {
                    User u1 = (User)o1;
                    User u2 = (User)o2;
                    return Integer.compare(u1.getAge(), u2.getAge());
                } else {
                    throw new RuntimeException("输入的类型不匹配");
                }
            }
        });
        User u1 = new User("Tom", 23);
        User u2 = new User("Jerry", 32);
        User u3 = new User("Jack", 20);
        User u4 = new User("Rose", 18);

        map.put(u1, 98);
        map.put(u2, 89);
        map.put(u3, 76);
        map.put(u4, 100);

        Set entrySet = map.entrySet();
        Iterator iterator = entrySet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```
### 7.7 Collections工具类

Collections:操作Collection，Map的工具类

面试题：Collection与Collections的区别？

    reverse(List):反转List中元素的顺序
    shuffle(List):对List集合元素进行随机排序
    sort(List):根据元素的自然顺序对指定List集合元素按升序排序
    sort(List, Comparator):根据指定的Comparator产生的顺序对List集合元素进行排序
    swap(List, int, int):将指定list集合中i处元素和j处元素进行交换

    Object max(Collection):根据元素的自然顺序，返回给定集合中的最大元素
    Object max(Collection, Comparator):根据Comparator指定的顺序，返回给定集合中的最大元素
    Object min(Collection):
    Object min(Collection, Comparator):
    int frequency(Collection, Object):返回指定集合中指定元素的出现次数
    void copy(List dest, List src):将src中的内容复制到dest中
    boolean replaceAll(List list, Object oldVal, Object newVal):使用新值替换List

Collections类中提供了多个synchronizedXxx()方法，该方法可使将指定集合，从而可以解决多线程并发访问集合时的线程安全问题

## 8. 其他常用类
System类，Math类，BigInteger与BigDecimal类






