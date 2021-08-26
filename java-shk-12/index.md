# Java零基础教程（十二.反射Reflection）


## 1. Java反射机制概述

```java
package com.atguigu.java;

import org.junit.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReflectionTest {

    // 反射之前
    @Test
    public void test1() {
        // 1. 创建Person类的对象
        Person p1 = new Person("Tom", 12);

        // 2. 通过对象，调用其内部的属性和方法
        p1.age = 10;
        System.out.println(p1.toString());

        p1.show();

        // 在Person类的外部，不可以通过Person类的对象调用其内部的私有结构
        // 比如：name, showNation(), 以及私有的构造器（封装性的限制）
    }

    // 反射之后，对于Person的操作
    @Test
    public void test2() throws Exception {
        Class clazz = Person.class;
        // 1. 通过反射，创建Person类的对象
        Constructor cons = clazz.getConstructor(String.class, int.class);
        Object obj = cons.newInstance("Tom", 12);
        Person p = (Person) obj;
        System.out.println(obj.toString());

        // 2. 通过反射，调用对象指定的属性和方法
        Field age = clazz.getDeclaredField("age");
        age.set(p, 10);
        System.out.println(p.toString());


        // 3. 调用方法
        Method show = clazz.getDeclaredMethod("show");
        show.invoke(p);

        // 4. 通过反射，可以调用Person类的私有结构的。比如：私有的构造器，属性，方法
        Constructor cons1 = clazz.getDeclaredConstructor(String.class);
        cons1.setAccessible(true);
        Person p1 = (Person) cons1.newInstance("Jerry");
        System.out.println(p1.toString());

        // 调用私有的属性
        Field name = clazz.getDeclaredField("name");
        name.setAccessible(true);
        name.set(p1, "HanMeimei");

        // 调用私有方法
        Method showNation = clazz.getDeclaredMethod("showNation", String.class);
        showNation.setAccessible(true);
        String nation = (String) showNation.invoke(p1, "中国");
        System.out.println(nation);
    }

    // 疑问：通过直接new的方式或反射的方式都可以调用公共的结构，开发中到底用哪个？
    // 建议：直接new的方式
    // 疑问：什么时候会用到反射的方式调用？
    // 反射的特征：动态性
    // 疑问：反射机制与面向对象中的封装性是不是矛盾的？如何看待两个技术？
    // 不矛盾。封装性可以理解为不建议调用private结构




    // 获取Class实例的方式
    @Test
    public void test3() throws ClassNotFoundException {
        // 方式一：调用运行时类的属性: .class
        Class<Person> clazz1 = Person.class;
        System.out.println(clazz1);
        // 方式二：通过运行时类的对象, 调用getClass()
        Person p1 = new Person();
        Class clazz2 = p1.getClass();
        System.out.println(clazz2);

        // 方式三(开发中常用)：调用Class的静态方法：forName(String classPath)
        Class clazz3 = Class.forName("com.atguigu.java.Person");
        System.out.println(clazz3);

        System.out.println(clazz1 == clazz2);
        System.out.println(clazz2 == clazz3);

        // 方式四：使用类的加载器：ClassLoader
        ClassLoader classLoader = ReflectionTest.class.getClassLoader();
        Class clazz4 = classLoader.loadClass("com.atguigu.java.Person");
        System.out.println(clazz4 == clazz3);
    }
}

```

```java
package com.atguigu.java;

public class Person {
    private String name;
    public int age;

    public Person() {
        System.out.println("Person()");
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    private Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public void show() {
        System.out.println("你好，我是一个人");
    }

    private String showNation(String nation) {
        System.out.println("我的国籍是：" + nation);
        return nation;
    }

}

```

## 2. 理解Class类并获取Class实例

关于java.lang.Class类的理解
    
1. 类的加载过程：程序经过java.exe命令后，会生成一个或多个字节码文件（.class结尾），接着我们使用java.exe命令对某个字节码文件进行解释运行。相当于将某个字节码文件加载到内存中。此过程就称为类的加载。加载到内存中的类，我们就称为运行时类，此运行时类就作为Class的一个实例。
2. 换句话说，Class的实例就对应着一个运行时类.
3. 加载到内存中的运行时类，会缓存一定的时间。在此时间之内，我们可以通过不同的方式来获取此运行时类。注意这里是"获取"，不是"创建"， 因为运行时已经加载到内存中。

