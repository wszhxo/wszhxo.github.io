# JUC

 JUC就是java.util .concurrent工具包的简称。这是一个处理线程的工具包，JDK 1.5开始出现的 

## 内存可见性问题

当多个线程操作共享数据时，彼此不可见 就是内存可见性问题。这是出现线程安全的根本

```java
public class TestVolatile {
    public static void main(String[] args){ //这个线程是用来读取flag的值的
        ThreadDemo threadDemo = new ThreadDemo();
        Thread thread = new Thread(threadDemo);
        thread.start();
        /*
        * 此时存在线程安全问题，大部分时候还是不会出现的
        *ThreadDemo在没有修改flag为true之前，主线程main读取到false就进入死循环
        * 而后修改flag=true
        * 输出 主线程读取到的flag = true
        * 解决方法
        * 1.使用synchronized每次循环读取flag
        * 2.使用volatile
        * 就会及时的把线程缓存中的数据刷新到主存中去
        * */
        while (true){
//            synchronized (threadDemo) {
                if (threadDemo.flag) {
                    System.out.println("主线程读取到的flag = " + threadDemo.flag);
                    break;
                }
//            }
        }
    }
}
class ThreadDemo implements Runnable{ //这个线程是用来修改flag的值的
    public  boolean flag = false;
//    public volatile boolean flag = false;
    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        flag = true;
        System.out.println("ThreadDemo线程修改后的flag = " + flag);
    }
}
```

- 使用synchronized，加了同步锁之后threadDemo持有锁，成功修改为true，判断结束后跳出循环并释放锁
- 使用volatile关键字， 他会及时的把线程缓存中的数据刷新到主存中去 ，
- volatile和synchronized的区别：
  volatile不具备互斥性(当一个线程持有锁时，其他线程进不来，这就是互斥性)。
  volatile不具备原子性。

## 原子性问题

 i++ 不是原子性操作，i++实际上是 int temp = i;    i = i+1;    i = temp; 读改写的三部操作， 因此就会出现线程安全问题  ， 在i还没+1时就被第二个线程读取到原来的数据，因此产生重复数据

```java
public class TestIcon {
    public static void main(String[] args){
        AtomicDemo atomicDemo = new AtomicDemo();
        for (int x = 0;x < 10; x++){
            new Thread(atomicDemo).start();
        }
    }
}
class AtomicDemo implements Runnable{
    private  int i = 0;
    public int getI(){
        return i++;
    }
//    AtomicInteger i = new AtomicInteger();
//    public int getI(){
//        return i.getAndIncrement();
//    }
    @Override
    public void run() {
        try {
            //模拟线程安全问题
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getI());
    }
}
```

输出结果0到9，也许会存在2个0，或者其他重复的情况

所以volatile只是解决了可见性问题，但并没有解决重复数据的问题，



