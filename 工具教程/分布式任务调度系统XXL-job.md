# 分布式任务调度系统XXL-job

首先下载管理端代码https://gitee.com/xuxueli0323/xxl-job.git

执行相关sql,修改配置文件,启动XxlJobAdminApplication

最重要的模块是任务管理和执行器管理

## 执行器管理

AppName对应项目名  ,项目中要配置config类读取配置文件并封装成了一个XxlJobSpringExecutor实体

第二是jobhandler类,里边注明了各种定时任务加上注解@XxlJob即可,启动该项目一段延迟后就会在对应执行器中注册为一个节点

## 任务管理

任务管理可以定义各种任务,也就是在对应执行器中写好的用注解Xxljob修饰的方法.

