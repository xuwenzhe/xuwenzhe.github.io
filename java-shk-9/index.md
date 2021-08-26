# Java零基础教程（九.泛型）


## 1. 泛型的使用

jdk5.0新增的特性

### 1.1. 在集合中使用泛型：
总结：
    
1. 集合接口或集合类在jdk5.0时都修改为带泛型的结构
2. 在实例化集合类时，可以指明具体的泛型类型
3. 指明完以后，在集合类或接口中凡是定义类或接口时，内部结构（比如：方法，构造器，属性）使用到类的泛型的位置，都指定为实例化的泛型类型。 比如：add(E e) ---> 实例化以后：add(Integer e)
4. 注意点：泛型的类型必须是类，不能是基本数据类型。需要用到基本数据类型的位置，拿包装类来替换
5. 如果实例化时，没有指明范型的类型，默认类型为java.lang.Object类型

```java
public class GenericTest {

    // 在集合中使用泛型
    @Test
    public void test1() {
        ArrayList list = new ArrayList();
        list.add(78);
        list.add(76);
        list.add(89);
        list.add(88);
        // 问题1： 类型不安全
//        list.add("Tom");
        for (Object score: list) {
            int stuScore = (Integer) score;
            System.out.println(stuScore);
        }
    }

    // 在集合中使用泛型的情况：
    @Test
    public void test2() {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(78);
        list.add(76);
        list.add(89);
        list.add(88);
        // 编译时，就会进行类型检查，保证数据的安全
//        list.add("Tom");
        for (Integer score : list) {
            // 避免了强转操作
            System.out.println(score);
        }
        // 方式二：
        Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    @Test
    public void test3() {
        Map<String, Integer> map = new HashMap<String, Integer>();
        map.put("Tom", 87);
        map.put("Jerry", 87);

        // 泛型的嵌套
        Set<Map.Entry<String, Integer>> entry = map.entrySet();
        // option + enter 自动补全
        Iterator<Map.Entry<String, Integer>> iterator = entry.iterator();
    }

    @Test
    public void test4() {
        // 如果定义了泛型类，实例化没有指明类的泛型，则认为此泛型类型为Object类型
        // 要求：如果大家定义了类是带泛型的，建议在实例化时要指明类的泛型
        Order order = new Order();
        order.setOrderT(123);
        order.setOrderT("ABC");

        // 建议：实例化时指明类的泛型
        Order<String> orderAA = new Order<>("orderAA", 1001, "order:AA");
        orderAA.setOrderT("AA:hello");
    }

    @Test
    public void test5() {
        SubOrder sub1 = new SubOrder();
        // 由于子类在继承带泛型的父类，指明了泛型类型。则实例化子类对象时，不再需要指明泛型。
        sub1.setOrderId(1122);

        SubOrder1<String> sub2 = new SubOrder1<>();
    }

    @Test
    public void test6() {
        ArrayList<String> list1 = null;
        ArrayList<Integer> list2 = null;
        // 泛型不同的引用不能相互赋值
//        list1 = list2
    }

    // 测试泛型方法
    @Test
    public void test7() {
        Order<String> order = new Order<>();
        Integer[] arr = new Integer[]{1,2,3,4};
        // 泛型方法在调用时，指明泛型参数的类型
        List<Integer> list = order.copyFromArrayToList(arr);
        System.out.println(list);
    }
}
```

### 1.2 如何自定义泛型结构：泛型类，泛型接口；泛型方法

泛型类，泛型接口

泛型方法


### 1.3 泛型在继承方面的体现

虽然类A是类B的父类，但是`G<A>`和`G<B>`二者不具备子父类关系，二者是并列关系
补充：类A是类B的父类，`A<G>`是`B<G>`的父类

### 1.4 通配符

通配符：？
类A是类B的父类，`G<A>`和`G<B>`是没有关系的，二者共同的父类是：`G<?>`

有限制条件的通配符的使用

* `? extends A: G<? extends A>`可以作为`G<A>`和`G<B>`的父类，其中B是A的子类，相当于小于等于
* `? super A: G<? super A>`可以作为`G<A>`和`G<B>`的父类，其中B是A的父类，相当于大于等于


```java
package com.atguigu.java2;

import org.junit.Test;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class GenericTest {
    @Test
    public void test1() {
        Object obj = null;
        String str = null;
        obj = str;

        Object[] arr1 = null;
        String[] arr2 = null;
        arr1 = arr2;

        List<Object> list1 = null;
        List<String> list2 = null;
        // 此时的list1和list2不具备子父类关系
//        list1 = list2;// 编译不通过
        /*
        反证法：
        假设list1 = list2
        list1.add(123)导致混入非String的数据，出错
         */

//        show(list1);//编译不通过
        show(list2);
    }

    public void show(List<String> list){

    }
    @Test
    public void test2() {
        List<String> list1 = null;
        ArrayList<String> list2 = null;
        list1 = list2;
    }

    @Test
    public void test3() {
        List<Object> list1 = null;
        List<String> list2 = null;

        List<?> list = null;
        list = list1;
        list = list2;

        List<String> list3 = new ArrayList<>();
        list3.add("AA");
        list3.add("BB");
        list3.add("CC");
        list = list3;
        // 添加：对于List<?>就不能向其内部添加数据
        // 除了添加null
//        list.add("DD");
        list.add(null);
        // 获取（读取）：允许读取数据，读取的数据类型为Object
        Object o = list.get(0);
        System.out.println(o);
    }

    public void print(List<?> list) {
        Iterator<?> iterator = list.iterator();
        while (iterator.hasNext()) {
            Object obj = iterator.next();
            System.out.println(obj);
        }
    }

    @Test
    public void test4() {
        List<? extends Person> list1 = null;
        List<? super Person> list2 = null;
        List<Student> list3 = null;
        List<Person> list4 = null;
        List<Object> list5 = null;

        list1 = list3;
        list1 = list4;
//        list1 = list5; // 编译不通过

        list2 = list4;
//        list2 = list3;//编译不通过
//        list1.add(new Student());//编译不通过
        list2.add(new Person());//编译通过
        list2.add(new Student());//编译通过
    }
}

```
















