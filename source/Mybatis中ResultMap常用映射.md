---
title: Mybatis中ResultMap常用映射
categories:
  - Mybatis
abbrlink: 3608610497
---
Mybatis的ResultMap中collection和association的准确用法,以及常见的错误
<!--more-->

## collection和association



```xml
<resultMap id="pageList" type="cn.coderzhx.pojo.PageBean">
        <result column="index" property="index"/>
        <result column="数据库里的列" property="实体类里的属性"/>
        <collection property="list"  javaType="ArrayList" 	         ofType="Blog" resultMap="blogMap" >
</resultMap>
    <resultMap id="blogMap" type="Blog">
        <result column="category" property="category"/>
        <result column="likeit" property="likeit"/>
        <association property="blogCategory"       javaType="cn.coderzhx.entity.BlogCategory">    
            <result column="count" property="count"/>
            <result column="hits" property="hits"/>
        </association>
    </resultMap>
```

```java
public class PageBean<T> {
  
    // reslut
    private Integer index;
    //collection  ofType 里就是 T 实体类
   private List<T> list;
    //association 
    private BlogCategory blogCategory;
    
```

```xml
<select id="listBlogs" resultMap="blogMap" parameterType="cn.coderzhx.pojo.PageBean">
    select b.*,c.name from blog b LEFT JOIN blog_category c  ON b.category_id=c.id order by create_time
   <if test="index!=null and index!=''">
    limit #{index},#{pageSize};
    </if>
</select>
```

- ResultMap 的意义是存储结果集,结果集的列名就是column,他会把值放进property中

- 或者把参数传进去,只要名字一样,比如BlogCategory对象里的count属性作为参数#{count},就是property中的值

## 报selectOne错误

### 1.你想要多个结果,却用了这个方法

接口里边一定要定义为List<>集合类型,比如

<font color="red">我想查出所有的listBlogs放入List<Blog>再把list对象放进PageBean类里

ResultMap ="pageList"??,然后接口定义为这样?

PageBean list(PageBean pageBean)这是完全错误的!</font>

你必须写List<Blog> list(PageBean pageBean),你想要封装进实体类,那是service层干的事

### 2.你想要一个结果,却返回多个

那只能是数据库本身就有这么多数据





