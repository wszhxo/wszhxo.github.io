# Volatile关键字理解

普通变量底层是这样实现的,会有一个副本,会使用副本进行相关计算,之后再同步到原始数据中

![image-20200429171205447](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200429171205447.png)

加了volatile关键字之后,就会直接操作原始变量

```java
public class Volatile关键字 {
	public static void main(String[] args) {
		MyThread thread=new MyThread();
		new Thread(thread,"A").start();;
		new Thread(thread,"B").start();;
		new Thread(thread,"C").start();;
	}
}
class MyThread implements Runnable{
	private  int ticket=5;//此时没加volatile关键字
	@Override
	public void run() {
		synchronized(this){
			while (this.ticket>0) {
				try {Thread.sleep(100);} catch (InterruptedException e) {e.printStackTrace();}
				System.out.println(Thread.currentThread().getName()+"==ticket=="+this.ticket--);
			}
		}
	}
}
```

在大数据量情况下,加了volatile关键字的程序出现多线程概率比没加的要低

 ## 面试题

#### 解释 volatile与synchronized的区别

- volatile主要在属性上使用,而synchronized是在代码块和方法上使用
- volatile无法描述同步的处理,只是一种则直接内存的处理,避免了操作副本,减少了中间操作就会减少多线程问题

其实这两个关键字没有实质联系