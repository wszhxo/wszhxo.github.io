---
title: Java深克隆浅克隆
categories:
  - Java基础
tags:
  - 克隆
abbrlink: 1122159324
---


> 之前我只听说过这个概念,以为很深奥的,但是随着自己慢慢学习java过程中很通俗的了解了什么是深克隆浅克隆?

<!-- more -->


## 浅克隆

我们都知道一个对象是存储在堆内存中的,对象的引用来源于此,基本数据类型不存在这个问题

```java
Person p1=new Person();
Person p2=p1;
```

`p1`和`p2`都指向同一个Person对象,当`p1`和`p2` 其中任意一个修改了Person的值那么Person对象就会发生改变,所以直接赋值就是浅克隆.

## 深克隆

我们需要产生一个完完全全的副本,类似于复印件供别人随意使用,修改,最简单的做法就是新建一个对象然后统统set进去,如果出现一个List集合那么**工作量是巨大的**,比如:

```java
Person p1=new Person();
Person p2=new Person();
p2.setName(p1.getName());//等等等.....
//出现集合,还需要遍历添加到新的集合中,因为集合对象也是存在堆中的
List<String> list=new ArrayList<>();
forEach(String p2:p1.getList()){
    list.add(p2)//循环添加
}
p2.setList(list);//最后赋值
```

可想而知,如果实体类里多来几个集合那么,代码会变得很多,**只需要实现接口即可**

```java
Person implements Cloneable {
    //属性省略....
    @Override
    public Person clone() {
        Person person = null;
        try {
            person = (Person) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return person;
    }
}
```

```java
Person p1=new Person();
Person p2=p1.clone();//深克隆
```



