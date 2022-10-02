# 项目开发文档

# 文档更新日志

| 文档版本号 | 系统版本号 | 更新内容                                      | 更新人 | 更新时间   |
| ---------- | ---------- | --------------------------------------------- | ------ | ---------- |
| 0.1        | 0.1        |                                               | 张洪祥 | 2021-06-02 |
| 0.2        | 0.2        | 加入pdf套打功能，声请人同意申明在服务器做留存 | 张洪祥 | 2021-06-10 |

# 项目逻辑介绍

### 系统逻辑概述

#### 系统简介

MinIO是Apache的对象存储服务。它兼容亚马逊S3云存储服务接口，非常适合于存储大容量非结构化的数据，例如图片、视频、日志文件等。与市面上主流的阿里云OOS，七牛云，腾讯云，FastDFS等存储服务具有 部署简单，收费低，高容灾等优点，而且文档齐全。

中文文档 ：http://docs.minio.org.cn/docs 英文文档： https://docs.min.io/

> 亚马逊S3：一个业界认可的底层服务。类似国内各大手机厂商开发自己的手机系统，但底层都是基于Android系统开发。
>
> 非结构化的数据：mysql，Oracle中就是二维表维护的结构化数据，而图片，视频就是非结构化数据。
>
> 对象存储：在对象存储系统里，你不能直接打开/修改文件，只能先下载、修改，再上传文件比如百度网盘。不同于文件存储，对象存储没有文件系统中层级关系，通过bucket，key来区分数据，比如上传文件fileName到叫做xxx的bucket，key是dir/dir/fileName, 根据这个key，就能找到物理存储位置 你当然也可以把这个key理解为目录层级。

MinIO分为服务端和客户端，服务端直接通过minio.exe启动就可以通过web端可视化管理文件，

本系统就是MinIO客户端，根据Minio提供的SDK开发的一个文件存储服务。提供通用接口，用于文件管理。

# 开发环境

Jdk版本：1.8

数据库版本：5.7

数据库连接客户端：SQLyog、Navicat

开发IDEA：IntelliJ IDEA

# 项目工具

## 接口文档管理：yapi

### 地址：http://yapi.devops.guchele.com/project/272/interface/api/cat_4743

## 代码仓库：Git

### 地址：http://gitlab.devops.guchele.com/bussiness/minio

## 依赖管理：maven

### 私服地址：http://118.25.27.178:8081/

# 项目依赖

## 依赖引入要求

\#待补充

## 依赖管理文档

\#待补充

# 系统代码目录说明

## minio

### 简介

通过minIO提供的java sdk开发的文件管理服务

### 目录介绍

├─java
│  └─com
│      └─che300
│          ├─http
│          │  ├─model 存放多模块通用常量及模型
│          │  └─util 请求工具类
│          ├─minio
│          │  ├─config 线程池，跨域，拦截请求配置类
│          │  ├─constant 常量参数
│          │  ├─controller 请求入口
│          │  ├─enumeration 系统枚举类
│          │  ├─exception 自定义异常
│          │  ├─interceptor 相关请求拦截器
│          │  ├─manager 异常处理
│          │  ├─mapper 持久化操作
│          │  ├─model 实体类
│          │  ├─pojo 数据库映射实体类
│          │  ├─service 逻辑处理层
│          │  └─util 
│          ├─sign  验证签名处理模块
│          │  ├─annotation 注解
│          │  ├─aspect 切面
│          │  ├─constant 常量
│          │  ├─enumeration 枚举常量
│          │  ├─strategy 验证签名具体实现
│          │  └─util 签名工具类
│          ├─transfer  谷歌插件截图功能模块
│          │  ├─config  谷歌插件配置类
│          │  ├─constant 常量
│          │  ├─controller 请求入口
│          │  ├─model 实体类
│          │  └─service 逻辑处理层
│          └─visitor  bis_visitor表的持久化操作
│              ├─mapper
│              ├─pojo
│              └─service

# 数据库概要设计