万事万物皆对象？对象，File，URL，类也是对象（反射），数据库操作


## 3. 类的加载与ClassLoader的理解

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class ClassLoaderTest {
    @Test
    public void test1() {
        // 对于自定义类，使用系统类加载器进行加载
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        System.out.println(classLoader);
        // 调用系统类加载器的getParent():获取扩展类加载器
        ClassLoader classLoader1 = classLoader.getParent();
        System.out.println(classLoader1);
        // 调用扩展类加载器的getParent()：无法获取引导类加载器
        // 引导类加载器主要负责加载java的核心类库，无法加载自定义类的
        ClassLoader classLoader2 = classLoader1.getParent();
        System.out.println(classLoader2);
        ClassLoader classLoader3 = String.class.getClassLoader();
        System.out.println(classLoader3);
    }

    /*
    Properties:用来读取配置文件
     */
    @Test
    public void test2() throws IOException {
        Properties pros = new Properties();
        // 此时的文件默认在当前module下
        // 读取配置文件的方式一
        FileInputStream fis = new FileInputStream("jdbc.properties");

        // 读取配置文件的方式二：使用ClassLoader
        // 配置文件默认识别为：当前module的src下
//        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
//        InputStream is = classLoader.getResourceAsStream("jdbc1.properties");


        pros.load(fis);
        String user = pros.getProperty("user");
        String password = pros.getProperty("password");
        System.out.println("user = " + user + ", password = " + password);
    }
}

```

## 4. 创建运行时类的对象

```java
package com.atguigu.java;

import org.junit.Test;
import java.util.Random;

/*
通过反射创建对应的运行时类的对象
 */
public class NewInstanceTest {
    @Test
    public void test1() throws IllegalAccessException, InstantiationException {
        Class<Person> clazz = Person.class;
        /*
        newInstance(): 调用此方法，创建对应的运行时类的对象.内部调用了运行时类的空参构造器

        要想此方法正常地创建运行时类的对象，要求：
        1. 运行时类必须提供空参的构造器
        2. 空参的构造器的访问权限得够。通常，设置为public

        在javabean中要求提供一个public的空参构造器，原因：
        1. 便于通过反射，创建运行时类的对象
        2. 便于子类继承此运行时类时，默认调用super()时，保证父类有此构造器
         */
        Person obj = clazz.newInstance();
        System.out.println(obj);
    }

    // 体会反射的动态性
    @Test
    public void test2() {
        int num = new Random().nextInt(3);// 0, 1, 2
        String classPath = "";
        switch (num) {
            case 0:
                classPath = "java.util.Date";
                break;
            case 1:
                classPath = "java.lang.Object";
                break;
            case 2:
                classPath = "com.atguigu.java.Person";
                break;
        }
        Object obj = null;
        try {
            obj = getInstance(classPath);
            System.out.println(obj);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /*
    创建一个指定类的对象
    classPath:指定类的全类名
     */
    public Object getInstance(String classPath) throws Exception {
        Class clazz = Class.forName(classPath);
        return clazz.newInstance();
    }
}

```

## 5. 获取运行时类的完整结构

定义父类Creature
```java
package com.atguigu.java1;

import java.io.Serializable;

public class Creature<T> implements Serializable {
    private char gender;
    public double weight;

    private void breath() {
        System.out.println("生物呼吸");
    }

    public void eat() {
        System.out.println("生物吃东西");
    }
}

```

定义子类Person
```java
package com.atguigu.java1;
@MyAnnotation(value="hi")
public class Person extends Creature<String> implements Comparable<String>, MyInterface {
    private String name;
    int age;
    public int id;

    public Person() {
    }

    @MyAnnotation(value="abc")
    private Person(String name) {
        this.name = name;
    }

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @MyAnnotation
    private void show(String nation){
        System.out.println("我的国籍是：" + nation);
    }

    public String display(String interest) throws NullPointerException, ClassCastException {
        return interest;
    }

    @Override
    public void info() {
        System.out.println("我是一个人");
    }

    @Override
    public int compareTo(String o) {
        return 0;
    }

    private static void showDesc() {
        System.out.println("我是一个可爱的人");
    }
}

```

定义接口MyInterface
```java
package com.atguigu.java1;

public interface MyInterface {
    void info();
}
```

定义注解MyAnnotation
```java
package com.atguigu.java1;


import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;

@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, MODULE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String value() default "hello";
}
```


获取当前运行时类的属性结构
```java
package com.atguigu.java2;

import com.atguigu.java1.Person;
import org.junit.Test;

import java.lang.reflect.Field;
import java.lang.reflect.Modifier;


public class FieldTest {
    @Test
    public void test1() {
        Class clazz = Person.class;

        // 获取属性结构
        // getFields()获取当前运行时类及其父类中声明为public访问权限的属性
        Field[] fields = clazz.getFields();
        for (Field f : fields) {
            System.out.println(f);
        }

        // getDeclaredFields(): 获取当前运行时类中声明的所有属性（不包含父类中声明的属性）
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field f : declaredFields) {
            System.out.println(f);
        }
    }

