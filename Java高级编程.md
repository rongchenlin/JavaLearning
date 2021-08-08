[TOC]

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

