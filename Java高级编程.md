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

