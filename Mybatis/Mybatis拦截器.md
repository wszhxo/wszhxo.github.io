# Mybatis 拦截器

用途：数据脱敏显示，sql日志打印，sql分页，记录sql执行时间等。

Mybatis 允许你在已映射语句整个过程中的某一点进行拦截调用

Mybatis 的InterceptorChain类会通过pluginAll对以下四种接口进行拦截。

| 拦截类型         | 拦截方法                                                     |                                                              |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Executor         | update, query, flushStatements, commit, rollback,getTransaction, close, isClosed | 是**SQL**执行器，包含了组装参数，组装结果集到返回值以及执行**SQL**的过程，粒度比较粗。 |
| ParameterHandler | getParameterObject, setParameters                            | 用来处理SQL的执行过程，我们可以在这里重写**SQL**非常常用。   |
| StatementHandler | prepare, parameterize, batch, update, query                  | 用来处理传入**SQL**的参数，我们可以重写参数的处理规则。      |
| ResultSetHandler | handleResultSets, handleOutputParameters                     | 用于处理结果集，我们可以重写结果集的组装规则。               |

## 介绍

Intercepts注解需要一个Signature(别名:拦截点或签名)参数数组。通过Signature来指定拦截哪个对象里面的哪个方法。@Intercepts注解定义如下:

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Intercepts {
    /**
     * 定义拦截点
     * 只有符合拦截点的条件才会进入到拦截器
     */
    Signature[] value();
}
```

Signature来指定咱们需要拦截哪个类对象的哪个方法和对应参数。定义如下：

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({})
public @interface Signature {
  /**
   * 定义拦截的类 Executor、ParameterHandler、StatementHandler、ResultSetHandler当中的一个
   */
  Class<?> type();

  /**
   * 在定义拦截类的基础之上，在定义拦截的方法
   */
  String method();

  /**
   * 在定义拦截方法的基础之上在定义拦截的方法对应的参数，
   * JAVA里面方法可能重载，故注意参数的类型和顺序
   */
  Class<?>[] args();
}

```

标识拦截注解`@Intercepts`规则,实例如下：

```java
@Intercepts({//注意看这个大花括号，也就这说这里可以定义多个@Signature对多个地方拦截，都用这个拦截器
        @Signature(
                type = ResultSetHandler.class,
                method = "handleResultSets", 
                args = {Statement.class}),
        @Signature(type = Executor.class,
                method = "query",
                args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})
})
```

**说明：**@Intercepts：标识该类是一个拦截器；@Signature：指明自定义拦截器需要拦截哪一个类型，哪一个方法；- type：上述四种类型中的一种；- method：对应接口中的哪类方法（因为可能存在重载方法）；- args：对应哪一个方法的入参；

## 实践

谈到自定义拦截器实践部分，主要按照以下三步：

1. 实现`org.apache.ibatis.plugin.Interceptor`接口,重写以下方法：

```java
public interface Interceptor {
    Object intercept(Invocation var1) throws Throwable;
    Object plugin(Object var1);
    void setProperties(Properties var1);
}
```

**拦截器中加入@Component即可注册拦截器**

### intercept(Invocation invocation)

拦截器处理逻辑的主要方法，从上面我们了解到interceptor能够拦截的四种类型对象，此处入参`invocation`便是指拦截到的对象。举例说明：拦截**StatementHandler#query(Statement st,ResultHandler rh)**方法，那么Invocation就是该对象。

### plugin(Object target)

这个方法的作用是就是让mybatis判断，是否要进行拦截，然后做出决定是否生成一个代理。

```java
@Override
    public Object plugin(Object target) {
    //判断是否拦截这个类型对象（根据@Intercepts注解决定），然后决定是返回一个代理对象还是返回原对象。
//故我们在实现plugin方法时，要判断一下目标类型，如果是插件是要拦截的对象时才执行Plugin.wrap方法，否则的话，直接返回目标本身。
        if (target instanceof StatementHandler) {
            return Plugin.wrap(target, this);
        }
        return target;
    }
```

**注意:每经过一个拦截器对象都会调用插件的plugin方法，也就是说，该方法会调用4次。根据@Intercepts注解来决定是否进行拦截处理。**

**StatementHandler：**

-  prepare: 用于创建一个具体的 Statement 对象的实现类或者是 Statement 对象。

- parametersize: 用于初始化 Statement 对象以及对sql的占位符进行赋值。

- update: 用于通知 Statement 对象将 insert、update、delete 操作推送到数据库。

- query: 用于通知 Statement 对象将 select 操作推送数据库并返回对应的查询结果。

**举例说明：**

- 当我们需要改变sql的时候，显然我们要在预编译SQL(prepare方法前加入修改的逻辑)比如分页功能，因此就需要拦截StatementHandler的prepare。

- 当我们需要修改参数的时候我们可以在调用parameterize方法前修改逻辑。或者使用ParameterHandler来改造设置参数。比如数据信息的加密存储功能

- 我们需要控制组装结果集的时候，也可以在query方法前后加入逻辑，或者使用ResultHandler来改造组装结果。比如数据信息的脱敏显示功能，记录sql执行时间功能。

>  Executor负责调度各种Handler最后还是会执行到Handler ,所以我们编写拦截器时要使用Handler，比如当要拦截更新语句时要使用StatementHandler中的update，更新语句包含增删改。

## 举例

```java
@Intercepts(value = {@Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})})
public class MyInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
          MetaObject metaObject = SystemMetaObject.forObject(handler);
            MappedStatement mappedStatement = (MappedStatement) metaObject.getValue("delegate.mappedStatement");
            SqlCommandType sqlCmdType = mappedStatement.getSqlCommandType();
        BoundSql boundSql = statementHandler.getBoundSql();
        Object obj = boundSql.getParameterObject();//获取sql参数
        String sql = boundSql.getSql();//获取预编译sql
        if (SqlCommandType.UPDATE.equals(sqlCmdType)) {
			
        } else if (SqlCommandType.INSERT.equals(sqlCmdType)) {
           
        }
        //sql执行获取的结果
        return invocation.proceed();
    }

 	@Override
    public Object plugin(Object target) {
        if (target instanceof StatementHandler) {
            return Plugin.wrap(target, this);
        } else {
            return target;
        }
    }

    @Override
    public void setProperties(Properties properties) {
        //设置拦截器需要使用的变量，这个方法基本不用
    }
}
```



**拦截器间冲突问题**

问题描述：多个Mybatis拦截器如果@Signature的type一样也许存在冲突，比如其中一个拦截器失效。

问题解决：

如果有多个拦截器一定重写plugin方法

```java
@Override
public Object plugin(Object target) {
    //StatementHandler表示@Signature的type
    //用于返回代理对象还是原始对象
    if (target instanceof StatementHandler) {
        return Plugin.wrap(target, this);
    } else {
        return target;
    }
}
```



**拦截器无法注入bean问题**

问题描述：拦截器无法注入Mapper类

问题原因：循环依赖问题，如果拦截器需要注入一些操作DB的类那么就有一个sqlSessionFactory需要加载，但是加载sqlSessionFactory需要初始化拦截器，因此产生了A-B-C-A 循环调用问题。

解决方法：先不加载需要注入的类，把拦截器加载好，当使用时再加载需要注入的类。使用懒加载@Lazy注解









