# JVM理解

### jvm运行之后由几部分组成?

- 类加载器(ClassLoader)
- 运行时数据区(Runtime Data Area)
- 执行引擎(Execution Engine)
- 本地库接口(Native Interface)

![image-20200521201744361](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200521201744361.png)

类加载器用于把.class文件加载到内存当中就是jvm所管理的内存,让她成为jvm能识别的格式,当new对象时,调用静态方法时,类继承父类,其父类没定义时,反射时,调用main方法时,就会调用类加载器,类加载器分为三种启动类加载器(BootStrapClassLoader),扩展类加载器(ExtensionClassLoader),应用程序类加载器(SystemClassLoader)

执行引擎就是用于把.class文件转换成计算机能识别的文件,然后cpu就可以执行了
本地方法接口就是当你用native关键字修饰时,他会调用本地方法接口,此时的实现是使用的其他语言 

## JVM的类加载过程

### 加载

查找class文件

### 验证

判断class文件的格式是否标准

### 准备

为**静态**变量分配内存并赋初始值int为0,引用类型为空

### 解析

方法中调用其他对象或方法,User user=new User() ,正式的将user指向User地址

### 初始化

对static修饰的变量或者语句块,进行初始化,先初始化父类的静态变量或者语句块



## JVM的双亲委托机制

![image-20200523103856878](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200523103856878.png)

当收到一个类加载请求时,(**如果有父加载器的情况下不能自己加载**)自下而上,因此最终是由启动类加载器加载

找到jdk中jre下的lib下的应用加载,失败了,才会往下找到扩展类加载器(jre下的ext),最终由应用程序类加载器,找到Classpath看是否有.class类文件.

#### 为什么不能由自己加载呢?

因为累加载器加载的类对象都不是一个对象,如果此时需要加载Java.lang.Object.class类,然后每种加载器都加载了各自的Object,那么到底要继承哪个加载器的Object对象呢?,因此双亲委托机制,自下而上,最终都会只产生一个顶级的类对象,就解决了混乱问题.

# jvm运行时数据区

## 程序计数器

当多线程情况下,线程之间执行会被打断,程序计数器就是用于记录,被打断的线程执行到哪了

## java虚拟机栈

```java
public static void main(String[] args){
    fun1();
}
public static fun1(){
    fun2();
}
public static int fun2(){
    return 1;
}
```



栈低就是main,栈顶就是fun2,因此返回数据时就是从顶开始往下返回最终回到main方法,灭有个方法就是一个**栈帧**

方法调用前就会创建栈帧,方法调用时就会入栈,执行后就会出栈

![image-20200524154932330](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200524154932330.png)

## 本地方法栈

native修饰时就会在方法栈中处理,和虚拟机栈蕾丝

## java堆

几乎所有的对象都存在java堆中,在虚拟机启动时被创建,java堆是java虚拟机锁管理的内存中最大的一块.java堆在物理内存上是不连续的只是,逻辑上是连续的

![image-20200524160450954](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200524160450954.png)

## 方法区

![image-20200524161414163](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200524161414163.png)

## GC垃圾回收

垃圾收集器对堆进行回收前会判断哪些对象是存活,哪些对象已经永远用不到了,判断的算法有两个引用计数器法和可达性分析算法,一般都是第二种

## 垃圾回收算法

### 标记-清除算法

### 复制算法

### 标记-整理算法

### 分带收集算法(重点)

根据对象生存周期不同将内存分为几个部分,新生代使用复制算法因为存活率较低,老年代使用标记清除和标记整理算法因为存活率较高

![image-20200524221025103](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200524221025103.png)

![image-20200524221153735](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200524221153735.png)

![image-20200530104103034](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530104103034.png)

![image-20200530104152141](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530104152141.png)





## mysql

## 重点

![image-20200530105104190](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530105104190.png)



![image-20200530105447464](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530105447464.png)



使用EXPLAIN关键字判断索引类型

![image-20200530110212745](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530110212745.png)

![image-20200530110340298](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530110340298.png)







### 以下属于架构师

![image-20200530110632608](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530110632608.png)

****

![image-20200530110657154](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530110657154.png)



![image-20200530111130763](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530111130763.png)

![image-20200530111649514](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530111649514.png)

等待唤醒机制

生产着消费者

![image-20200530112755376](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530112755376.png)

![image-20200530112833184](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530112833184.png)

![image-20200530112901238](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530112901238.png)

![image-20200530112932488](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200530112932488.png)