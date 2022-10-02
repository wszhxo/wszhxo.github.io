Springboot

快速整合第三方框架(Mybatis,hibernate) 原理:Maven依赖和自定义的starter

完全去除xml配置,采用注解形式原理:包装Spring原生的注解

不需要外部容器内嵌tomcat

![image-20200304115644717](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200304115644717.png)

Spring-boot -starter-web 内加入了tomcat 依赖

分析流程:

- 创建SpringApplication对象Springboot容器初始化操作
- 获取当前应用启动类型  原理:判断当前classpath是否有加载我们的servlet类返回servlet web 启动方式(webApplicationType)有三种1,响应式启动(Spring5新特性)2.None使用外部tomcat启动而不是内置的,3.SERVLET使用内置tomcat
- setInitializers赌气Springboot包下的META-INF/spring.factories获取到对应ApplicationContextInitializer装配到集合中
- setListeners 读取Springboot包下的META-INF/spring.factories获取到对应ApplicationListener装配到集合中
- mainApplicationClass获取当前运行的主函数
- 调用SpringApplication run 方法实现启动
- StopWatch stopWatch=new StopWatch();记录aSpringboot项目启动时间



