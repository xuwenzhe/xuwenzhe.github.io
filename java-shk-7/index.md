# Java零基础教程（七.多线程）


## 1. 基本概念理解
### 1.1 程序 vs 进程 vs 线程

* 程序（program）是为完成特定任务，用某种语言编写的一组指令的集合。即指**一段静态的代码**，静态对象。
* 进程（process）是程序的一次执行过程，或是**正在运行的一个程序**。是一个动态的过程：有它自身的产生，存在和消亡的过程。 - 生命周期
	- 如：运行中的QQ，运行中的MP3播放器
	- 程序是静态的，进程是动态的
	- **进程作为资源分配的单位**，系统在运行时会为每个进程分配不同的内存区域。
	- 再比如一个运行中的杀毒软件（进程），其中清理文件，查杀木马的运行分别对应一个线程。
* 线程（thread），进程可进一步细化为线程，是一个程序内部的一条执行路径。
	- 若一个进程同一时间**并行**执行多个线程，就是支持多线程的
	- **线程作为调度和执行的单位，每个线程拥有独立的运行栈和程序计数器(pc)**，线程切换的开销小。
	- 一个进程中的多个线程共享相同的内存单元/内存地址空间，他们从同一堆中分配对象，可以访问相同的变量和对象。这就使得线程间通信更简便，高效。但多个线程操作共享的系统资源可能就会带来**安全的隐患**。

{{< figure src="/java-shk/jvm_memory.png" class="center" width="500" >}}



### 1.2 单核CPU vs. 多核CPU

* 单核CPU，其实是一种假的多线程，因为在一个时间单元内，也只能执行一个线程的任务。例如：虽然有多车道，但是收费站只有一个工作人员在收费，只有收了费才能通过，那么CPU就好比收费人员。如果有某个人不想交钱，那么收费人员可以把他“挂起”（晾着他，等他想通了，准备好了钱，再去收费）。但是因为CPU时间单元特别短，因此感觉不出来。
* 如果是多核的话，才能更好的发挥多线程的效率。（现在的服务器都是多核的）
* 一个Java应用程序java.exe，其实至少有三个线程：main()主线程，gc()垃圾回收线程，异常处理线程。当然如果发生异常，会影响主线程。

### 1.3 并行与并发
{{< figure src="/java-shk/f1_change_tires.gif" class="center" width="500" >}}

* 并行：多个CPU同时执行多个任务。比如：多个人同时做不同的事。
* 并发：一个CPU（采用时间片）同时执行多个任务。比如：秒杀，多个人做同一件事。（Quicksilver changes F1 tires?）

### 1.4 多线程的优点
背景：以单核CPU为例，只使用单核线程先后完成多个任务（调用多个方法），肯定比用多个线程来完成用的时间更短，为何仍需多线程呢？

多线程程序的优点：

1. 提高应用程序的响应。对图形化界面更有意义，可增强用户体验。
2. 提高计算机系统CPU的利用率
3. 改善程序结构。将既长又复杂的进程分为多个线程，独立运行，利于理解和修改。

何时需要多线程

* 程序需要同时执行两个或多个任务
* 程序需要实现一些需要等待的任务时，如用户输入，文件读写操作，网络操作，搜索等。
* 需要一些后台运行的程序时。

## 2. 线程的创建和使用

### 2.1 多线程的创建方式一：继承Thread类

1. 创建一个继承于Thread类的子类
2. 重写Thread类的run() -> 将此线程执行的操作声明在run()中
3. 创建Thread类的子类的对象
4. 通过此对象调用start()

```java
package com.atguigu.java;
/*
例子：遍历100以内的所有偶数
 */

// 1. 创建一个继承于Thread类的子类
class MyThread extends Thread {
    // 2. 重写Thread类的run()
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}
public class ThreadTest {
    public static void main(String[] args) {
        // 3. 创建Thread类的子类的对象
        MyThread t1 = new MyThread();
        // 4. 通过此对象调用start(): 1）启动当前线程 2）调用当前线程的run()
        t1.start();
        // 问题一：我们不能通过直接调用run()的方式启动线程。
        // t1.run();
        // 问题二：再启动一个线程，遍历。不可以还让已经start()的线程去执行。会报IllegalThreadStateException
        // t1.start();
        // 我们需要重新创建一个线程的对象
        MyThread t2 = new MyThread();
        t2.start();

        // 如下操作仍然是在main线程中执行的。
        System.out.println(Thread.currentThread().getName());
    }
}
```

