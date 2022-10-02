---
title: 定时任务Quartz
categories:
  - 业务实现
tags:
  - SpringBoot
  - Quartz
abbrlink: 868183855
---


> 由于网站访问量统计的次数+1的操作非常频繁还有文章访问量统计,不可能说每次访问数据库去+1,这样数据库压力是非常大的.这就需要用到定时任务,比如每隔一天把上一天统计的数添加到数据库,中间要用到一个静态变量存储计数,再把这个数加入数据库!

<!--more-->

## 添加依赖

```xml
<dependency>
<groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-quartz</artifactId>
 </dependency>
```

## 编写要定时的业务类

```java
public class ViewsQuartz extends QuartzJobBean {
  
    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        //这里随意写
        System.out.println("测试定时任务");
    }

}
```

## 定时任务配置类

```java
import cn.coderzhx.blog2.handler.ViewsQuartz;
import org.quartz.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author zhx
 * @create 2019-11-02-17
 */
@Configuration
public class QuartzConfig {
//这里随便写
    private static final String VIEWS_IDENTITY = "viewsQuartz";

    @Bean
    public JobDetail quartzDetail(){
        return JobBuilder.newJob(ViewsQuartz.class).withIdentity(VIEWS_IDENTITY).storeDurably().build();
    }

    @Bean
    public Trigger quartzTrigger(){
        SimpleScheduleBuilder scheduleBuilder = SimpleScheduleBuilder.simpleSchedule()
                .withIntervalInSeconds(1)  //设置时间周期单位秒
//                .withIntervalInHours(2)  //两个小时执行一次
                .repeatForever();
        return  TriggerBuilder.newTrigger().forJob(quartzDetail())
                .withIdentity(VIEWS_IDENTITY)
                .withSchedule(scheduleBuilder)
                .build();
    }
}
```

**这样启动Appliaction就会一秒执行一次ViewsQuartz的executeInternal方法**

