---
title: SpringBoot实现网站的ip访问数和总浏览量
categories:
  - 业务实现
tags:
  - SpringBoot
  - AOP
abbrlink: 1512459220
---


## 用到了切面技术(AOP)

**切面是什么?**:举例乘动车
<!--more-->

- **@Before**类似于过安检,检查大包小包,金属探测器检查全身

- 乘车过程

- **-@After**到达站点时需要检票出站

- **@AfterReturning**出站成功检票器,记录下了你的相关信息,

- **@AfterThrowing**检票失败,异常


## 1.编写IP访问类

```java
public class RequestLog {
        private String url;//访问地址
        private String ip;//访问ip
        private String classMethod;//访问方法
        private Object[] args;//参数

        public RequestLog(String url, String ip, String classMethod, Object[] args) {
            this.url = url;
            this.ip = ip;
            this.classMethod = classMethod;
            this.args = args;
        }

        @Override
        public String toString() {
            return "{" +
                    "url='" + url + '\'' +
                    ", ip='" + ip + '\'' +
                    ", classMethod='" + classMethod + '\'' +
                    ", args=" + Arrays.toString(args) +
                    '}';
        }
}
```

## 2.切面实现日志功能

切面主要用来实现日志功能,方法顺序是@Before-> @After->@AfterReturning

日志保存的位置在yml或者properties中配置

```java
@Aspect
@Component
public class LogAspect {

    private final Logger logger = LoggerFactory.getLogger(this.getClass());
// 意思是cn.coderzhx.blog2.web包下的所有类,所有方法,参数不限
    @Pointcut("execution(* cn.coderzhx.blog2.web.*.*(..))")
    public void log() {}


    @Before("log()")
    public void doBefore(JoinPoint joinPoint) {
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        String url = request.getRequestURL().toString();
        String ip = request.getRemoteAddr();
        String classMethod = joinPoint.getSignature().getDeclaringTypeName() + "." + joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();
        RequestLog requestLog = new RequestLog(url, ip, classMethod, args);
        logger.info("Request : {}", requestLog);
    }

    @After("log()")
    public void doAfter() {
//        logger.info("--------doAfter--------");
    }
//记录返回的视图
    @AfterReturning(returning = "result",pointcut = "log()")
    public void doAfterRuturn(Object result) {
        logger.info("Result : {}", result);
    }
}

```

## 3.实现ip访问次数功能

- 1.服务器启动执行init()从数据库获取ip和该ip访问次数,放入HashMap中,ip为键,访问次数为值.
- 2.访问网站获取ip,如果HashMap中有该ip则次数+1,没有则put(ip,1)
- 因为hashMap键不能重复,则map.size()表示有多少ip访问
- 把所有的map.value()相加就是总浏览量了

##  4.在上述代码中添加

```java
//ip地址为键,Integer为访问次数
public  static HashMap<String, Long> map=new HashMap<>();
```

**设置为Long的目的是放置访问次数过大超出Integer的范围,为将来着想**在doBefore中添加下面代码

```java
Long i= map.get(ip);//获取出现次数
if (i==null) {//空表示map没有该ip
    map.put(ip, 1L);
}else {//有则次数+1
    map.put(ip, i+1);
}
```

​    服务器关闭时,把所有的ip和访问次数放入数据库            

​	再次开启时则获取全部放入HashMap赋值给上面的类LogAspect中的map,

这个功能会在我的博客2.0版本中开源,1.0版本中计算ip个访问次数有bug           