匿名子类的方式

```java
package com.atguigu.exer;

public class ThreadDemo {
    public static void main(String[] args) {
        // 创建Thread类的匿名子类的方式
        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    if (i % 2 == 0) {
                        System.out.println(Thread.currentThread().getName() + ":" + i);
                    }
                }
            }
        }.start();

        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    if (i % 2 == 1) {
                        System.out.println(Thread.currentThread().getName() + ":" + i);
                    }
                }
            }
        }.start();
    }
}
```

### 2.2 Thread类中常用的方法

测试Thread中常用方法

1. start()：启动当前线程；调用当前线程的run()
2. run()：通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中
3. currentThread()：静态方法，返回执行当前代码的线程
4. getName()：获取当前线程的名字
5. setName()：设置当前线程的名字
6. yield()：释放当前CPU的执行权
7. join()：在线程a中调用线程b的join(),此时线程a就进入阻塞状态，直到线程b完全执行完以后，线程a才结束阻塞状态。
8. stop()：已过时。当执行此方法时，强制结束此线程
9. sleep(long millitime)：让当前线程"睡眠"指定的毫秒。在指定的毫秒时间内，当前的线程是阻塞状态。
10. isAlive()：判断当前线程是否存活

线程的优先级
1. `MAX_PRIORITY: 10`; `MIN_PRIORITY: 1`; `NORM_PRIORITY: 5`
2. 如何获取和设置当前线程的优先级：
	* `getPriority()`: 获取当前线程的优先级
	* `setPriority(int p)`: 设置线程的优先级

说明： 高优先级的线程抢占低优先级线程CPU的执行权。但是只是从概率上讲，高优先级的线程高概率的情况下被执行。并不意味着只有当高优先级的线程执行完以后，低优先级的线程才执行。

```java
package com.atguigu.java;

public class ThreadMethodTest {
    public static void main(String[] args) {
        HelloThread h1 = new HelloThread("Thread h1");
//        h1.setName("线程一");
        // 设置分线程的优先级
//        h1.setPriority(Thread.MAX_PRIORITY);
        h1.start();
        // 给主线程命名
        Thread.currentThread().setName("主线程");
//        Thread.currentThread().setPriority(Thread.MIN_PRIORITY);
        for (int i = 0; i < 10; i++) {
            if (i % 2 == 1) {
                System.out.println(Thread.currentThread().getName() + ": " + Thread.currentThread().getPriority() + ": "+ i);
            }

            if (i == 5) {
                try {
                    h1.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(h1.isAlive());
    }
}

class HelloThread extends Thread {

    public HelloThread(String name) {
        super(name);
    }

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            if (i % 2 == 0) {
                try {
                    sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + ": " + Thread.currentThread().getPriority() + ": "+ i);
            }

//            if (i % 20 == 0) {
//                yield();
//            }
        }
    }
}
```

例子：用此方式（继承Thread）处理共享数据

```java
package com.atguigu.java;
/*
例子： 创建三个窗口卖票，总票数为100张，使用继承Thread类的方式
存在线程安全问题，待解决...
 */

class Window extends Thread {

    private static int ticket = 100;

    @Override
    public void run() {
        while (true) {
            if (ticket > 0) {
                System.out.println(getName() + "卖票， 票号为：" + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }
}

public class WindowTest {
    public static void main(String[] args) {
        Window t1 = new Window();
        Window t2 = new Window();
        Window t3 = new Window();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
```

### 2.3 多线程的创建方式二：实现Runnable

创建多线程的方式二：实现Runnable接口

1. 创建一个实现Runnable接口的类
2. 实现类去实现Runnable中的抽象方法：run()
3. 创建实现类的对象
4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
5. 通过Thread类的对象调用start()

比较创建线程的两种方式：

开发中，优先选择实现Runnable接口的方式, 原因： 

* 实现的方式没有类的单继承的局限性
* 实现的方式更适合来处理多个线程有共享数据的情况

