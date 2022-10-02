---
title: 访问网站的IP和网站总浏览量
categories:
  - 业务实现
abbrlink: 1894089074
---


我的网站项目要实现访问网站人数统计:同一个ip浏览多次,只会加浏览量
<!--more-->
## 实现思路:

- 创建数据库表,主键自增,记录ip,访问时间,ip访问次数,访问浏览器
- 取出数据库数据放到List集合中,再转化为Map,键:ip,值:该行数据
- 用户访问网站记录ip地址,map.containsKey(ip)方法判断是否在map中
- 若有则该ip的访问次数+1,若无,则创建entity对象,把对象插入数据库

### 获取ip'地址方法

```java

public static String getIpAddr(HttpServletRequest request) {
        String ip = request.getHeader("X-Real-IP");
        if (ip!= null && !"".equals(ip) && !"unknown".equalsIgnoreCase(ip)) {
            return ip;
        }
        ip = request.getHeader("X-Forwarded-For");
        if (ip!= null && !"".equals(ip)  && !"unknown".equalsIgnoreCase(ip)) {
            // 多次反向代理后会有多个IP值，第一个为真实IP。
            int index = ip.indexOf(',');
            if (index != -1) {
                return ip.substring(0, index);
            } else {
                return ip;
            }
        } else {
            return request.getRemoteAddr();
        }
    }
```

## 注意点

每次访问网站主页,考虑到性能,不能总是select * from 数据表,然后每次都要转化为Map,

因此使用init方法创建一个变量,服务器加载select * from 数据表,存到这个List变量

顺便转化为Map,访问主页时获取变量判断ip是否在其中