    // 权限修饰符 数据类型 变量名
    @Test
    public void test2() {
        Class clazz = Person.class;
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field f : declaredFields) {
            // 权限修饰符
            int modifiers = f.getModifiers();
            System.out.println(Modifier.toString(modifiers));
            // 数据类型
            Class<?> type = f.getType();
            System.out.println(type);
            // 变量名
            String fName = f.getName();
            System.out.println(fName);
            System.out.println("==========");
        }
    }
}

```

获取运行时类的方法结构
```java
package com.atguigu.java2;

import com.atguigu.java1.Person;
import org.junit.Test;
import org.junit.experimental.theories.suppliers.TestedOn;

import java.lang.annotation.Annotation;
import java.lang.reflect.Method;
import java.lang.reflect.Modifier;

public class MethodTest {
    @Test
    public void test1() {
        Class clazz = Person.class;

        // getMethods(): 获取当前运行时类及其所有父类中声明为public权限的方法
        Method[] methods = clazz.getMethods();
        for (Method m : methods) {
            System.out.println(m);
        }

        System.out.println("===================");
        // getDeclaredMethods(): 获取当前运行时类中声明的所有方法（不包含父类中声明的方法）
        Method[] declaredMethods = clazz.getDeclaredMethods();
        for (Method m : declaredMethods) {
            System.out.println(m);
        }
    }

    /*
    权限修饰符 返回值类型 方法名（参数类型1，形参名1，...） throws Exception
     */
    @Test
    public void test2() {

        Class clazz = Person.class;
        Method[] declaredMethods = clazz.getDeclaredMethods();
        for (Method m : declaredMethods) {
            // 1. 获取方法声明的注解
            Annotation[] annos = m.getAnnotations();
            for (Annotation a : annos) {
                System.out.println(a);
            }
            // 2. 权限修饰符
            System.out.println(Modifier.toString(m.getModifiers()));
            // 3. 返回值类型
            System.out.println(m.getReturnType().getName());
            // 4. 方法名
            System.out.println(m.getName());
            // 5. 形参列表
            Class[] parameterTypes = m.getParameterTypes();
            if (!(parameterTypes == null || parameterTypes.length == 0)) {
                for (Class p : parameterTypes) {
                    System.out.print(p.getName());
                }
            }
            System.out.println();

            // 6. 抛出的异常
            Class<?>[] exceptionTypes = m.getExceptionTypes();
            if (!(exceptionTypes == null || exceptionTypes.length == 0)) {
                for (Class p : exceptionTypes) {
                    System.out.print(p.getName());
                }
            }
            System.out.println();
            System.out.println("====================");
        }
    }
}

```

获取运行时类的其他结构
```java
package com.atguigu.java2;

import com.atguigu.java1.Person;
import org.junit.Test;

import java.lang.annotation.Annotation;
import java.lang.reflect.Constructor;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;

public class OtherTest {
    /*
    获取构造器结构
     */
    @Test
    public void test1() {
        Class clazz = Person.class;
        // getConstructors():获取当前运行时类中声明为public的构造器
        Constructor[] constructors = clazz.getConstructors();
        for (Constructor c : constructors) {
            System.out.println(c);
        }
        System.out.println();
        // getDeclaredConstructors(): 获取当前运行时类中声明的所有构造器
        Constructor[] declaredConstructors = clazz.getDeclaredConstructors();
        for (Constructor c : declaredConstructors) {
            System.out.println(c);
        }
    }

