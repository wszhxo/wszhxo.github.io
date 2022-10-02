---
title: 'Mybatis的一对多,多对一,多对多映射'
categories:
  - Mybatis
tags:
  - Mybatis
  - sql
abbrlink: 2083929677
---


应用场景:一篇文章有一个分类,一篇文章有多个标签,一个标签有多篇文章

<!--more-->

## 一对一映射

Blog类里有Type类对象

### 一个文章有一个分类

```java
public class Blog  implements Serializable {
    private Long id;
    private Type typeId;//种类
}
```

### mapper接口

```java
List<Blog> listBlog(Integer id);//列出所有文章
```

### 实现类

一对一**关键字assocaition**



```java
<resultMap id="blogMap" type="Blog">
        <result column="id" property="id"/>
        <association property="typeId" javaType="cn.coderzhx.blog2.po.Type">
            <result column="id" property="id"/>
            <result column="name" property="name"/>
        </association>
    </resultMap>    
    <!--id接口方法-->
<select id="listBlog" resultMap="blogMap" parameterType="Integer">
        select b.id,c.name from t_blog b  LEFT JOIN t_type c  ON b.type_id=c.id
         <where>
             <if test="typeId!=null">
                 and b.type_id=#{typeId}
             </if>       
         </where>
    </select>    
    
  
```

## 一对多映射

Type类中有Blog类的List集合

### 一个分类下有多个文章

```java
public class Type implements Serializable {
    private Long id;
    private String name;
    private Integer sum;//记录数量
    //该分类下的文章
    private ArrayList<Blog> blogs = new ArrayList<>();
}
```

### mapper接口

```java
Type listBLogByTypeId(Integer id);
```

### 实现类

- 调用listTypeById方法选出结果封装进resultMap中
- resultMap查询collection的方法,访问listBLogByTypeId参数是typeId的值
- 查询结果封装进Blog集合中,再把整个pageList封装进Type类中

![1571729940321](Mybatis的一对多,多对一,多对多映射/1571729940321.png)



## 多对多

**一个文章有多个标签,一个标签有多个文章**应该可以想象其中的关系

```java
public class Tag {
    private Long id;
    private String name;
    //该标签下的文章
    private List<Blog> blogs = new ArrayList<>();
}
```

```java
public class Blog  implements Serializable {
    private Long id;
    private String cover;//封面
    private String title;//标题
   //........
    //该文章下的所有标签
    private ArrayList<Tag> tags=new ArrayList<>();
```

### mapper接口

```java
Tag listBlogByTagId(Integer id);//根据标签id列出所有文章
```

- 执行listBlogByTagId返回blogMap  封装进Type 的blogs集合,
- 到collection中的listTagByBlogId,参数是文章id查询出所有标签,封装进Blog的tags集合
- 你中有我,我中有你

![1571731770349](Mybatis的一对多,多对一,多对多映射/1571731770349.png)

![1571732007546](Mybatis的一对多,多对一,多对多映射/1571732007546.png)

![1571732153511](Mybatis的一对多,多对一,多对多映射/1571732153511.png)

之间的关系是很乱的,我也是花费很久理清思路,具体代码我会开源在github中



