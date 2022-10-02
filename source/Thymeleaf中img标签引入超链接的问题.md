---
title: Thymeleaf中img标签引入超链接的问题
categories:
  - Thymeleaf
abbrlink: 1534426965
---


# Thymeleaf中img标签引入超链接的问题

因为我把图片全部存储在七牛云,仅仅使用一个超链接,但是img标签th:src默认加上localhost:8080

```html
<img   th:src="${config.wechat_icon}" />
```

导致路径变成了,访问就报错了

<!--more-->

```html
<img src="http://localhost:8080/puu1qclkk.bkt.clouddn.com/xxxx">
```

其实解决非常简单,但我查阅了各种资料,花了几个小时,只需要加上 **||** 就可以了!

```html
<img   th:src="|${config.wechat_icon}|" />
```

渲染得到的效果就是正常了

```html
<img src="http://puu1qclkk.bkt.clouddn.com/xxxx">
```



这个问题可把我憋屈坏了!