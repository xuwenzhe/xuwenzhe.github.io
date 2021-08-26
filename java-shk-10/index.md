# Java零基础教程（十.IO流）


## 1. File类

File类的使用

1. File类的一个对象，代表一个文件或一个文件目录（俗称：文件夹）
2. File类的声明在java.io包下
3. File类中涉及到关于文件或文件目录的创建，删除，重命名，修改时间，文件大小等方法。并未涉及到写入或读取文件内容的操作。如果需要读取或写入文件内容，必须使用IO流来完成
4. 后续File类的对象常会作为参数传递到流的构造器中，指明读取或写入的"终点"

### 1.1 File类的创建

1. 如何创建File类的实例

	    File(String filePath)
	    File(String parentPath, String childPath)
	    File(File parentPath, String childPath)
2.
	* 相对路径：相较于某个路径下，指明的路径
	* 绝对路径：包含盘符在内的文件或文件目录的路径

3. 路径分隔符
windows:`\\`
unix:`/`

### 1.2 File类的API

	public String getAbsolutePath():获取绝对路径
	public String getPath():获取路径
	public String getName():获取名称
	public String getParent():获取上层文件目录路径。若无，返回null
	public long length():获取文件长度（即，字节数）。不能获取目录的长度
	public long lastModified():获取最后一次的修改时间，毫秒值
	如下的两个方法适用于文件目录
	public String[] list():获取指定目录下的所有文件或者文件目录的名称数组
	public File[] listFiles:获取指定目录下的所有文件或者文件目录的File数组
	public boolean renameTo(File dest):把文件重命名为指定的文件路径
    比如：file1.renameTo(file2)为例：
        要想保证返回true，需要file1在硬盘中是存在的，且file2不能在硬盘中存在
    public boolean isDirectory():判断是否是文件目录
    public boolean isFile():判断是否是文件
    public boolean exists():判断是否存在
    public boolean canRead():判断是否可读
    public boolean canWrite():判断是否可写
    public boolean isHidden():判断是否隐藏

    创建硬盘中对应的文件或文件目录
    public boolean createNewFile():创建文件。若文件存在，则不创建，返回false
    public boolean mkdir():创建文件目录。如果此文件目录存在，就不创建了。
    						如果此文件目录的上层目录不存在，也不创建
    public boolean mkdirs():创建文件目录。如果上层文件目录不存在，一并创建
    删除磁盘中的文件或文件目录
    public boolean delete():删除文件或者文件夹
    删除注意事项：
    Java中的删除不走回收站


```java
package com.atguigu.java3;

import org.junit.Test;

import java.io.File;
import java.io.IOException;
import java.util.Date;

public class FileTest {

    @Test
    public void test1() {
        // 构造器1
        File file1 = new File("hello.txt");//相对于当前module
        System.out.println(file1);
    }

    @Test
    public void test2() {
        File file1 = new File("hello.txt");
        File file2 = new File("/Users/wenzhe/Desktop/test_java_io.txt");

        System.out.println(file1.getAbsoluteFile());
        System.out.println(file1.getPath());
        System.out.println(file1.getName());
        System.out.println(file1.getParent());
        System.out.println(file1.length());
        System.out.println(new Date(file1.lastModified()));

        System.out.println();

        System.out.println(file2.getAbsoluteFile());
        System.out.println(file2.getPath());
        System.out.println(file2.getName());
        System.out.println(file2.getParent());
        System.out.println(file2.length());
        System.out.println(file2.lastModified());
    }

    @Test
    public void test3() {
        File file = new File("/Users/wenzhe/IdeaProjects/JavaSenior");
        String[] list = file.list();
        for (String s : list) {
            System.out.println(s);
        }

        File[] files = file.listFiles();
        for(File f : files) {
            System.out.println(f);
        }
    }

    @Test
    public void test6() throws IOException {
        File file1 = new File("hi.txt");
        if (!file1.exists()) {
            file1.createNewFile();
            System.out.println("创建成功");
        } else {
            file1.delete();
            System.out.println("删除成功");
        }
    }

    @Test
    public void test7() {
        //文件目录
        File file1 = new File()
    }
}

```
## 2. 节点流（文件流）

### 2.1 流的分类
1. 操作数据单位：字节流，字符流
2. 数据的流向：输入流，输出流
3. 流的角色；节点流，处理流

