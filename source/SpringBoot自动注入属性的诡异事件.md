---
title: SpringBoot自动注入属性的诡异事件
categories:
  - SpringBoot
tags:
  - SpringBoot
  - 诡异
abbrlink: 796688297
---

在写项目时遇到的诡异属性注入事件,因为标签的id集合用逗号分割的字符串表示,在开发此功能时发现的一个离奇bug
<!--more-->
在写项目时遇到的诡异属性注入事件

比如下面这段代码

```java
 //添加或者修改文章
    @PostMapping("/blogs")
    public String post(Blog blog) {
        System.out.println(blog.getTagIds());//0
        return "redirect:/admin/blogs";
    }
```

blog实体类中

```java
private String tagIds;//标签的id字符串集合,用于业务
 public String getTagIds() {
        return "0";
    }
    public void setTagIds(String tagIds) {
        this.tagIds = tagIds;
    }
```

比如前台传来数据是**字符串**`1,2,3`但是blog实体类中的tagIds属性自动注入的值竟然是`0,1,2,3`,我搞不明白怎么会这么神奇,但是调用`getTagIds()`输出的却是`0`

很疑惑,或许得由将来的我来解答了!