

## linux相关操作

top 查不到则是用

```
ps -eo pid,pcpu | sort -n -k 2 
```

进入cpu过高的pid

```
ps -aux |grep -v grep|grep 22448
```

进入pid 的目录

```
cd /proc/22448
```

```
ls -ail
```

![image-20210325144753392](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210325144753392.png)

cwd进程所在目录

exe进程类型



## linux 开放端口

```
查看防火墙状态
查看防火墙状态 systemctl status firewalld
开启防火墙 systemctl start firewalld  
关闭防火墙 systemctl stop firewalld
开启防火墙 service firewalld start 
若遇到无法开启
先用：systemctl unmask firewalld.service 
然后：systemctl start firewalld.service
查看已经开启的端口 firewall-cmd --list-ports

查看想开的端口是否已开：
firewall-cmd --query-port=6379/tcp
添加指定需要开放的端口：
firewall-cmd --add-port=123/tcp --permanent
重载入添加的端口：
firewall-cmd --reload
查询指定端口是否开启成功：
firewall-cmd --query-port=123/tcp


移除指定端口：
firewall-cmd --permanent --remove-port=9000/tcp
```

![image-20210326103435493](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210326103435493.png)

B?sRkL0HZ4WI







### delta测试

```
tail -f -n 1000 api-service_info.log
```

```
grep "aaa" api-service_info.log
```

```
sh rebuild_delta.sh
```

```
cd delta_project/ 
cd logs/
vim api-service_info.log
vim rebuild_delta.sh
```







### minio测试

文档地址https://fe.che300.com/easymock/wikiCatalog/markdown/6052f17af165d82dc3ce9be4

```
tail -300f log/minio-client/info.log
find / -name minio

 ps -ef|grep minio

ll /proc/32133
cd /opt/minio/

cd log/

cd minio-client/
ls
```



```
Xshell 6 (Build 0204)
Copyright (c) 2002 NetSarang Computer, Inc. All rights reserved.

Type `help' to learn how to use Xshell prompt.
[C:\~]$ 

Connecting to 127.0.0.1:55905...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.
Warning: The host key of 127.0.0.1 (port: 55905) is not registered.
The authenticity of host '127.0.0.1:55905 (127.0.0.1:55905)' can't be established.
RSA key fingerprint is MD5:27:62:13:9a:5c:6c:75:b7:6e:f2:cc:5a:82:ae:75:86.
RSA key fingerprint is SHA256:4f9J9Jo7NBKXBDOMoiiHSfJv84dLwz4xp3bVg5SgiVU.
+---[RSA 2048]----+        +---[RSA 2048]----+
|        . . .    |        |       .Eo+.     |
|     . . . . .   |        |..    o o .o.    |
|      =     .    |        |o+ . + +.. o .   |
|   . = .   .     |        |+ + o o. .+ o    |
|    + + S o o    |        | o .    S. =.    |
|     . o = *     |        |    +.o ..+.=.   |
|        E + =    |        |   . o+* ..o.o.  |
|       o o +     |        |     o*.o  o.+   |
|      ... .      |        |     .o=    *o   |
+------[MD5]------+        +----[SHA256]-----+

Are you sure you want to continue connecting (yes/no)? yes 
Warning: Permanently added '127.0.0.1' (RSA) to the list of known hosts.

