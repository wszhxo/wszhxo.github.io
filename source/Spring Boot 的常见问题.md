

# # Spring Boot 的常见问题

## 项目启动时初始化资源

实现CommandLineRunner接口，实现run方法

```java
@Component
public class Runner implements CommandLineRunner {
    public void run(String... args) throws Exception {
        System.out.println("初始化");
    }
}
```

想要初始化加载顺序使用Order注解,数字越小优先级越大，可以是**正负整数**，不带有Order的则会最后启动

```java
@Component
@Order(1)
public class Runner1 implements CommandLineRunner {
    public void run(String... args) throws Exception {
        System.out.println("初始化@Order(1)");
    }
}
```

控制台结果如下

```java
容器初始化@Order(1)
容器初始化@Order(2)
没有Order初始化
```



