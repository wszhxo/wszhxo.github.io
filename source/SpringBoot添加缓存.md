---
title: SpringBoot添加缓存
categories:
  - 配置
abbrlink: 2972964066
---



# SpringBoot添加缓存

博客项目中因为文章写一次之后基本不变了,这种不经常变更但是经常使用的数据,最适合缓存了,缓存是保存使用者的内存中.
<!--more-->
## 1.pom.xml加入依赖

```xml
 <!-- 缓存 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

## 2.Service层加入缓存注解

### 根据id查询文章

cacheNames是自定义的,第一次命名后,以后都会直接用这个缓存,而不会进入方法

```java
@Override
@Cacheable(cacheNames = "findBlogById")
public Blog findBlogById(int id) {
    System.out.println("缓存测试");
    return  blogMapper.findBlogById(id);
}
```

### 更新文章

根据id更新缓存同时更新数据库,blog是更新的变量名

```java
@Override
@CachePut(cacheNames = "findBlogById", key = "#blog.id")
public void editBlog(Blog blog) {
    blogMapper.editBlog(blog);
}
```

### 删除文章

根据id删除文章的同时删除缓存

```java
@Override
@CacheEvict(cacheNames = "findBlogById", key = "#id")
public void delBlog(Integer id) {
    blogMapper.delBlog(id);

}
```

### 在application启动类里加上注解```@EnableCaching```