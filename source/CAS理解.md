# CAS理解

 CAS（Compare And Swap ）是乐观锁的一种实现方式，是一种轻量级锁。JAVA1.5开始引入了CAS，**JUC（java.util.concurrent包）下很多工具类都是基于CAS。** 

## 悲观锁和乐观锁

**悲观锁**（ Pessimistic Concurrency Control ），每次有修改数据的操作时，会锁定修改的列，以免同时修改数据，之所以叫做悲观，就是认定每次修改大概率会出现并发问题，因此每次修改前要锁数据库，分为以下两种锁：

**共享锁**【Shared lock】又称为读锁，简称S锁 ， 共享锁就是多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改。 

**排他锁**【Exclusive lock】又称为写锁， 排他锁就是不能与其他锁并存，如果一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁，包括共享锁和排他锁，但是获取排他锁的事务是可以对数据行读取和修改。 

 一个事务如果锁定了某行数据，其他事务就必须等待该事务处理完才可以处理那行数据。 因此数据库开销大，效率低，易产生死锁。

**乐观锁**（Optimistic Locking）， 乐观锁假设数据一般情况下不会造成冲突，所以在数据进行提交更新的时候，才会正式对数据的冲突与否进行检测，如果发现冲突了，则返回给用户错误的信息，让用户决定如何去做。  相对于悲观锁，在对数据库进行处理的时候，乐观锁并不会使用数据库提供的锁机制。一般的实现乐观锁的方式就是记录数据版本。 **就是表中加一个标志字段，比如version字段，刚开始设置为0，每次操作后加上1，修改之前也要获取到这个字段值，根据一定规则进行判断**，由于并不使用锁机制，因此不会产生死锁

 **MySQL InnoDB默认行级锁。行级锁都是基于索引的，如果一条SQL语句用不到索引是不会使用行级锁的，会使用表级锁把整张表锁住，这点需要注意。** 

```sql
1.开启事务，默认自动提交，因此要开启
begin；
2.商品查询库存，使用for update加锁
select quanitity form items where id=1 for update；
3.修改库存
update items set quanitity=2 where id=1；
4提交
commit；
```

举个例子: 假设有张表user ，里面有 id 和 name 两列，id是主键。

例1: (明确指定主键，并且数据真实存在，row lock)

```
SELECT * FROM user WHERE id=3 FOR UPDATE;

SELECT * FROM user WHERE id=3 and name='Tom' FOR UPDATE;
```

例2: (明确指定主键，但数据不存在，无lock)

```
SELECT * FROM user WHERE id=0 FOR UPDATE;
```

例3: (主键不明确，table lock)

```
SELECT * FROM user WHERE id<>3 FOR UPDATE;

SELECT * FROM user WHERE id LIKE '%3%' FOR UPDATE;
```

例4: (无主键，table lock)

```
SELECT * FROM user WHERE name='Tom' FOR UPDATE;
```

以上就是悲观锁

FOR UPDATE仅适用于InnoDB，且必须在事务处理模块(BEGIN/COMMIT)中才能生效。 

## CAS

cas包含3个操作 内存值V，预估值A，更新值B



 乐观锁：主要就是两个步骤：冲突检测和数据更新。其实现方式有一种比较典型的就是CAS， **CAS是项乐观锁技术**，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。 

 ```SQL
update item set quantity=quantity-1 where quantity-1>0
 ```

通过quantity-1>0进行乐观锁控制，每次修改数据时先判断quantity-1>0  目的是查询quantity-1的值比如此时quantity=3，那就是3-1，就算有并发线程改变了quantity=2 变为2-1，此时赋值给quantity的还是之前查询到的为3-1，因此最终quantity就变成2了。



## 优缺点

1. 乐观锁并未真正加锁，效率高。一旦锁的粒度掌握不好，更新失败的概率就会比较高，容易发生业务失败。
2. 悲观锁依赖数据库锁，效率低。更新失败的概率比较低。

随着互联网三高架构（高并发、高性能、高可用）的提出，悲观锁已经越来越少的被应用到生产环境中了，尤其是并发量比较大的业务场景。