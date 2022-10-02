Mybatis会通过SqlSessionFactoryBuilder从mybatis全局配置文件（mybatis-config.xml）中构建出Configuration（所有的配置信息全保存在该对象中）以及一个个MappedStatement，之后就可以通过Configuration创建 SqlSessionFactory （线程安全），sessionFactory就可以开启SqlSession， SqlSession对象就可以完成和数据库的交互： a、用户程序调用mybatis接口层api（即Mapper接口中的方法） b、SqlSession通过调用api的Statement ID找到对应的MappedStatement对象 c、通过Executor（负责动态SQL的生成和查询缓存的维护）将MappedStatement对象进行解析，sql参数转化、动态sql拼接，生成jdbc Statement对象 d、JDBC执行sql。  借助MappedStatement中的结果映射关系，将返回结果转化成HashMap、JavaBean等存储结构并返回。 

## 2、Mybaits 的优点：
1、基于 SQL 语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL 写在 XML 里，解除 sql 与程序代码的耦合，便于统一管理；提供 XML 标签， 支持编写动态 SQL 语句， 并可重用。
2、与 JDBC 相比，减少了 50%以上的代码量，消除了 JDBC 大量冗余的代码，不需要手动开关连接；
3、很好的与各种数据库兼容（ 因为 MyBatis 使用 JDBC 来连接数据库，所以只要JDBC 支持的数据库 MyBatis 都支持）。
4、能够与 Spring 很好的集成；
5、提供映射标签， 支持对象与数据库的 ORM 字段关系映射； 提供对象关系映射标签， 支持对象关系组件维护。

## 3、MyBatis 框架的缺点：
1、SQL 语句的编写工作量较大， 尤其当字段多、关联表多时， 对开发人员编写SQL 语句的功底有一定要求。
2、SQL 语句依赖于数据库， 导致数据库移植性差， 不能随意更换数据库。
## MyBatis 框架适用场合：
1、MyBatis 专注于 SQL 本身， 是一个足够灵活的 DAO 层解决方案。
2、对性能的要求很高，或者需求变化较多的项目，如互联网项目， MyBatis 将是不错的选择。

## MyBatis 与Hibernate 有哪些不同？

1、Mybatis 和 hibernate 不同，它不完全是一个 ORM 框架，因为 MyBatis 需要程序员自己编写 Sql 语句。
2、Mybatis 直接编写原生态 sql， 可以严格控制 sql 执行性能， 灵活度高， 非常适合对关系数据模型要求不高的软件开发， 因为这类软件需求变化频繁， 一但需求变化要求迅速输出成果。但是灵活的前提是 mybatis 无法做到数据库无关性， 如果需要实现支持多种数据库的软件，则需要自定义多套 sql 映射文件，工作量大。
3、Hibernate 对象/关系映射能力强， 数据库无关性好， 对于关系模型要求高的软件， 如果用 hibernate 开发可以节省很多代码， 提高效率。

## 19、Mybatis 是否支持延迟加载？如果支持，它的实现原理是什么？

答： Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载， association 指的就是一对一， collection 指的就是一对多查询。在 Mybatis 配置文件中， 可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。
它的原理是， 使用 CGLIB 创建目标对象的代理对象， 当调用目标方法时， 进入拦截器方法， 比如调用 a.getB().getName()， 拦截器 invoke()方法发现 a.getB()是null 值， 那么就会单独发送事先保存好的查询关联 B 对象的 sql， 把 B 查询上来， 然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName()方法的调用。这就是延迟加载的基本原理。
当然了， 不光是 Mybatis， 几乎所有的包括 Hibernate， 支持延迟加载的原理都是一样的。