Last login: Tue Mar 30 16:38:56 2021 from 10.10.160.50
[htapp@zles_pinggu_app_dev ~]$ sudo root
[sudo] password for htapp: 
htapp is not in the sudoers file.  This incident will be reported.
[htapp@zles_pinggu_app_dev ~]$ su root
Password: 
[root@zles_pinggu_app_dev htapp]# cd /opt/minio
[root@zles_pinggu_app_dev minio]# ll
total 0
drwxr-xr-x. 4 minio root 110 Mar 30 16:12 client
drwxr-xr-x. 2 minio root  26 Mar 16 14:26 plugin
drwxr-xr-x. 2 minio root  36 Mar 24 11:55 server
drwxr-xr-x. 2 htapp root  30 Mar 30 16:40 test
[root@zles_pinggu_app_dev minio]# rm -r test
rm: descend into directory ‘test’? 
[root@zles_pinggu_app_dev minio]# ll
total 0
drwxr-xr-x. 4 minio root 110 Mar 30 16:12 client
drwxr-xr-x. 2 minio root  26 Mar 16 14:26 plugin
drwxr-xr-x. 2 minio root  36 Mar 24 11:55 server
drwxr-xr-x. 2 htapp root  30 Mar 30 16:40 test
[root@zles_pinggu_app_dev minio]# rm -r test
rm: descend into directory ‘test’? y
rm: remove regular file ‘test/minio-client.jar’? y
rm: remove directory ‘test’? y
[root@zles_pinggu_app_dev minio]# ll
total 0
drwxr-xr-x. 4 minio root 110 Mar 30 16:12 client
drwxr-xr-x. 2 minio root  26 Mar 16 14:26 plugin
drwxr-xr-x. 2 minio root  36 Mar 24 11:55 server
[root@zles_pinggu_app_dev minio]# cd client
[root@zles_pinggu_app_dev client]# ll
total 704
-rw-r--r--. 1 minio minio 614468 Mar 29 18:53 f2d1bbbd48767ce216377ebb6d636454_1561979671757.pdf
drwxr-xr-x. 2 minio root    4096 Mar 24 11:49 lib
drwxr-xr-x. 3 minio root      26 Mar 16 15:54 log
-rw-r--r--. 1 minio root   93457 Mar 30 16:10 minio-client.jar
[root@zles_pinggu_app_dev client]# rm -f minio-client.jar 
[root@zles_pinggu_app_dev client]# rm -f f2d1bbbd48767ce216377ebb6d636454_1561979671757.pdf 
[root@zles_pinggu_app_dev client]# rz
# 先把supervisor 停了 再删除原来的jar 
[root@zles_pinggu_app_dev client]# chown minio minio-client.jar 
[root@zles_pinggu_app_dev client]# ll
total 100
drwxr-xr-x. 2 minio root  4096 Mar 24 11:49 lib
drwxr-xr-x. 3 minio root    26 Mar 16 15:54 log
-rw-r--r--. 1 minio root 93339 Mar 30 16:05 minio-client.jar
[root@zles_pinggu_app_dev client]# tail -f /mnt/log/supervisor/client_minio.log 
16:54:45.648 [main] INFO  org.apache.catalina.core.ContainerBase.[Tomcat].[localhost].[/] - Initializing Spring embedded WebApplicationContext
16:54:45.649 [main] INFO  org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext - Root WebApplicationContext: initialization completed in 2756 ms
16:54:45.791 [main] INFO  com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceAutoConfigure - Init DruidDataSource
16:54:46.034 [main] INFO  com.alibaba.druid.pool.DruidDataSource - {dataSource-1} inited
Logging initialized using 'class org.apache.ibatis.logging.stdout.StdOutImpl' adapter.
16:54:46.479 [main] INFO  org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor - Initializing ExecutorService
16:54:46.482 [main] INFO  org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor - Initializing ExecutorService 'imageTransferExecutor'
16:54:46.952 [main] INFO  org.apache.coyote.http11.Http11NioProtocol - Starting ProtocolHandler ["http-nio-8180"]
16:54:46.978 [main] INFO  org.springframework.boot.web.embedded.tomcat.TomcatWebServer - Tomcat started on port(s): 8180 (http) with context path ''
16:54:46.989 [main] INFO  com.che300.MinioApplication - Started MinioApplication in 5.771 seconds (JVM running for 7.011)
^[[D^C
[root@zles_pinggu_app_dev client]# 

```

新开一个窗口操作supervisor

![image-20210330170514970](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210330170514970.png)

查看应用运行情况

```java
ps -ef | grep tomcat
```
查看进程id
```java
netstat -nap | grep 1095
```
查看进程id详细信息
```java
ps -ef | grep 1095
```

查看端口
```java
netstat -tunlp | grep 8080
```

启动jar
```java
nohup java -Xms512m -Xmx512m -jar -Dspring.profiles.active=prod /usr/local/beitai-1850/beitai-admin.jar &
```

查看磁盘占用情况
```
df -h
```
查看inodes占用情况(查看文件夹是否过多)
```
df -h
```

进入对应的目录查看文件夹占用情况
```
   du -ah --max-depth=1  /目录
```

设定内存启动
 ./program > /dev/null 2>&1&表示不产生任何信息和日志
```java
nohup java -Xms512m -Xmx512m -jar -Dspring.profiles.active=prod beitai-kafka.jar ./program > /dev/null 2>&1&
```