联系： `public class Thread implements Runnable`

相同点：两种方式都需要重写run()，将线程要执行的逻辑声明在run()中。

```java
package com.atguigu.java;

// 1. 创建一个实现Runnable接口的类
class MThread implements Runnable {
    // 2. 实现类去实现Runnable中的抽象方法：run()

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
            }
        }
    }
}
public class ThreadTest1 {
    public static void main(String[] args) {
        // 3. 创建实现类的对象
        MThread mThread = new MThread();
        // 4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
        Thread t1 = new Thread(mThread);
        t1.setName("线程1");
        // 5. 通过Thread类的对象调用start(): 1)启动线程 2)调用当前线程的run() -> 调用了Runnable类型的target
        t1.start();

        // 再启动一个线程
        Thread t2 = new Thread(mThread);
        t2.setName("线程2");
        t2.start();
    }
}
```

两种方式的对比

```java
package com.atguigu.java;
/*
例子： 创建三个窗口卖票，总票数为100张，使用实现Runnable接口的方式
 */
class Window1 implements Runnable {
    // 不需要static：因为堆空间中只创建了一个对象，多个线程共享同一个对象
    private int ticket = 100;
    @Override
    public void run() {
        while (true) {
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + "卖票，票号为：" + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }
}
public class WindowTest1 {
    public static void main(String[] args) {
        Window1 w = new Window1();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}
```

## 3. 线程的生命周期

{{< figure src="/java-shk/thread_lifecycle.png" class="center" width="500" >}}

区分“阻塞”状态与“就绪”状态（yield）：阻塞是指CPU想执行也执行不了的状态。


## 4. 线程的同步
### 4.1 Java的同步机制

例子：创建三个窗口卖票，总票数100张，使用实现Runnable接口的方式

1. 问题：卖票过程中，出现了重票和错票 -> 出现了线程的安全问题
2. 问题出现的原因：当某个线程操作车票的过程中，尚未操作完成时，其他线程参与进来，也操作车票。
3. 如何解决：当一个线程在操作ticket的时候，其他线程不能参与进来。直到线程a操作完ticket时，其他线程才可以开始操作ticket。这种情况即使线程a出现了阻塞，也不能被改变。
4. 在Java中，我们通过同步机制，来解决线程的安全问题。

* 方式一：同步代码块
	
		synchronized(同步监视器) {
		   // 需要被同步的代码
		}

	说明：

	- 操作共享数据的代码，即为需要被同步的代码 -- 不能包含代码多了，也不能包含代码少了
	- 共享数据：多个线程共同操作的变量。比如：ticket就是共享数据。
	- 同步监视器，俗称：锁。任何一个类的对象，都可以充当锁。锁的要求：多个线程必须要共用同一把锁。

    补充：在实现Runnable接口创建多线程的方式中，我们可以考虑使用this充当同步监视器

* 方式二：同步方法

	如果操作共享数据的代码完整的声明在一个方法中，我们不妨将此方法声明同步的。

5. 同步的方式，解决了线程的安全问题。-- 好处

   操作同步代码时，只能有一个线程参与，其他线程等待。相当于是一个单线程的过程，效率低。 -- 坏处

对于两种线程创建方式（继承Thread类，实现Runnable接口），各有两种解决线程安全的方案，即同步代码块和同步方法。四种情况组合如下：

#### 同步代码块，Runnable

```java
package com.atguigu.java;

/*


 */
class Window1 implements Runnable {
    private int ticket = 100;
    Object obj = new Object();

    @Override
    public void run() {
        // Object obj = new Object(); // 线程还是不安全，这里没有共用一把锁
        while (true) {
            synchronized (this) {// 此时的this：唯一的Window1的对象
//            synchronized (obj) {// 正确
                if (ticket > 0) {

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + "卖票，票号：" + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
public class WindowTest1 {
    public static void main(String[] args) {
        Window1 w = new Window1();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}
```

#### 同步代码块，继承Thread