```java
CREATE TABLE `bis_visitor` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增ID',
  `visitor_name` varchar(32) NOT NULL COMMENT '访问者名称',
  `token_key` char(16) NOT NULL COMMENT '访问令牌key',
  `token_secret` varchar(16) NOT NULL COMMENT '访问令牌secret',
  `enabled` tinyint(4) unsigned NOT NULL DEFAULT '1' COMMENT '是否可用',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_token_key` (`token_key`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='访问权限信息表';

```

![](https://assets.che300.com/wiki/2021-06-02/16226249346177944.png)



# 代码开发约定

## 常见约定

\#待补充

## Java代码规范

\#待补充

## 列表接口实现规范

此处为语雀文档，点击链接查看：https://www.yuque.com/docs/share/55079179-a436-4f21-8f82-5c15b9fb661d?# 《列表接口实现规范》

## 配置文件管理规范

此处为语雀文档，点击链接查看：https://www.yuque.com/docs/share/3693d556-8cd5-4cc3-b36d-e596307fbe92?# 《配置文件管理规范》

## 静态常量管理规范

此处为语雀文档，点击链接查看：https://www.yuque.com/docs/share/b719873c-ef7c-4be5-8c82-273aaef82786?# 《静态常量管理规范》

## 事务管理规范

\#待补充

## Java代码注释规范

此处为语雀文档，点击链接查看：https://www.yuque.com/docs/share/b2180778-25d6-4aa7-a765-dd0c6c7247e3?# 《Java代码注释规范》

## 工具类编写规范

此处为语雀文档，点击链接查看：https://www.yuque.com/docs/share/2146aad6-c19f-4ff0-8b05-ffcabca3048f?# 《工具类编写规范》

## 代码分层规范

\#待补充

# 配置文件说明

### 说明

介绍系统内配置文件中各配置选项，目前包含application-local.yml本地配置，application-test.yaml测试配置，application-prod.yml生产配置，application-press.yaml压测配置,log4j2日志打印格式配置

#### application-local.yml

```
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://39.100.87.64:3306/minio?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true
    username: minio
    password: minio@user
    type: com.alibaba.druid.pool.DruidDataSource
  aop:
    proxy-target-class: true
    auto: true
  servlet:
    multipart:
      max-file-size: 30MB
      max-request-size: 30MB

server:
  port: 8080
mybatis:
  mapper-locations: classpath*:mybatis/mapper/*/*.xml
  config-location: classpath:mybatis/mybatis-config.xml

minio:
  server:
    url: http://172.16.0.99:9000/
    nginx-proxy-url: http://172.16.0.99:9000/
    accessKey: minio
    secretKey: minioadmin
    file-separator: /
  bucket-chunk: chunk
  bucket-origin: origin
  bucket-garbage: garbage
  garbage:
    expire-day: 1

external:
  huishang:
    token: b5a1f904c2b241e580590541bf19f874

webdriver:
  chrome:
    driver: D:\codeAPP\chromedriver\chromedriver.exe
```

## 服务器信息

- minio服务器信息
  - ​    开发环境  172.16.0.99 
    -  管理员 root      rootche300     
    - 文件保存位置  /home/dev/minio/data/     
    - minio启动脚本  /home/dev      minioclient_reload.sh
  - ​    测试环境  118.25.27.178    
    -  用户yangwei       autohome.com.cn/   
    - 管理员 root   big@data!@#
    - 文件保存位置 /data2/minio/data
    - 其他相关存储位置 /data2/minio 
    - 启动脚本/data2/minio/script/
- 
-   minio文件管理端地址  
  -    开发环境  http://172.16.0.99:9000/              minio/minioadmin
  -    测试环境 http://118.25.27.178:9000/       minio/s2vy0MfhBmeXJczj



# 自动化部署方案

启动脚本

# 项目维护人员

| 版本号 | 前端 | 服务端 | 时间 | 备注 |
| ------ | ---- | ------ | ---- | ---- |
| 0.1    | --   | 张洪祥 |      |      |

目前有两个版本；

​	线上分支： 徽商master分支 和 汇通 huitong分支 

master_dev开发分支

master_zhx开发分支 

## 待改进

- 用到了mysql，但是里边只有一张表，存储了token，代码冗余，目前来看完全可以通过配置文件实现。先开一个分支改进，如果以后用到了数据库可以再接着拓展。
- 系统中有没用到的代码，具为MinioMapper.xml相关的代码
- 系统中的异常处理不恰当使用抛出方式，后续修改为捕获异常配合日志使异常粒度更小。
- 日志处理待优化

### 系统目前存在bug

存在上传图片有缺失情况，缺失部分显示黑色。

存在网页截图功能超时情况。