    /*
    获取运行时类的父类
     */
    @Test
    public void test2() {
        Class clazz = Person.class;
        Class superclass = clazz.getSuperclass();
        System.out.println(superclass);
    }

    /*
    获取运行时类的带泛型的父类
     */
    @Test
    public void test3() {
        Class clazz = Person.class;
        Type genericSuperclass = clazz.getGenericSuperclass();
        System.out.println(genericSuperclass);
    }

    /*
    获取运行时类的带泛型的父类的泛型
    代码：逻辑性代码 vs 功能性代码（车轮子）
     */
    @Test
    public void test4() {
        Class clazz = Person.class;
        Type genericSuperclass = clazz.getGenericSuperclass();
        ParameterizedType parameterizedType = (ParameterizedType) genericSuperclass;
        // 获取泛型类型
        Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
        System.out.println(actualTypeArguments[0].getTypeName());
    }

    /*
    获取运行时类实现的接口
     */
    @Test
    public void test5() {
        Class clazz = Person.class;
        Class[] interfaces = clazz.getInterfaces();
        for (Class c : interfaces) {
            System.out.println(c);
        }
        System.out.println();
        Class[] interfaces1 = clazz.getSuperclass().getInterfaces();
        for (Class c : interfaces1) {
            System.out.println(c);
        }
    }

    /*
    获取运行时类所在的包
     */
    @Test
    public void test6() {
        Class clazz = Person.class;
        Package aPackage = clazz.getPackage();
        System.out.println(aPackage);
    }

    /*
    获取运行时类声明的注解
     */
    @Test
    public void test7() {
        Class clazz = Person.class;
        Annotation[] annotations = clazz.getAnnotations();
        for (Annotation anno : annotations) {
            System.out.println(anno);
        }
    }
}

```
## 6. 调用运行时类的指定结构

```java
package com.atguigu.java2;

import com.atguigu.java1.Person;
import org.junit.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

/*
调用运行时类中指定的结构：属性，方法，构造器
 */
public class ReflectionTest {
    // 不需要掌握
    @Test
    public void test1() throws Exception {
        Class clazz = Person.class;
        // 创建运行时类的对象
        Person p = (Person) clazz.newInstance();
        // 获取指定的属性:要求运行时类中属性声明为public
        // 通常不实用此方法，因为属性一般为private
        Field id = clazz.getField("id");
        /*
        设置当前属性的值
        set()： 参数1，指明设置哪个对象的属性， 参数2：将此属性设置为多少
         */
        id.set(p, 1001);
        int pId = (int) id.get(p);
        System.out.println(pId);
    }

    /*
    如何操作运行时类中的指定的属性 - 需要掌握
     */
    @Test
    public void testField1() throws Exception {
        Class clazz = Person.class;
        // 创建运行时类的对象
        Person p = (Person) clazz.newInstance();
        // getDeclaredField(String fieldName): 获取运行时类中指定变量名的属性
        Field name = clazz.getDeclaredField("name");
        // 保证当前属性是可访问的
        name.setAccessible(true);
        // 获取，设定指定对象的属性值
        name.set(p, "Tom");
        System.out.println(name.get(p));
    }

    /*
    如何操作运行时类中的指定的方法 - 需要掌握
     */
    @Test
    public void testMethod() throws Exception {
        Class clazz = Person.class;
        // 创建运行时类的对象
        Person p = (Person) clazz.newInstance();
        // 获取指定的某个方法
        // getDeclaredMethod(): 参数1：指明获取的方法的名称 参数2：指明获取方法的形参列表
        Method show = clazz.getDeclaredMethod("show", String.class);
        // 保证当前方法是可访问的
        show.setAccessible(true);
        // 调用方法的invoke(): 参数1： 方法的调用者 参数2：给方法形参赋值的实参
        // invoke()的返回值即为对应类中调用的方法的返回值
        Object returnValue = show.invoke(p, "CHN");// p.show("CHN");
        System.out.println(returnValue);

        // private static void showDesc()
        Method showDesc = clazz.getDeclaredMethod("showDesc");
        showDesc.setAccessible(true);
        // 如果调用的运行时类中的方法没有返回值，则此invoke()返回null
        showDesc.invoke(Person.class);
    }

