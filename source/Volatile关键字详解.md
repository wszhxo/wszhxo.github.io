Volatile关键字详解

> 轻量级的同步机制,(synchronized的青春版,因为synchronized可以保证可见性)
>
> 保证了可见性
>
> 不保证原子性
>
> 禁止指令重排(有序性)



## 可见性



**JMM**(Java内存模型Java Memory Model，简称JMM)本身是一种抽象的概念并不真实存在，它描述的是一组规则或规范:**可见性,原子性,有序性以保证线程安全**

JMM关于同步的规定:

1. 线程解锁前，必须把共享变量的值刷新回主内存
2. 线程加锁前，必须读取主内存的最新值到自己的工作内存
3. 加锁解锁是同一把锁


由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存(有些地方称为栈空间)，工作内存是每个线程的私有数据区域，而Java内存模型中规定所有变量都存储在主内存，主内存是共享内存区域，所有线程都可以访问，但线程对变量的操作(读取赋值等)必须在工作内存中进行，首先要将变量从主内存拷贝的自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信(传值)必须通过主内存来完成.

![image-20210306202511162](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora/image-20210306202511162.png)

每一个线程都会有自己的**私有工作内存**是从主内存中读取的副本  ,如果此时线程1修改了age为24 ,写到主内存中主内存变成了24, 但是其他线程是不知道的

**volatile关键字就是可以使得变量可见,也就是线程1更新写回主内存其他线程能马上知道获得通知,这个机制就是可见性**

程序验证:只要证明如果线程1修改了数据,线程2确实马上得到了通知,那就能说明可见性

```java
public class JVM学习 {
    int age=23;
    public void add(){
       this.age+=1;
    }
    public static void main(String[] args) {
        JVM学习 app1=new JVM学习();

        new Thread(()->{
            System.out.println(Thread.currentThread().getName()+"来了,此时age为:"+app1.age);
            //线程休眠3秒为了让main线程先运行
            try { Thread.sleep(3000);} catch (InterruptedException e) {e.printStackTrace();}
            app1.add();
            System.out.println(Thread.currentThread().getName()+"修改了age为:"+app1.age);
        },"线程1").start();
        while(app1.age==23){
            //如果main线程一直在循环说明,main线程没有得到通知,也就是说没有可见性
        }
        System.out.println(Thread.currentThread().getName()+"结束!");
    }
}
```



![image-20210306202544700](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora/image-20210306202544700.png)



执行后发现该程序并没有结束,还一直在运行,说明while循环一直为true,也就是main线程并没有得到age已经修改的通知

此时把age变量加入关键词volatile改为```volatile int age=23;```

![image-20210306202558994](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora/image-20210306202558994.png)

此时程序已经结束了,说明main线程得到通知,这就验证了**可见性**



## 不保证原子性

创建20个线程每个线程执行1000次加1操作

```java
public class JVM学习 {
    volatile int age=23;
    public void add(){
       this.age+=1;
    }
    public static void main(String[] args) {
        JVM学习 app1=new JVM学习();

        for (int i = 0; i <20 ; i++) {
            new Thread(()->{
                for (int j = 0; j <1000 ; j++) {
                    app1.add();
                }
            },"线程"+i).start();
        }
		//目的为了让上边的20个线程全部执行完
        while (Thread.activeCount()>2){//main线程和gc线程
            Thread.yield();//放行,让其他线程先执行
        }
        System.out.println(app1.age);
    }
}
```

如果线程安全,最后得到20023,但是执行之后每次都是小于这个值

**因为age++操作不是原子性的**,他在字节码文件中分为3个步骤,获取原值,+1,写入主存

在写入主存的过程中,比如线程1获取age=23,线程2也获取age=23,他们各自给自己的副本加1操作都为24,线程2挂起,线程1把24写入主存,线程2执行写入操作把原来的24覆盖了,age完全没变还是24,这个过程就造成了值永远小于预期值

### 解决原子性:

- 可以在add方法中加入synchronized,但是杀鸡用牛刀,大材小用

- 使用java.util.concurrent.atomic下的原子包装类型(**乐观锁实现**)

  ```java
  AtomicInteger age=new AtomicInteger(23);
      public void add(){
         age.getAndIncrement();//表示加1
      }
  ```

  



## 有序性

计算机在执行程序时，为了提高性能，编译器和处理器的常常会对指令做重排，

源代码 - > 编译器优化的重排 - > 指令并行的重排 - > 内存系统的重排 - > 最终执行的指令

一般分以下3种:

- 单线程环境里面确保程疗最终执行结果和代码顺序执行的结果一致。
- **处理器在进行重排序时必须要考虑指令之间的数据依赖性**
- 多线程环境中线程交替执行,由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的,结果无法预测

```java
int a=1;  //1
int b=2;  //2
a=a+1;    //3
b=a*a;  //4
```

上述四行代码在底层存在指令重排,也就是顺序为  1234,2134, 1324,1243 ,,使用volatile可以防止这种情况,当然由于数据依赖性不会出现4开头的情况

举例单例模式,双检锁的懒汉单例模式是线程安全的,

```
public class 单例模式 {
    private static 单例模式 s=null;
    private 单例模式(){
        System.out.println(Thread.currentThread().getName()+"构造方法");
    }
    public static 单例模式 getInstance(){
        if (s==null){
           synchronized (单例模式.class){
               if (s==null){
                   s=new 单例模式();
               }
           }
        }
        return s;
    }
    public static void main(String[] args) {
        for (int i = 0; i <20 ; i++) {
            new Thread(()->{
                    单例模式.getInstance();
            },"线程"+i).start();
        }
    }
}
```

可是如果**考虑到指令重排**   ```s=new 单例模式(); ```  的过程会被分为3步

1. 分配对象内存空间
2. 调用构造器方法,初始化对象
3. 设置s指向刚分配的内存地址,此时s就不为空了

23步骤存在调换执行顺序的情况,因此需要加入volatile禁止指令重排

| -    | -线程1                          | 线程2                                             |
| ---- | ------------------------------- | ------------------------------------------------- |
| 1    | 分配内存                        |                                                   |
| 2    | 变量赋值指向该内存,此时 s!=null |                                                   |
| 3    |                                 | 判断第一层 s!=null                                |
| 4    |                                 | 不为空因此直接使用该对象,**此时对象并没有初始化** |
| 5    | 调用构造方法初始化对象          |                                                   |

 