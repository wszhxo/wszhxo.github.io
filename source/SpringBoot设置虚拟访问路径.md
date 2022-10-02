---
title: SpringBoot设置虚拟访问路径
categories:
  - SpringBoot
tags:
  - 图片上传
  - SpringBoot
  - Linux
abbrlink: 2284355318
---


> 网站的图片上传到Linux后,发现访问的时候404,考虑到Linux图片的访问权限,修改chmod  777 赋予所有权限,发现还是不行,经过一个小时终于发现问题所在

<!--more-->

上传图片就不说了,博客中搜索`上传图片`关键字第一篇就是了

## 通过url直接访问图片

需要写一个配置类

```java
@Configuration
public class WebPathFigurer implements WebMvcConfigurer {
    @Value("${file.uploadfolder}")
    private String uploadfolder;
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {  
 registry.addResourceHandler("/upload/**").addResourceLocations("file:"+uploadfolder);
 	//linux下的配置 uploadfolder=/usr/upoad/
       //windows下是"file:///"  uploadfolder=D://upload/
    }

}
```

`addResourceHandler`表示图片的访问路径`ip:端口/upload/...`

``addResourceLocations`表示图片上传到这个目录







