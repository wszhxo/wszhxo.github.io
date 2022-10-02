---
title: Mybatis新增之后返回新数据id
categories:
  - Mybatis
abbrlink: 3761097269
---


项目中用到了新增文章和标签功能，因为文章有多个标签，文章表设置了自增id，只能等到添加完成后返回id再添加标签表的外键，所以产生了这个问题
<!--more-->
```xml

 <insert id="addBlog" parameterType="blog" useGeneratedKeys="true" keyProperty="id" keyColumn="id">
    </insert>
```

keyColumn数据库主键字段，keyProperty实体类映射字段，useGeneratedKeys="true"，我也不知道，我只知道要加上这个

## 接口```void addBlog(Blog blog)```

获取id只需要``` blog.getId()```因为他会自动映射进id属性