```java
package com.atguigu.java;
/*
使用同步代码块解决继承Thread类的方式的线程安全问题

例子：创建三个窗口卖票，总票数100张，使用继承Thread类的方式

说明：在继承Thread类创建多线程的方式中，慎用this充当同步监视器，考虑使用当前类充当同步监视器。
 */
class Window2 extends Thread {
    private static int ticket = 100;
    // 注意static锁
    private static Object obj = new Object();

    @Override
    public void run() {
        while (true) {
//            synchronized (obj) {
//            synchronized (this) {// 错误，this代表着t1,t2,t3三个对象
            synchronized (Window2.class) {// Class clazz = Window2.class, Window2.class只会加载一次
                if (ticket > 0) {

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(getName() + "卖票，票号：" + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
public class WindowTest2 {
    public static void main(String[] args) {
        Window2 t1 = new Window2();
        Window2 t2 = new Window2();
        Window2 t3 = new Window2();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}

```

#### 同步方法，实现Runnable

```java
package com.atguigu.java;

/*
使用同步方法解决实现Runnable接口的线程安全问题

关于同步方法的总结：
1。 同步方法仍然涉及到同步监视器，只是不需要我们显式地声明。
2。 非静态的同步方法，同步监视器是：this
3。 静态的同步方法，同步监视器是：当前类本身

 */
class Window3 implements Runnable {
    private int ticket = 100;

    @Override
    public void run() {
        while (true) {
            show();
        }
    }

    private synchronized void show() { // 同步监视器：this
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "卖票，票号：" + ticket);
            ticket--;
        }
    }
}
public class WindowTest3 {
    public static void main(String[] args) {
        Window3 w = new Window3();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}

```

#### 同步方法，继承Thread

```java
package com.atguigu.java;

class Window4 extends Thread {
    private static int ticket = 100;

    @Override
    public void run() {
        while (true) {
            show();
        }
    }

    private static synchronized void show() { // 正确，static共享锁，同步监视器：Windows4.class
//    private synchronized void show() { // 错误，同步监视器：t1,t2,t3
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "卖票，票号：" + ticket);
            ticket--;
        }
    }
}
public class WindowTest4 {
    public static void main(String[] args) {
        Window4 t1 = new Window4();
        Window4 t2 = new Window4();
        Window4 t3 = new Window4();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
```

### 4.2 懒汉式同步机制回顾

```java
package com.atguigu.java1;
/*
使用同步机制将单例模式中的懒汉式改写为线程安全的
 */
public class BankTest {
}

class Bank {
    private Bank(){}

    private static Bank instance = null;

    public static Bank getInstance() {
        // 方式一：效率稍差
//        synchronized (Bank.class) {
//            if (instance == null) {
//                instance = new Bank();
//                return instance;
//            }
//        }
        // 方式二：效率更高
        if (instance == null) {
            synchronized (Bank.class) {
                if (instance == null) {
                    instance = new Bank();
                }
            }
        }
        return instance;
    }
}
```


### 4.3 死锁问题

不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。（例子：两人吃饭，一人拿一根筷子）

演示线程的死锁问题
1. 死锁的理解：不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。
2. 说明：
	* 出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续
	* 我们使用同步时，要避免出现死锁。

解决方法：

* 专门的算法，原则
* 尽量减少同步资源的定义
* 尽量避免嵌套同步

```java
package com.atguigu.java1;

public class ThreadTest {
    public static void main(String[] args) {
        StringBuffer s1 = new StringBuffer();
        StringBuffer s2 = new StringBuffer();

        new Thread(){
            @Override
            public void run() {
                synchronized (s1) {
                    s1.append("a");
                    s2.append("1");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    synchronized (s2) {
                        s1.append("b");
                        s2.append("2");

                        System.out.println(s1);
                        System.out.println(s2);
                    }
                }
            }
        }.start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (s2) {
                    s1.append("c");
                    s2.append("3");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    synchronized (s1) {
                        s1.append("d");
                        s2.append("4");

                        System.out.println(s1);
                        System.out.println(s2);
                    }
                }
            }
        }).start();
    }
}
```

### 4.4 第三种同步机制（JDK5.0新增）- Lock

解决线程安全问题的方式三：Lock锁 --- JDK5.0新增
1. 面试题：synchronized 与 Lock的异同？
    
    同：二者都可以解决线程安全问题
    
    不同：synchronized机制在执行完相应的同步代码以后，自动地释放同步监视器
        Lock需要手动地启动同步lock(),同时结束同步也需要手动地实现unlock()

