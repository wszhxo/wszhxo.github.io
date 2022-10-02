---
title: 'HashSet,HashMap为什么要重写hashCode和equals?'
abbrlink: 3729986994
categories: 
- java基础
---


## Hash是数组和链表的混合,数组的每一项都是一个链表

<!--more-->

## 利用简单的例子阐述原理

### 先定义一个类

```
class Person1{
	public int age;
	public String name;
	public Person1(String name,int age) {
		this.age=age;
		this.name=name;
	}
	@Override
	public String toString() {
		return "name: "+name+"age: "+age;
	}
}
```

### 重写和不重写的区别



```
public class tanxinsuanfa {
	/**
	*创建人:
	*项目名:	
	*test
	*2019年4月16日-上午10:41:17
	*/
	
	public static void main(String[] args) {
		 
		HashSet<Person1> set=new HashSet<>();
		set.add(new Person1("abc",22));
		set.add(new Person1("abc",22));
		set.add(new Person1("bcd",22));
		set.add(new Person1("bcd",22));
		System.out.println(set);
		
		HashSet<String> set2=new HashSet<>();
		set2.add(new String("abc"));
		set2.add(new String("abc"));
		set2.add(new String("bcd"));
		set2.add(new String("bcd"));
		System.out.println(set2);
	}
}
```

```
[name: abc age: 22
, name: bcd age: 22
, name: bcd age: 22
, name: abc age: 22
]
[bcd, abc]


```

## 为什么Person1类有四个而String类结果只有两个?

Person1类4个都是new出来的新的不同对象,地址都不相同,equals方法默认判断地址是否相同,当然是false,

## 而String类内部实现了hashCode和equals方法

去掉了仅仅因为地址不同的重复对象

第一个对象new String("abc") 

```
public static int hashCode(byte[] value) {//String类底层方法
        int h = 0;
        for (byte v : value) {
            h = 31 * h + (v & 0xff);
        }
        return h;
    }
```

<font color=springgreen size=4>**0xff是16进制的255，也就是二进制的 11111111,当前字节与运算** </font>

```
1 1 1 1 1 1 1 1   =255
0 1 1 0 0 0 0 1   =97(ASCII码)=a   也就是255 & 97
0 1 1 0 0 0 0 1   =97 
```

​		v & 0xff其实就是v本身,所以我不知道大佬是什么想法这样做的目的 ,先不管了!

```
31 * 0 + 97  =97
31 * 97 + 98  =3105
31 * 3105 + 99  =96354
```

​		new String("abc") .hashCode()=96354

​			第二次又来了一个new String("abc")也是96354 ,集合在容器内找到了有相同hashCode的对象,所以要调用第二层equal方法

```
public static boolean equals(byte[] value, byte[] other) {
        if (value.length == other.length) {
            for (int i = 0; i < value.length; i++) {
                if (value[i] != other[i]) {
                    return false;
                }
            }
            return true;
        }
        return false;
    }
```

就是对象具体的值进行比较 "abc"="abc"所以返回true,因此是同一个,<font color=springgreen size=4>后来的覆盖先前的</font>

**可能存在偶然情况恰好hashCode是true,但是equals是false,只要有一个是false,那就是不同的对象**

## 想要达到同样的效果,因此我们要在Person类中也要重写hashCode和equals来达到去重的效果

## 比如  

```
	@Override
	public int hashCode() {
		return name.hashCode()+31*age;
	}
	@Override
	public boolean equals(Object obj) {
		if(this == obj)return true;
		if(obj == null)return false;//这两步每次重写都得有
		if(obj instanceof Person1){
			Person1 p = (Person1)obj;//强转类型
			return name.equals(p.name) && age==p.age;//比较值
		}
		return false;
	}
```

## 为什么不直接重写equals,而要两个都要呢?

equals为true hashCode一定是true,那么为什么还要重写hashCode呢

因为hashCode比的是数字速度快,而equals啥都比较,速度当然较慢,

所以先比较hashCode,相同了再equals,这样保证了效率

## HashMap比较的是键也是同样的原理

HashSet底层是依靠HashMap实现的,hashMap 的key有唯一性,如果出现重复的,会覆盖掉上一个,相对于HashSet来说那就是没变,实际上是把原来的覆盖了,1覆盖1,不还是1嘛!