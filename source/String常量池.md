---
title: String常量池
categories:
  - java基础
abbrlink: 2194052164
---


常量池引起的对象之间的比较
<!--more-->
```java
		String a="a";//(1)
		String b="a";
		System.out.println(a==b);//true
		String ab="a"+"b";//(2)
		String ab2="ab";
		System.out.println(ab==ab2);//true
		String aba="a"+"b"+a;//(3)
		String aba2="aba";
		System.out.println(aba==aba2);//false
		
```

## 1.

Sring类final修饰的,所以java虚拟机会在编译的时候就会把字面量(字面量"a"那就是a)放到常量池中

常量池只能有一份一样的字面量,比如变量a在常量池中已经有一份了,那么变量b来的时候只是字面量"a"的引用,所以a和b 都是指向同一个空间,所以为true

## 2.

这个和上面是一样的道理

## 3.

因为变量a是变量,所以不确定aba变量,默认new一份,既然是new 的当然不一样了,那就是false了

