---
title: IDEA热部署配置
categories:
  - 配置
abbrlink: 1394761062
---
## 关于Spring Boot 项目的热部署

## 如果是Gradle构建

只需在application.properties中加入以下
<!--more-->

## 如果是Maven要在pom.xml加入

### 热部署生效

spring.devtools.restart.enabled: true
### 设置重启的目录

spring.devtools.restart.additional-paths: src/main/java

### classpath目录下的WEB-INF文件夹内容修改不重启

spring.devtools.restart.exclude: WEB-INF/**

热部署jar包
spring-boot-devtools

Ctrl+Shift+Alt+/  选择Register

![1560308442807](IDEA热部署配置/1560308442807.png)

勾选 Close就好了

![1560308343209](IDEA热部署配置/1560308343209.png)

集成mybatis

mybatis-spring-boot-starter 

