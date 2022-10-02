# Mybatis

ORM框架,ORM指的就是数据库字段的类型可以互相转化为对应的java实体类中属性的类型,Mybatis充当了翻译工具

1. SqlSessionFactoryBuilder   parse解析

2. Configuration  build

3. SqlSessionFactory openSession

   **以上都是解析xml文件**

4. SqlSession query 

5. Executor  newStatementHander  

6. StatementHander  HanderResultSets

7. ResultSetHander

   **以上是java操作数据库**

