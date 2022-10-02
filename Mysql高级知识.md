Mysql高级知识

## 索引

### 单值索引

二叉树的每个结点值包含一个字段的值

创建三个单值索引 c1 c2 c3

c1生效 c2和c3不生效

```sql
 select * from table where c1=xx and c2=xx and c3=xx
```

```sql
 select * from table where c2=xx and c3=xx and c1=xx
```

c1生效 c2不生效

```sql
 SELECT * from table where c1=xx and c2=xx
```

c1生效 c3不生效

```sql
 select * from table where c1=xx and c3=xx
```

c2生效 c3不生效

```sql
 select * from table where c2=xx and c3=xx
```

c1和c3都不生效

```sql
 select * from table where c1=xx or c3=xx
```

c1和c2都生效

```sql
 select * from table where c1=xx or c2=xx
```

c3生效

```sql
select * from table where c3=xx
```

 c1和c2生效  c3不生效

```sql
 SELECT * from sys_user where  c1=xx or c2=xx and c3=xx
```

c1和c2生效  c3不生效

```sql
 SELECT * from sys_user where  c1=xx  or c3=xx and c2=xx
```

c1，c2，c3都不生效

```sql
 SELECT * from sys_user where  c1=xx and c2=xx or c3=xx
```

可以看出多个单值索引只会按照创建索引的顺序使第一个索引生效，**不过我没搞懂为什么最后一个索引都不生效。**

### 组合索引

创建组合索引( c1 c2 c3)

实际产生了c1 （c1，c2）（c1，c2，c3）三个索引

c1 c2 c3都生效，顺序不影响索引

```sql
 select * from table where c1=xx and c2=xx and c3=xx
```

```sql
 select * from table where c2=xx and c3=xx and c1=xx
```

c1生效 c3不生效

```sql
 select * from table where c1=xx and c3=xx
```

### 覆盖索引

select 列中包含索引 c1,c2,c3中的任意列或者组合，但如果包含了其他列那就不是覆盖索引了。

原理：正常查询数据是通过索引查询行数据，再取得具体值，而索引二叉树的结点中本身包含了索引值。因此select的数据直接从索引中就能获得，不必通过索引读取数据行，再获取对应值，

### 选择哪些列创建索引？

1.查询较为频繁的字段应该创建索引；

2.唯一性太差的字段不应该创建索引，就算这个字段经常作为查询条件；

3.更新频繁的字段不应该创建索引，索引是需要维护成本的；

4.有where条件时才会走索引，所以如果这个列不会作为where的查询条件，没有必要为这个字段创建索引；

### 不走索引的情况

在以前的工作中，有一次发现在特殊的情况下，即使你创建了索引，并且也在where加上了索引条件，查询过程中还是没有走索引。例如：

1.使用不等于（!= 或者<>）的时候MySQL 无法使用索引

2.like 后面的字符当首位为通配符时不走索引；如

  like "北%"，这种情况是走索引的

  like "%北"，这种情况是不走索引的

3.索引字段 is null 不走索引；

4.索引字段前加了函数或参加了运算不走索引；