优先使用顺序：Lock -> 同步代码块（已经进入了方法体，分配了相应资源） -> 同步方法（在方法体之外）

2. 面试题：如何解决线程安全问题？有几种方式？synchronized， Lock

```java
package com.atguigu.java1;

import java.util.concurrent.locks.ReentrantLock;

class Window implements Runnable {
    private int ticket = 100;
    // 1. 实例化ReentrantLock
    private ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            try {
                // 2. 调用锁定方法lock()方法
                lock.lock();

                if (ticket > 0) {

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + "售票，票号：" + ticket);
                    ticket--;
                } else {
                    break;
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // 3. 调用解锁方法：unlock()
                lock.unlock();
            }
        }
    }
}

public class LockTest {
    public static void main(String[] args) {
        Window w = new Window();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.start();
        t2.start();
        t3.start();

    }
}
```

一道练习

```java
package com.atguigu.exer;

class Account {
    private double balance;

    public Account(double balance) {
        this.balance = balance;
    }

    public synchronized void deposit(double amt) {
        if (amt > 0) {
            balance += amt;
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "存钱成功，余额：" + balance);
        }
    }
}

class Customer extends Thread {
    private Account acct;

    @Override
    public void run() {
        for (int i = 0; i < 3; i++) {
            acct.deposit(1000);
        }
    }

    public Customer(Account acct) {
        this.acct = acct;
    }
}
public class AccountTest {
    public static void main(String[] args) {
        Account acct = new Account(0);
        Customer c1 = new Customer(acct);
        Customer c2 = new Customer(acct);

        c1.setName("c1");
        c2.setName("c2");

        c1.start();
        c2.start();
    }
}
```

## 5. 线程的通信

线程通信的例子：使用两个线程打印1-100。线程1，线程2交替打印

涉及到的三个方法：

* wait()：一旦执行此方法，当前线程就进入阻塞状态，并释放同步监视器（锁）
* notify()：一旦执行此方法，就会唤醒被wait的一个线程。如果有多个线程被wait，就唤醒优先级高的线程
* notifyAll()：一旦执行此方法，就会唤醒所有被wait的线程。

说明：
* wait(), notify(), notifyAll()三个方法必须使用在同步代码块或同步方法中。
* wait(), notify(), notifyAll()三个方法的调用者必须是同步代码块或同步方法中的同步监视器。否则，会出现IllegalMonitorStateException异常
* wait(), notify(), notifyAll()三个方法是定义在java.lang.Object类中

面试题：sleep()和wait()的异同？

* 相同点：一旦执行方法，都可以使得当前的线程进入阻塞状态
* 不同点
    - 两个方法声明的位置不同：Thread类中声明sleep(), Object类中声明wait()
    - 调用的要求不同：sleep()可以在任何需要的场景下调用。wait()必须使用在同步代码块或同步方法中
    - 关于是否释放同步监视器（锁）：如果两个方法都使用在同步代码块或同步方法中，sleep()不会释放锁，wait()会释放锁。


```java
package com.atguigu.java2;

class Number implements Runnable {
    private int number = 1;
    private Object obj = new Object();

    @Override
    public void run() {
        while (true) {
            synchronized (obj) {
                obj.notify();
                if (number < 100) {
                    System.out.println(Thread.currentThread().getName() + " : " + number);
                    number++;

                    try {
                        // 使得调用如下wait()方法的线程进入阻塞状态
                        obj.wait();// 会释放锁。作为对比，sleep不会释放锁
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else {
                    break;
                }
            }
        }
    }
}
public class CommunicationTest {

    public static void main(String[] args) {
        Number number = new Number();
        Thread t1 = new Thread(number);
        Thread t2 = new Thread(number);

        t1.setName("线程1");
        t2.setName("线程2");

        t1.start();
        t2.start();
    }
}
```

看一道综合的例题：生产者/消费者问题