### 2.2 流的体系结构
	抽象基类        节点流(或文件流)                                       缓冲流（处理流的一种）
	InputStream   FileInputStream (read(byte[] buffer))                BufferedInputStream (read(byte[] buffer))
	OutputStream  FileOutputStream (write(byte[] buffer, 0, len))      BufferedOutputStream (write(byte[] buffer, 0, len)) / flush()
	Reader        FileReader (read(char[] cbuf))                       BufferedReader (read(char[] cbuf) / readLine())
	Writer        FileWriter (write(char[] cbuf, 0, len))              BufferedWriter (write(char[] cbuf, 0, len)) / flush()

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class FileReaderWriterTest {

    public static void main(String[] args) {
        File file = new File("day09/hello.txt");
        System.out.println(file.getAbsolutePath());
    }

    /*
    将day09下的hello.txt文件内容读入程序中
    说明点：
    1. read(): 返回的读入的一个字符，如果达到文件末尾，返回-1
    2. 异常的处理：为了保证流资源一定可以执行关闭操作。需要使用try-catch-finally处理
    3. 读入的文件一定要存在，否则就会报FileNotFoundException
     */
    @Test
    public void testFileReader() {
        FileReader fr = null;
        try {
            // 1. 实例化File类的对象，指明要操作的文件
            File file = new File("hello.txt");//相较于当前Module
            // 2. 提供具体的流
            fr = new FileReader(file);
            // 3. 数据的读入
            // read(): 返回的读入的一个字符，如果达到文件末尾，返回-1
            // 方式一
//        int data = fr.read();
//        while (data != -1) {
//            System.out.print((char)data);
//            data = fr.read();
//        }
            // 方式二
            int data;
            while ((data = fr.read()) != -1) {
                System.out.print((char)data);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4. 流的关闭操作
            if (fr != null) {
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    // 对read()操作升级：使用read的重载方法
    @Test
    public void testFileReader1() {
        FileReader fr = null;
        try {
            // 1. File类的实例化
            File file = new File("hello.txt");
            // 2. FileReader流的实例化
            fr = new FileReader(file);
            // 3. 读入的操作
            // read(char[] cbuf):返回每次读入cbuf数组中的字符的个数。如果达到文件末尾，返回-1
            char[] cbuf = new char[5];
            int len;
            while ((len = fr.read(cbuf)) != -1) {
                // 方式一
//                for (int i = 0; i < len; i++) {
//                    System.out.print(cbuf[i]);
//                }
                // 方式二
                String str = new String(cbuf, 0, len);
                System.out.print(str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fr != null) {
                // 4. 资源的关闭
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }


    /*
     从内存中写出数据到硬盘的文件里
     说明：
     1。输出操作，对应的File可以不存在的，并不会报异常
     2。File对应的硬盘中的文件如果不存在，在输出的过程中，会自动创建此文件
        File对应的硬盘中的文件如果存在：
            如果流使用的构造器是: FileWriter(file, false) / FileWriter(file):对原有文件的覆盖
            如果流使用的构造器是：FileWriter(file, true): 不会对原有文件覆盖，而是在原有文件基础上追加内容
     */

    @Test
    public void testFileWriter() throws IOException {
        // 1. 提供File类的对象，指明写出到的文件
        File file = new File("hello.txt");
        // 2. 提供FileWriter的对象，用于数据的写出
        FileWriter fw = new FileWriter(file, true);
        // 3. 写出的操作
        fw.write("I have a dream!\n");
        fw.write("you need to have a dream!");
        // 4. 流资源的关闭
        fw.close();
    }


    // 文件的复制
    @Test
    public void testFileReaderFileWriter() {
        FileReader fr = null;
        FileWriter fw = null;
        try {
            // 1. 创建File类的对象，指明读入和写出的文件
            File srcFile = new File("hello.txt");
            File destFile = new File("hello2.txt");
            // 2. 创建输入流和输出流的对象
            fr = new FileReader(srcFile);
            fw = new FileWriter(destFile);
            // 3. 数据的读入和写出操作
            char[] cbuf = new char[5];
            int len; //记录每次读入到cbuf数组中的字符的个数
            while ((len = fr.read(cbuf)) != -1) {
                // 每次写出len个字符
                fw.write(cbuf, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4. 关闭流资源
            try {
                if (fw != null)
                    fw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (fr != null)
                    fr.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}

```

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

/*
测试FileInputStream和FileOutputStream的使用

结论：
1. 对于文本文件(.txt, .java, .c, .cpp)，使用字符流处理
2. 对于非文本文件(.jpg, .mp3, .mp4, .avi, .doc, .ppt, ...)，使用字节流处理


 */
public class FileInputOutputStreamTest {

    // 使用字节流FileInputStream处理文本文件，可能出现乱码
    @Test
    public void testFileInputStream() {
        FileInputStream fis = null;
        try {
            // 1. 造文件
            File file = new File("hello.txt");
            // 2. 造流
            fis = new FileInputStream(file);

            // 3. 读数据
            byte[] buffer = new byte[5];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                String str = new String(buffer,0, len);
                System.out.print(str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fis != null)
                    fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 3. 缓冲流
1. 缓冲流(处理流之一)的使用
	
* BufferedInputStream
* BufferedOutputStream
* BufferedReader
* BufferedWriter

2. 作用：提高流的读取，写入的速度
提高读写速度的原因：内部提供了一个缓冲区

3. 处理流，就是"套接"在已有的流的基础上。

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.*;

public class BufferedTest {

    /*
    实现非文本文件的复制
     */
    @Test
    public void BufferedStreamTest() {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            // 1. 造文件
            File srcFile = new File("sf_skyline.jpeg");
            File destFile = new File("sf_skyline_test.jpeg");
            // 2. 造流
            // 2.1 造节点流
            FileInputStream fis = new FileInputStream(srcFile);
            FileOutputStream fos = new FileOutputStream(destFile);
            // 2.2 造缓冲流
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);

            // 3. 复制的细节：读取写入
            byte[] buffer = new byte[10];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
//                bos.flush(); // 刷新缓冲区
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4. 资源关闭
            // 要求1：先关闭外层的流，再关闭内层的流
            try {
                if (bos != null)
                    bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (bis != null)
                    bis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            // 说明：关闭外层流的同时，内层流也会自动的进行关闭。关于内层流的关闭，我们可以省略
//        fos.close();
//        fis.close();
        }
    }

    // 实现文件复制的方法
    public void copyFileWithBuffered(String srcPath, String destPath) {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            // 1. 造文件
            File srcFile = new File(srcPath);
            File destFile = new File(destPath);
            // 2. 造流
            // 2.1 造节点流
            FileInputStream fis = new FileInputStream(srcFile);
            FileOutputStream fos = new FileOutputStream(destFile);
            // 2.2 造缓冲流
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);

            // 3. 复制的细节：读取写入
            byte[] buffer = new byte[1024];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4. 资源关闭
            // 要求1：先关闭外层的流，再关闭内层的流
            try {
                if (bos != null)
                    bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (bis != null)
                    bis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            // 说明：关闭外层流的同时，内层流也会自动的进行关闭。关于内层流的关闭，我们可以省略
//        fos.close();
//        fis.close();
        }
    }

    @Test
    public void testCopyFileWithBuffered() {
        long start = System.currentTimeMillis();
        String srcPath = "sf_skyline.jpeg";
        String destPath = "sf_skyline_test.jpeg";
        copyFileWithBuffered(srcPath, destPath);
        long end = System.currentTimeMillis();
        System.out.println("Copy took: " + (end - start) + " millis");
    }
}

```

## 4. 转换流

处理流之二：转换流的使用

1. 转换流：属于字符流

		InputStreamReader：将一个字节的输入流转换为字符的输入流
		OutputStreamWriter：将一个字符流的输出流转换为字节的输出流

2. 作用：提供字节流与字符流之间的转换

3. 编码与解码

		解码： 字节，字节数组 ---> 字符数组，字符串
		编码： 字符数组，字符串 ---> 字节，字节数组

4. 字符集
* ASCII:美国标准信息交换码，用一个字节的7位可以表示
* ISO8859-1:拉丁码表。欧洲码表，用一个字节的8位表示
* GB2312:中国的中文编码表。最多两个字节编码所有字符
* GBK:中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码
* Unicode:国际标准码，融合了目前人类使用的所有字符。为每个字符分配唯一的字符码。所有的文字都用两个字节来表示
* UTF-8:变长的编码方式，可用1-4个字节来表示一个字符

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.*;

public class InputStreamReaderTest {

    /*
    此时处理异常的话，仍然应该使用try-catch-finally
     */
    @Test
    public void test1() throws IOException {
        FileInputStream fis = new FileInputStream("test.txt");
//        InputStreamReader isr = new InputStreamReader(fis); // 使用系统默认的字符集
        // 参数2指明了字符集，具体使用哪个字符集，取决于文件保存时使用的字符集
        InputStreamReader isr = new InputStreamReader(fis, "UTF-8");

        char[] cbuf = new char[20];
        int len;
        while ((len = isr.read(cbuf)) != -1) {
            String str = new String(cbuf, 0, len);
            System.out.print(str);
        }
    }

    @Test
    public void test2() throws IOException {
        // 1. 造文件，造流
        InputStreamReader isr = new InputStreamReader(new FileInputStream(new File("test.txt")), "utf-8");
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(new File("test_gbk.txt")), "gbk");

        // 2. 读写过程
        char[] cbuf = new char[20];
        int len;
        while ((len = isr.read(cbuf)) != -1) {
            osw.write(cbuf, 0, len);
        }
        // 3. 关闭资源
        isr.close();
        osw.close();
    }
}
```

## 5. 其他流

### 5.1 标准输入流输出流

    
* 标准的输入，输出流
	* System.in: 标准的输入流，默认从键盘输入
    * System.out: 标准的输出流，默认从控制台输出
    * System类的setIn(InputStream is) / setOut(PrintStream ps)方式重新指定输入和输出的流

* 练习
从键盘输入字符串，要求将读取到的整行字符串转成大写输出。然后继续进行输入操作，直至当输入"e"或者"exit"时，退出程序
    
    * 方法一：使用scanner实现，调用next()返回一个字符串
    * 方法二：使用System.in 实现 System.in ---> 转换流 ---> BufferedReader的readLine()
     

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.*;

public class OtherStreamTest {

    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            InputStreamReader isr = new InputStreamReader(System.in);
            br = new BufferedReader(isr);
            while (true) {
                System.out.println("请输入字符串");
                String data = br.readLine();
                if (data.equalsIgnoreCase("e") || data.equalsIgnoreCase("exit")) {
                    System.out.println("程序结束");
                    break;
                }
                System.out.println(data.toUpperCase());
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

### 5.2 打印流

```java
    @Test
    public void test2() {
        PrintStream ps = null;
        try {
            FileOutputStream fos = new FileOutputStream(new File("test_print_stream.txt"));
            ps = new PrintStream(fos, true);
            if (ps != null) {
                System.setOut(ps);
            }
            for (int i = 0; i < 255; i++) {
                System.out.print((char) i);
                if (i % 50 == 0) {
                    System.out.println();
                }
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ps != null) {
                ps.close();
            }
        }
    }

```

### 5.3 数据流

* DataInputStream 和 DataOutputStream
* 作用：用于读取或写出基本数据类型的变量或字符串

练习：将内存中的字符串，基本数据类型的变量写出到文件中

注意：处理异常的话，仍然应该使用try-catch-finally

```java
/*
   
    @Test
    public void test3() throws IOException {
        DataOutputStream dos = new DataOutputStream(new FileOutputStream("test_data_stream.txt"));
        dos.writeUTF("abc");
        dos.flush();//刷新操作，将内存中的数据写入文件
        dos.writeInt(123);
        dos.flush();
        dos.writeBoolean(true);
        dos.flush();
        dos.close();
    }

    /*
    将文件中存储的基本数据类型变量和字符串读取到内存中，保存在变量中
    注意点：读取不同类型的数据的顺序要与当初写入文件时，保存的数据的顺序保持一致。
     */
    @Test
    public void test4() throws IOException {
        DataInputStream dis = new DataInputStream(new FileInputStream("test_data_stream.txt"));
        String s = dis.readUTF();
        int i = dis.readInt();
        boolean b = dis.readBoolean();
        System.out.println(s + i + b);
        dis.close();
    }
}
```

### 5.4 对象流

对象流的使用

1. ObjectInputStream 和 ObjectOutputStream
2. 作用：用于存储和读取基本数据类型数据或对象的处理流。它的强大之处就是可以把java中的对象转化为二进制流。
3. 要想一个java对象是可序列化的，需要满足相应的要求。见Person.java
4. 序列化机制：
对象序列化机制允许把内存中的java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。
当其他程序获取了这种二进制流，就可以恢复成原来的java对象。

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.*;

public class ObjectInputOutputStreamTest {

    /*
    序列化过程：将内存中的java对象保存在磁盘中，或通过网络传输出去
    使用ObjectOutputStream实现
     */
    @Test
    public void testObjectOutputStream() {
        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("object.dat"));
            oos.writeObject(new String("helloWorld"));
            oos.flush();// 刷新操作

            oos.writeObject(new Person("dxu", 20));
            oos.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (oos != null) {
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /*
    反序列化：将磁盘文件中的对象还原为内存中的一个java对象
    使用ObjectInputStream来实现
     */
    @Test
    public void testObjectInputStream() {
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream("object.dat"));
            Object obj = ois.readObject();
            String str = (String) obj;
            System.out.println(str);

            Person p = (Person) ois.readObject();
            System.out.println(p);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ois != null) {
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

```java
package com.atguigu.java;

import java.io.Serializable;

/*
Person需要满足如下的要求，方可序列化
1. 需要实现接口：Serializable
2. 当前类提供一个全局常量：serialVersionUID
3. 除了当前Person类需要实现Serializable接口之外，还必须保证其内部所有属性也必须是可序列化的。（默认情况下，基本数据类型是可序列化的）
4. ObjectOutputStream和ObjectInputStream不能序列化static和transient修饰的成员变量
 */
public class Person implements Serializable {
    public static final long serialVersionUID = 424545154841L;
    private String name;
    private int age;

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

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```


### 5.5 随机存取文件流

RandomAccessFile的使用

1. RandomAccessFile直接继承于java.lang.Object类，实现了DataInput和DataOutput接口
2. RandomAccessFile既可以作为一个输入流，又可以作为一个输出流
3. 如果RandomAccessFile作为输出流时，写出到的文件如果不存在，则在执行过程中自动创建;
如果写出到的文件存在，则会对原有文件内容进行覆盖。（默认情况下，从头覆盖）
4. 可以通过相关的操作，实现RandomAccessFile"插入"数据的效果

```java
package com.atguigu.java;

import org.junit.Test;

import java.io.File;
import java.io.IOException;
import java.io.RandomAccessFile;

public class RandomAccessFileTest {
    @Test
    public void test1() {
        RandomAccessFile raf1 = null;
        RandomAccessFile raf2 = null;
        try {
            raf1 = new RandomAccessFile(new File("sf_skyline.jpeg"), "r");
            raf2 = new RandomAccessFile(new File("sf_skyline1.jpeg"), "rw");

            byte[] buffer = new byte[1024];
            int len;
            while ((len = raf1.read(buffer)) != -1) {
                raf2.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (raf1 != null) {
                try {
                    raf1.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (raf2 != null) {
                try {
                    raf2.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @Test
    public void test2() throws IOException {
        RandomAccessFile raf1 = new RandomAccessFile("hello.txt", "rw");
        raf1.seek(3);// 将指针移动到索引为3的位置
        raf1.write("xyz".getBytes());
        raf1.close();
    }
    /*
    使用RandomAccessFile实现数据的插入效果
     */
    @Test
    public void test3() throws IOException {
        RandomAccessFile raf1 = new RandomAccessFile("hello.txt", "rw");
        raf1.seek(3);// 将指针移动到索引为3的位置
        // 保存指针3后面的所有数据到StringBuilder中
        StringBuilder builder = new StringBuilder((int) new File("hello.txt").length());
        byte[] buffer = new byte[20];
        int len;
        while ((len = raf1.read(buffer)) != -1) {
            builder.append(new String(buffer, 0, len));
        }
        // 调回指针,写入"xyz"
        raf1.seek(3);
        raf1.write("xyz".getBytes());
        // 将StringBuilder中的数据写入到文件中
        raf1.write(builder.toString().getBytes());
        raf1.close();

        // 思考：将StringBuilder替换为ByteArrayOutputStream
    }
}

```



































