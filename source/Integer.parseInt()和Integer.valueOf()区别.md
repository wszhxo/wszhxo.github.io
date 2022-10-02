---
title: Integer.parseInt()和Integer.valueOf()区别
categories:
  - java基础
abbrlink: 701936335
---


Integer.parseInt()返回int类型

Integer.valueOf()返回Integer类型

<!--more-->

**底层实现都是Integer.parseInt(String s ,int radix)**

Integer.valueOf(String s ,int radix)其实就是

new Integer(Integer.parseInt(s, radix))

radix是进制数在2到36之间,因为0-9,a-z正好只够36个的

```java
public static Integer valueOf(int i) {
        /*IntegerCache.low为 -128；
        IntegerCache.high默认为127，但可以在JVM进行配置，一般默认就是127*/
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            /*如果i在-128~127之间，从缓存中取对象*/
            return IntegerCache.cache[i + (-IntegerCache.low)];
        /*如果i不在-128~127之间，重新创建一个对象*/
        return new Integer(i);
    }
```

## 这就解释了为什么

```java
        Integer cInteger=127;
		Integer dInteger=127;
		System.out.println(cInteger==dInteger);//true
		Integer cInteger2=128;
		Integer dInteger2=128;
		System.out.println(cInteger2==dInteger2);//false
```

因为超过了127就是新的Integer对象

而==比较是地址是否相同,对象不同自然不同了.

对于基本类型==就是比较的值是否相同