```java
package com.atguigu.java2;
/*
线程通信的应用：经典例题：生产者/消费者问题

生产者（Productor）将产品交给店员（Clerk），而消费者（Customer）从店员处取走产品，店员一次只能持有固定数量的产品（比如：20），
如果生产者试图生产更多的产品，店员会叫生产者停一下，如果店中有空位放产品了再通知生产者继续生产；如果店中没有产品了，店员会告诉
消费者等一下，如果店中有产品了再通知消费者来取走产品。

分析：
1. 是否有多线程问题？是，生产者线程，消费者线程
2. 是否有共享数据？是，店员（或产品）
3. 如何解决线程的安全问题？同步机制，有三种方法
4. 是否涉及到线程通信？是
 */
class Clerk {
    private int productCount = 0;
    // 生产产品
    public synchronized void produceProduct() {
        if (productCount < 20) {
            productCount++;
            System.out.println(Thread.currentThread().getName() + "开始生产第" + productCount + "个产品");

            notify();
        } else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    // 消费产品
    public synchronized void consumeProduct() {
        if (productCount > 0) {
            System.out.println(Thread.currentThread().getName() + "开始消费第" + productCount + "个产品");
            productCount--;

            notify();
        } else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Producer extends Thread {
    private Clerk clerk;

    public Producer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "开始生产产品...");
        while (true) {
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.produceProduct();
        }
    }
}

class Consumer extends Thread {
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "开始消费产品...");
        while (true) {
            try {
                Thread.sleep(20);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.consumeProduct();
        }
    }
}
public class ProductTest {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();

        Producer p1 = new Producer(clerk);
        p1.setName("生产者1");

        Consumer c1 = new Consumer(clerk);
        c1.setName("消费者1");

        Consumer c2 = new Consumer(clerk);
        c2.setName("消费者2");

        p1.start();
        c1.start();
        c2.start();
    }
}
```


## 6. JDK5.0新增线程创建方式

### 6.1 多线程的创建方式三：实现Callable接口（JDK5.0新增）

如何理解实现Callable接口的方式创建多线程比实现Runnable接口创建线程方式强大？

1. call()可以有返回值的。
2. call()可以抛出异常，被外面的操作捕获，获取异常的信息
3. Callable是支持范型的

使用步骤：

1. 创建一个实现Callable的实现类
2. 实现call方法，将此线程需要执行的操作声明在call()中
3. 创建Callable接口实现类的对象
4. 将此Callable接口实现类的对象作为参数传递到FutureTask构造器中，创建FutureTask的对象
5. 将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()。
6. (optional) 获取Callable中call方法的返回值

```java
package com.atguigu.java2;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

class NumThread implements Callable {
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i < 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}

public class ThreadNew {
    public static void main(String[] args) {
        NumThread numThread = new NumThread();
        FutureTask futureTask = new FutureTask(numThread);
        new Thread(futureTask).start();
        try {
            // get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值
            Object sum = futureTask.get();
            System.out.println("总和为：" + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

### 6.2 多线程的创建方式四：线程池


线程池

* 背景：经常创建和销毁使用量特别大的资源，比如并发情况下的线程，对性能影响很大。
* 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁，实现重复利用。类似生活中的公共交通工具。
* 好处：
	- 提高响应速度（减少了创建新线程的时间）
	- 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
	- 便于线程管理
		* corePoolSize：核心池的大小
		* maximumPoolSize：最大线程数
		* keepAliveTime：线程没有任务时最多保持多长时间后会终止

开发中，一般都使用线程池的方式创建和管理线程

面试题：创建多线程有几种方式？四种！

```java
package com.atguigu.java2;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class NumberThread implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName()+ " : " + i);
            }
        }
    }
}

class NumberThread1 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + " : " + i);
            }
        }
    }
}
public class ThreadPool {
    public static void main(String[] args) {
        // 1. 提供指定线程数量的线程池
        // 2. 执行指定的线程的操作。需要提供实现Runnable接口或Callable接口实现类的对象
        ExecutorService service = Executors.newFixedThreadPool(10);

        // 设置线程池的属性
        System.out.println(service.getClass()); // ThreadPoolExecutor
//        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;// 向下转型
//        service1.setCorePoolSize(15);// 线程池的管理

        service.execute(new NumberThread());//适用于Runnable
        service.execute(new NumberThread1());
//        service.submit(Callable callable);//适用于Callable
        // 3. 关闭线程池
        service.shutdown();
    }
}
```






















