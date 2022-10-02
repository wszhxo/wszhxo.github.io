---
title: Mybatis 深层理解
categories:
  - Mybatis
---
# Mybatis 深层理解

<!--more-->

- 定义xml文件,里边配置了数据库连接信息,以及mapper类路径

- 将xml文件转化为流

- 创建SqlSessionFactoryBuilder对象,通过它创建SqlSessionFactory对象,而且是单例的,只要项目创建过一次,直到项目关闭才会消失,

- 通过SqlSessionFactory得到SqlSession,这个接口中包含了所有操作数据库的方法

- 读取mapper接口类,找到接口类对应的mapper.xml文件,再根据接口方法找xml里的id为方法名的结点,解析出sql语句,如果没有找到方法则找接口类方法中的注解比如`@Select()`里找到sql语句,但如果xml和注解都写了sql语句,那么就会报错,因为底层是一个`Map<String,MapperStatement> mapperStatement=new StrictMap<>(); ` String存储id也就是方法名,它会生成一个MapperStatement对象,StrictMap继承了HashMap.可以重现put方法,当第二个相同的key进来时就会报错,因此xml和注解只能选择一个

- 获取sql后首先判断是`${}`还`是#{}`返回相应的对象dynamicSqlSourse还是staticSqlSourse,如果是静态的那么会把`#{}`转化为?,也就是预编译的形式,等到执行sql时才会把值传递进去,只要sql包含一个$那么就是动态的,

  ```java
  select ${str} from user where name=#{name} password=#{password}
  //接口方法赋值str="id",${}用的最多的也是这种方式
  select id from user where name=#{name} password=#{password}
  //之后转化为?
   select id from user where name=? password=?
  //最后就是jdbc级常规操作,就是预编译的参数设定,执行
  ```
  
  
  
- 使用jdk动态代理把参数传入,形成最终要执行的sql语句,传值的问题jdk1.8和之前有差别,是否加`Param`的区别

  因为jdk1.7时的反射机制还无法获取形参的名字`name`和`password`,只会生成`arg0`和`arg1`,djk1.8之后就可以
  
  ```xml
  User getUser(String name,String password)//1.8写法
  <select >
      select * from user where name=#{name} password=#{password} //jdk1.8是可以传入值的
  </select>
  User getUser(@Param("name") String name,@Param("password") String password)//1.7写法
  <select >
      select * from user where name=#{name} password=#{password} 
  </select>    
  ```
  
  如果要在jdk.7下不加入`@Param`那么,还有一种方法是在pom.xml中的build下的configuration标签下下加入,pom.xml下加入build标签可以使之生成war包而不是jar包
  
  ```xml
  <compilerArgs>
      <compilerArg>-parameters</compilerArg>
  </compilerArgs>
  ```
  
  

