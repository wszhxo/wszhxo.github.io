# mysql获取每种类型的前几条数据

> 没搞懂原理，先记录

方法1

```sql
# 举例 获取最后的前3条  ，获取前边的3条把t2.id>=t1.id改成小于即可
select * from A  as t1
WHERE (SELECT COUNT(*) FROM A as t2 WHERE t1.type_id=t2.type_id AND t2.id>=t1.id) <=3
```

方法2

```sql
select t1.*,count(*) as num from A as t1 inner join A as t2 ON t1.type_id=t2.type_id where t2.id>=t1.id group by t1.id having num<=3;
```

