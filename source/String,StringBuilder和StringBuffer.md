---
title: 'String,StringBuilder和StringBuffer'
categories:
  - java基础
abbrlink: 3857973081
---

## String是不可变的

因为String 方法底层是用 final修饰的char [] ,因此```String str="c"+"b"```这个拼接字符串是不会产生垃圾的,

<!--more-->

但如果

```java
String a="c";
a+="b";
```

| -栈  | -堆  |
| ---- | ---- |
| str  | "c"  |
|      | "b"  |
|      | "cb" |
|      |      |

最终指向"cb" 这就会产生垃圾

其实有三个堆空间,分别是 "cb", "c","b",而"c"和"b"变成了垃圾,出现了垃圾空间

## StringBuilder和StringBuffer

内容可以改变,底层使用char[]

他们拼接字符串,只需创建一个对象,是可变的

比如 StringBuilder a=new StringBuilder();

a.append("a");a.append("b");

|               |              |
| ------------- | ------------ |
| StringBuilder | StringBuffer |
| 线程不安全    | 线程安全     |
| 效率高        | 效率低       |