    /*
    如何调用运行时类中指定的构造器
     */
    @Test
     public void testConstructor() throws Exception {
        Class clazz = Person.class;
        // private Person(String name)
        //1. 获取指定的构造器
        // getDeclaredConstructor(): 参数：指明构造器的参数列表
        Constructor constructor = clazz.getDeclaredConstructor(String.class);
        // 保证此构造器是可访问的
        constructor.setAccessible(true);
        // 调用此构造器创建运行时类的对象
        Person per = (Person) constructor.newInstance("Tom");
        System.out.println(per);
    }
}

```

## 7. 反射的应用：动态代理


### 7.1 回顾静态代理

```java
package com.atguigu.java;
/*
静态代理举例
特点：代理类和被代理类在编译期间就确定下来了
 */
interface ClothFactory {
    void produceCloth();
}

// 代理类
class ProxyClothFactory implements ClothFactory {
    private ClothFactory factory;// 用被代理类对象进行实例化
    public ProxyClothFactory(ClothFactory factory) {
        this.factory = factory;
    }

    @Override
    public void produceCloth() {
        System.out.println("代理工厂做一些准备工作");
        factory.produceCloth();
        System.out.println("代理工厂做一些后续的收尾工作");
    }
}

// 被代理类
class NikeClothFactory implements ClothFactory {

    @Override
    public void produceCloth() {
        System.out.println("Nike工厂生产一批运动服");
    }
}

public class StaticProxyTest {
    public static void main(String[] args) {
        // 创建被代理类的对象
        NikeClothFactory nike = new NikeClothFactory();
        // 创建代理类的对象
        ProxyClothFactory proxyClothFactory = new ProxyClothFactory(nike);

        proxyClothFactory.produceCloth();

    }
}

```

### 7.2 根据运行时类动态的创建代理类

```java
package com.atguigu.java;
/*
动态代理的举例
 */

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface Human {
    String getBelief();
    void eat(String food);
}
// 被代理类
class SuperMan implements Human {

    @Override
    public String getBelief() {
        return "I believe I can fly";
    }

    @Override
    public void eat(String food) {
        System.out.println("我喜欢吃" + food);
    }
}

class HumanUtil {
    public void method1() {
        System.out.println("通用方法1");
    }

    public void method2() {
        System.out.println("通用方法2");
    }
}

/*
要想实现动态代理，需要解决的问题：
问题1：如何根据加载到内存中的被代理类，动态地创建一个代理类及其对象
问题2：当通过被代理类的对象调用方法时，如何动态地调用被代理类中的同名方法
 */

class ProxyFactory {
    //obj被代理类的对象， 调用此方法返回一个代理类的对象，解决问题1
    public static Object getProxyInstance(Object obj) {
        MyInvocationHandler handler = new MyInvocationHandler();
        handler.bind(obj);
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), handler);
    }
}

class MyInvocationHandler implements InvocationHandler {

    private Object obj;// 需要使用被代理类的对象进行赋值
    public void bind(Object obj) {
        this.obj = obj;
    }
    // 当我们通过代理类的对象，调用方法a时，就会自动地调用如下的方法：invoke()
    // 将被代理类要执行的方法a的功能就声明在invoke()中
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        HumanUtil humanUtil = new HumanUtil();
        humanUtil.method1();//展示AOP的用法，这里method1和method2是固定的，但是中间的方法method不是确定的
        // method:即为代理类对象调用的方法，此方法也就作为了被代理类对象要调用的方法
        // obj:被代理类的对象
        Object returnValue = method.invoke(obj, args);
        // 上述方法的返回值就作为当前类中invoke()的返回值。

        humanUtil.method2();
        return returnValue;
    }
}

public class ProxyTest {
    public static void main(String[] args) {
        SuperMan superMan = new SuperMan();
        // proxyInstance代理类的对象
        Human proxyInstance = (Human) ProxyFactory.getProxyInstance(superMan);
        // 当通过代理类对象调用方法时，会自动地调用被代理类中同名的方法
        String belief = proxyInstance.getBelief();
        System.out.println(belief);
        proxyInstance.eat("四川麻辣烫");
        System.out.println("==========================");
        NikeClothFactory nikeClothFactory = new NikeClothFactory();
        ClothFactory proxyClothFactory = (ClothFactory) ProxyFactory.getProxyInstance(nikeClothFactory);
        proxyClothFactory.produceCloth();
    }
}
```
