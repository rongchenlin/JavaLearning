- [Java高级编程](#java高级编程)
  - [1 Java多线程编程](#1-java多线程编程)
    - [1.1 Thread类实现多线程](#11-thread类实现多线程)
    - [1.2 Runnable接口实现多线程](#12-runnable接口实现多线程)
    - [？1.4 Callable接口实现多线程](#14-callable接口实现多线程)
    - [1.5 多线程运行状态](#15-多线程运行状态)
  - [2 线程常用操作方法](#2-线程常用操作方法)
    - [2.1 线程的命名和取得](#21-线程的命名和取得)
    - [2.2 线程休眠](#22-线程休眠)
    - [2.3 线程中断](#23-线程中断)
    - [2.4 线程强制执行](#24-线程强制执行)
    - [2.5 线程礼让](#25-线程礼让)
    - [2.6 线程优先级](#26-线程优先级)
  - [3 线程的同步与死锁](#3-线程的同步与死锁)
    - [3.1 同步问题引出](#31-同步问题引出)
    - [3.2 线程同步处理](#32-线程同步处理)
  - [*4 生产者消费者问题](#4-生产者消费者问题)
    - [线程等待和唤醒](#线程等待和唤醒)
  - [5 多线程案例](#5-多线程案例)
    - [加法减法问题](#加法减法问题)
  - [7 java基础类库](#7-java基础类库)
    - [7.1 CharSequence](#71-charsequence)
    - [Runtime类](#runtime类)
    - [System类](#system类)
  - [9 日期操作](#9-日期操作)
    - [9.1 Date日期处理](#91-date日期处理)
    - [9.2 SimpleDateFormat处理类](#92-simpledateformat处理类)
    - [开发支持类库](#开发支持类库)
    - [UUID类](#uuid类)

# Java高级编程

## 1 Java多线程编程

### 1.1 Thread类实现多线程

- **继承Thread**类实现多线程 ，然后**覆写run()方法**，但是run()方法不能直接被调用，要想启动多线程必须**使用start()方法**完成。


```Java
class MyThread extends Thread{
	private String title;
	public MyThread(String title){
		this.title = title;
	}
	@Override
	public void run(){
		for(int i = 0;i<10;i++){
			System.out.println(this.title+"运行. x = " + i);
		}
	}
}
public class Test {
	public static void main(String[] args) throws Exception {
		new MyThread("线程A").start();
		new MyThread("线程B").start();
		new MyThread("线程C").start();
	}
}
```

### 1.2 Runnable接口实现多线程

优点：不再有单继承的缺陷。

```Java
class MyThread implements Runnable{  // 实现Runnable
	private String title;
	public MyThread(String title){
		this.title = title;
	}
	@Override
	public void run(){
		for(int i = 0;i<10;i++){
			System.out.println(this.title+"运行. x = " + i);
		}
	}
}
public class Test {
	public static void main(String[] args) throws Exception {
		Thread threadA = new Thread(new MyThread("线程A"));
		threadA.start();
		Thread threadB = new Thread(new MyThread("线程B"));
		threadB.start();
	}
}
```

### ？1.4 Callable接口实现多线程

Runnable接口的缺点：无法获取一个返回值。

<img src="image/image-20210808135122432.png" alt="image-20210808135122432" style="zoom:67%;" />

Runnable和Callable的区别

- Runnable是在 JDK1.0 的时候提出的多线程的实现接口，而Callable是在JDK1.5之后提出的;
- java.lang.Runnable接口之中只提供有一个run()方法，并且没有返回值;.
- java.util.concurrent.Callable接口提供有call()方法，可以有返回值;

```Java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

class MyThread implements Callable<String>{  // 实现Runnable
	private String title;
	public MyThread(String title){
		this.title = title;
	}
	@Override
	public String  call() throws Exception{
		for(int i = 0;i<10;i++){
			System.out.println(this.title+"运行. x = " + i);
		}
		return "线程执行完毕";
	}
}
public class Test {
	public static void main(String[] args) throws Exception {
		FutureTask<String> task = new FutureTask<>(new MyThread("线程A"));
		new Thread(task).start();
		System.out.println("线程返回数据："+task.get());
	}
}
运行结果：
    	线程A运行. x = 0
        线程A运行. x = 1
        线程A运行. x = 2
        线程A运行. x = 3
        线程A运行. x = 4
        线程A运行. x = 5
        线程A运行. x = 6
        线程A运行. x = 7
        线程A运行. x = 8
        线程A运行. x = 9
        线程返回数据：线程执行完毕

```

### 1.5 多线程运行状态

start（）：准备运行，进入就绪状态，实际还没执行

run（）：获得调度，开始执行

## 2 线程常用操作方法

### 2.1 线程的命名和取得

在任何的开发之中，主线程可以创建若干个子线程，创建子线程的目的是可以将一些复杂逻辑。

主线程负责处理整体流程，而子线程负责处理耗时操作。

- 构造方法: public Thread(Runnable target, String name); 
- 设置名字:public final void setName(String name); 
- 取得名字:public final String getName(); 
- 获取当前线程: public static Thread currentThread();

```Java
class MyThread implements Runnable{
	@Override
	public void run(){
		System.out.println(Thread.currentThread().getName());
	}
}
public class Test {
	public static void main(String[] args)  {
		MyThread mt = new MyThread();
		new Thread(mt,"线程A").start();
		new Thread(mt,"线程B").start();
		new Thread(mt).start();
	}
}
```

### 2.2 线程休眠

如果希望某一个线程可以暂缓执行，那么可以进行休眠。

<img src="image/image-20210809105130698.png" alt="image-20210809105130698" style="zoom:67%;" />

### 2.3 线程中断

所有正在执行的线程都是可以被中断的，中断线程必须进行异常的处理。

```Java
public class Test {
	public static void main(String[] args) throws InterruptedException  {
		Thread thread = new Thread(()->{
			System.out.println("睡觉中");
			try{
				Thread.sleep(5000); // 休眠5s
				System.out.println("睡足了");
			}catch (InterruptedException e) {
				System.out.println("谁把我吵醒");
			}
		});
		thread.start();  // 开始
		Thread.sleep(1000); // 先休眠1s
		if(!thread.isInterrupted()) { // 如果没有被中断
			System.out.println("我要打扰你睡觉了");
			thread.interrupt();
		}
	}
}
```

### 2.4 线程强制执行

强制执行：当满足某些条件之后，某一个线程对象将可以一直独占资源，一直到该线程的程序执行

Thread mainThread = Thread.currentThread();  //获得线程

mainThread.join();  // 让线程先执行

### 2.5 线程礼让

Thread中的yield方法

### 2.6 线程优先级

优先级越高，越**有可能**先抢占到资源。

- 设置优先级: public final void setPriority(intnewPriority); 
- 获取优先级: public final int getPriority()。

![image-20210809111548118](image/image-20210809111548118.png)

## 3 线程的同步与死锁

### 3.1 同步问题引出

原因：多个进程同时访问一个资源。

### 3.2 线程同步处理

使用synchronized关键字，在同步代码块的操作里面的代码只允许一个线程执行。（但是使用同步，会降低性能）

- 同步代码块
- 同步方法

```Java
class MyThread implements Runnable{
	private int ticket = 10;
	public synchronized boolean sale(){ // 同步方法
		if(this.ticket > 0){
			try {
				Thread.sleep(100); // 模拟网络延迟
			} catch (InterruptedException e) {
				// TODO: handle exception
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + " 卖票  ,   ticket = "+ this.ticket--);
			return true;
		}else{
			System.out.println("票已经卖光了");
			return false;
		}
	}
	@Override
	public void run(){
		while(this.sale()){
			;
		}
	}
}
public class Test {
	public static void main(String[] args) throws InterruptedException  {
		MyThread mt = new MyThread();
		new Thread(mt,"窗口1").start();
		new Thread(mt,"窗口2").start();
		new Thread(mt,"窗口3").start();
	}
}
运行结果：
    窗口1 卖票  ,   ticket = 10
    窗口3 卖票  ,   ticket = 9
    窗口3 卖票  ,   ticket = 8
    窗口3 卖票  ,   ticket = 7
    窗口2 卖票  ,   ticket = 6
    窗口2 卖票  ,   ticket = 5
    窗口3 卖票  ,   ticket = 4
    窗口1 卖票  ,   ticket = 3
    窗口3 卖票  ,   ticket = 2
    窗口3 卖票  ,   ticket = 1
    票已经卖光了
    票已经卖光了
    票已经卖光了
```

## *4 生产者消费者问题

### 线程等待和唤醒

wait()：   // 线程等待

notify()：// 唤醒第一个等待进程，其他进程继续等待

notifyAll()：// 唤醒所有等待进程，优先级高的可能优先被唤醒

## 5 多线程案例

### 加法减法问题

```Java
// 加法线程类
class  AddThread implements Runnable{
    private Resource resource;
    public AddThread (Resource resource){
        this.resource = resource;
    }

    @Override
    public void run() {
        for(int i=0;i<50;i++){
            try {
                this.resource.add();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
// 减法线程类
class  SubThread implements Runnable{
    private Resource resource;
    public SubThread (Resource resource){
        this.resource = resource;
    }
    @Override
    public void run() {
        for(int i=0;i<50;i++){
            try {
                this.resource.sub();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class Resource{
    private int num = 0;
    private boolean flag = true;
    // flag = true : 只可以进行加操作
    // flag = false :只可以进行减操作
    public synchronized void add() throws InterruptedException {
        if(this.flag == false) {   // 现在只能进行减法操作，加法等待
            super.wait();
        }
        Thread.sleep(100);
        this.num++;
        System.out.println("加法操作 "+ Thread.currentThread().getName() + "num = " + this.num);
        this.flag = false;   // 加法操作结束，需要进行减法操作
        super.notifyAll();   // 唤醒全部等待进程
    }

    public synchronized void sub() throws InterruptedException {
        if(this.flag == true) {
            super.wait();
        }
        Thread.sleep(200);
        this.num--;
        System.out.println("减法操作 "+ Thread.currentThread().getName() + "num = " + this.num);
        this.flag = true;
        super.notifyAll();
    }
}
public class Hello {
    public static void main(String[] args) {
        Resource res = new Resource();
        AddThread addThread = new AddThread(res);
        SubThread subThread = new SubThread(res);
        new Thread(addThread,"加法进程A：").start();
        new Thread(addThread,"加法进程B：").start();
        new Thread(subThread,"减法进程X：").start();
        new Thread(subThread,"减法进程Y：").start();
    }
}
```

## 7 java基础类库

### 7.1 CharSequence

CharSequence是一个描述字符串结构的接口，在这个接口有三个常用子类。

<img src="image/image-20210810161013237.png" alt="image-20210810161013237" style="zoom:50%;" />

### Runtime类

Runtime类是描述运行时状态的类。

- 获取实例化对象:public static Runtime getRuntime();
- 通过这个类中的availableProcessors()方法可以获取本机的CPU内核数
- 获取最大可用内存空间: public long maxMemory();   // 默认为1/4
- 获取可用内存空间:public long totalMemory(); 
- 获取空闲内存空间: public long freeMemory(); 
- 手工进行GC处理: public void gc()。

```Java
public class Hello {
    public static void main(String[] args) {
        Runtime runtime = Runtime.getRuntime(); // 获取实例化对象
        System.out.println("内核数量："+runtime.availableProcessors());
        System.out.println("最大内存："+runtime.maxMemory());
        System.out.println("可用内存："+runtime.totalMemory());
        System.out.println("空闲内存："+runtime.freeMemory());
    }
}
```

### System类

获取当前的日期时间数值:public static long currentTimeMillis();

## 9 日期操作

### 9.1 Date日期处理

- 将long转为Date: public Date(long date);
- 将 Date转为 long: public long getTime(); 

```Java
import java.util.Date;

public class Hello {
    public static void main(String[] args) {
        Date date = new Date();
        System.out.println(date);
        // Date = > long
        long nextDay = date.getTime() + 86400 *1000;   // 一天之后的时间
        System.out.println(new Date(nextDay));  // long = > Date
    }
}
```

### 9.2 SimpleDateFormat处理类

主要是用于**时间格式化处理**，返回String类型

```Java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
public class Hello {
    public static void main(String[] args) throws ParseException {
        // 日期 => String
        Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String str = sdf.format(date);
        System.out.println(str);
        // String => 日期
        String dd = "2020-11-11 12:12:20.111";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
        Date date1 = sdf2.parse(dd);
        System.out.println(date1);
    }
}
```

### 开发支持类库

### UUID类

根据时间戳产生一个无重复的字符串定义。

```java
import java.text.ParseException;
import java.util.UUID;

public class Hello {
    public static void main(String[] args) throws ParseException {
       UUID uuid = UUID.randomUUID();
        System.out.println(uuid.toString());
    }
}
```

在对文件进行自动命名时，UUID类型好用。

### ThreadLocal类

所有线程的数据都存放到ThreadLocal中，同时ThreadLocal提供set()、get()、remove()方法。

对于线程对象，则是由Thread.currentThread()来获取。

### 定时调度

- java.util.TimerTask类:实现定时任务处理;.
- java.util.Timer类:进行任务的启动，启动的方法: 
  - 任务启动: public void schedule(TimerTask task, long delay)
  - 间隔触发：scheduleAtFixedRate()

<img src="image/image-20210811113439228.png" alt="image-20210811113439228" style="zoom:50%;" />

```Java
import java.util.Timer;
import java.util.TimerTask;

class MyTask extends TimerTask{ // 任务主体
    @Override
    public void run() {  // 多线程处理方法
        System.out.println(Thread.currentThread().getName() + " 定时任务执行，当前时间：" + System.currentTimeMillis());
    }
}
public class Hello {
    public static void main(String[] args)  {
       Timer timer = new Timer();  // 定时任务
        timer.schedule(new MyTask(),1000); // 延迟1s中后启动
    }
}
```

### Base64加密和解密

- Base64.Encoder:进行加密处理;.
  - 加密处理: public byte[] encode(byte[] src);
- Base64.Decoder:进行解密处理。
  - 解密处理: public byte[] decode(String src); 

**加密：**

<img src="image/image-20210811115207774.png" alt="image-20210811115207774" style="zoom:50%;" />

**解密：**

<img src="image/image-20210811115246659.png" alt="image-20210811115246659" style="zoom:50%;" />

## 13 比较器

### Comparable比较器

对象数组排序，只需要在compareTo()方法进行排序规则的定义。

```Java
import java.util.Arrays;

class Person implements Comparable<Person>{
    private String name;
    private int age;
    public Person(String name,int age){
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person o) {
        return this.age - o.age;  // 根据年龄进行排序
    }

    @Override
    public String toString() {
        return "姓名："+this.name+" 年龄："+this.age+"\n";
    }
}
public class Hello {
    public static void main(String[] args)  {
        Person[] data = new Person[]{
                new Person("张三",12),
                new Person("李四",8),
                new Person("王五",16)
        };
        Arrays.sort(data);   // 根据compareTo()里面的排序方式
        System.out.println(Arrays.toString(data));
    }
}
```

### Comparator比较器

**对于排序，不建议使用Comparator，最好使用Comprable**

java.lang.Comparable是在类定义的时候实现的父接口，主要用于定义排序规则

java.util.Comparator是挽救的比较器操作，需要设置单独的比较器规则类实现排序

```Java
import java.util.Arrays;
import java.util.Comparator;

class Person {
    private String name;
    private int age;
    public Person(String name,int age){
        this.name = name;
        this.age = age;
    }
    public int getAge(){
        return this.age;
    }
    @Override
    public String toString() {
        return "姓名："+this.name+" 年龄："+this.age+"\n";
    }
}
class PersonComprator implements Comparator<Person>{
    @Override
    public int compare(Person o1, Person o2) {
        return o1.getAge() - o2.getAge();
    }
}
public class Hello {
    public static void main(String[] args)  {
        Person[] data = new Person[]{
                new Person("张三",12),
                new Person("李四",8),
                new Person("王五",16)
        };
        Arrays.sort(data,new PersonComprator());   // 根据compareTo()里面的排序方式
        System.out.println(Arrays.toString(data));
    }
}
```

## 14 类库使用案例分析

### 学生信息比较

```Java
import java.util.Arrays;

class Student implements Comparable<Student>{
    private String name;
    private int age;
    private double score;
    public Student(String name, int age, double score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }

    @Override
    public int compareTo(Student o) {
        if(this.score < o.score){
            return 1;
        } else if(this.score > o.score){
            return -1;
        } else{
            return  this.age - o.age;
        }
    }
    @Override
    public String toString() {
        return "姓名：" + this.name + " 年龄：" + this.age + "成绩：" + this.score +"\n";
    }
}

public class Hello {
    public static void main(String[] args)  {
        String title = "张三:21:95|李四:18:90|王五:23:98";
        String [] result = title.split("\\|");
        Student students [] = new Student[result.length];
        for(int i = 0;i<result.length;i++){
            String  temp[] = result[i].split(":");
            students[i] = new Student(temp[0],Integer.parseInt(temp[1]),Double.parseDouble(temp[2]));
        }
        Arrays.sort(students);
        System.out.println(Arrays.toString(students));
    }
}
```

## 15 文件操作

### 15.1 File类基本操作

```Java
import java.io.File;
import java.io.IOException;

public class Hello {
    public static void main(String[] args) throws IOException {
        File file = new File("e:\\Java.txt"); // 文件对象
        if(file.exists()) { // 判断文件是否存在
            file.delete();  // 若文件存在则删除
        } else {             // 若文件不存在
            file.createNewFile();// 创建文件
        }
    }
}
```

### 15.2 File类操作深入

1. 为了考虑不同操作系统的文件分隔符问题，File类采用了常量**File.separator**来定义。

``` Java
File file = new File("e:"+File.separator+"Java.txt");
```

2. 获取父路径：getParentFIle()；
3. 创建目录：mkdirs();  // 多级，单级使用mkdir();

```Java
File file = new File("e:"+File.separator+"hello"+File.separator+"hello"+File.separator+"Java.txt");
if(!file.getParentFile().exists()) {  // 如果目录不存在
    file.mkdirs();
}
```

### 15.3 获取文件信息

```Java
public class Hello {
    public static void main(String[] args) throws IOException {
        File file = new File("e:"+File.separator+"2.jpg");
        System.out.println("文件是否可读："+file.canRead());
        System.out.println("文件是否可写："+file.canWrite());
        System.out.println("文件大小："+file.length());
    }
}
```

### 15.4 列出所有目录内容

```Java
public class Hello {
    public static void main(String[] args) throws IOException {
        File file = new File("e:"+File.separator);
        listDir(file);
    }
    public static void listDir(File file){
        if(file.isDirectory()) { // 是一个目录
            File result[] = file.listFiles(); // 列出目录中所有内容
            if (result != null) {
                for (int i = 0; i < result.length; i++) {
                    listDir(result[i]);
                }
            }
        }
        System.out.println(file); // 如果不是目录，则列出后内容
    }
}
```

## 反射机制

### Class类对象的三种实例化模式

getClass()可以帮助使用者找到对象的根源。

Class类三种实例化形式：

1. Object类支持：

```Java
class Person{

}

public class Hello {
    public static void main(String[] args) {
        Person per = new Person();
        Class<? extends Person> cls = per.getClass();
        System.out.println(cls.getSimpleName());
    }
}
```

2. JVM直接支持：类.class

```Java
class Person{

}

public class Hello {
    public static void main(String[] args) {
        Class<? extends Person> cls = Person.class;
        System.out.println(cls.getSimpleName());
    }
}
```

3. Class类支持：Class中的static方法

```Java
class Person{

}

public class Hello {
    public static void main(String[] args) throws ClassNotFoundException {
        Class<? > cls = Class.forName("Person");
        System.out.println(cls.getName());
    }
}
```

## 22 反射应用案例

### 22.1 反射实例化对象

**默认的Class类中的newInstance()方法只能够调用无参构造**

**旧的方法**

```Java
class Person{
    public Person(){
        System.out.println("这是一个无参构造方法");
    }
}

public class Hello {
    public static void main(String[] args) throws Exception {
        Class<? > cls = Class.forName("Person");
        Object obj = cls.newInstance(); // 实例化对象，JDK1.9之后废除了
        System.out.println(obj);
    }
}
```

新的方法

```java
class Person{
    public Person(){
        System.out.println("这是一个无参构造方法");
    }
}

public class Hello {
    public static void main(String[] args) throws Exception {
        Class<? > cls = Class.forName("Person");
//        Object obj = cls.newInstance(); // 实例化对象，JDK1.9之后废除了
        Object obj = cls.getDeclaredConstructor().newInstance();
        System.out.println(obj);
    }
}
```

### 反射与工厂设计模式

```Java
class Factory{
    /**
     * @function 获取接口实例化对象：通过泛型、反射实现一个与接口类型无关的工厂设计模式
     * @param className 接口的子类名称
     * @param clazz 描述的是接口的类型
     * @return 如果子类存在则返回指定类型的实例化对象
     */
    private Factory(){}
    public static <T> T getInstance(String className,Class<T> clazz){
        T instance = null;
        try {
            instance = (T) Class.forName(className).getDeclaredConstructor().newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return instance;
    }
}
interface IService{
    public void service();
}
class HouseService implements IService{
    @Override
    public void service() {
        System.out.println("住房服务！");
    }
}
interface IMessage{
    public void send();
}
class Message implements IMessage{
    @Override
    public void send() {
        System.out.println("发送消息");
    }
}
public class Hello {
    public static void main(String[] args) throws Exception {
        // 发送消息
        IMessage msg = Factory.getInstance("Message",IMessage.class);
        msg.send();
        // 住房服务
        IService ser = Factory.getInstance("HouseService",IService.class);
        ser.service();
    }
}
```

### **反射与单例设计模式

![image-20210812143430051](image/image-20210812143430051.png)

## 23 反射与类操作

### 23.1 反射获取类结构信息

- 获取包名称: public Package getPackage(); .
- 获取继承父类: public Class<? super T> getSuperclass();
- 获取实现父接口: public Class<?-[]getInterfaces()。

### 23.2 反射调用构造方法

所有类的构造方法的获取都可以通过Class类来完成。

- 获取所有构造方法：getDeclaredConstructors()；
- 获取指定构造方法：gtDeclaredConstructor(Class<?>)
