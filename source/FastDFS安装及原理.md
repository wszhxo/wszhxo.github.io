# FastDFS安装及原理

>  FastDFS(Fast Distributed File System)是一款开源轻量级分布式文件系统 

#### 安装libfastcommon

1. 获取libfastcommon安装包：

   ```
   wget https://github.com/happyfish100/libfastcommon/archive/V1.0.38.tar.gz
   ```

2. 解压安装包：tar -zxvf V1.0.38.tar.gz

3. 进入目录：cd libfastcommon-1.0.38

4. 执行编译：./make.sh

5. 安装：./make.sh install

可能遇到的问题：

```
-bash: make: command not found
-bash: gcc: command not found解决方案：
debian通过apt-get install gcc make安装
centos通过yum -y install gcc make安装
```

#### 安装FastDFS

1. 获取fdfs安装包：

   ```
   wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz
   ```

2. 解压安装包：tar -zxvf V5.11.tar.gz

3. 进入目录：cd fastdfs-5.11

4. 执行编译：./make.sh

5. 安装：./make.sh install

6.  查看可执行命令：ls -la /usr/bin/fdfs* 

#### 配置Tracker服务

1. 进入/etc/fdfs目录，有三个.sample后缀的文件（自动生成的fdfs模板配置文件），通过cp命令拷贝tracker.conf.sample，删除.sample后缀作为正式文件：

2. 编辑tracker.conf：vi tracker.conf，修改相关参数

   ```yml
   base_path=/home/mm/fastdfs/tracker  #tracker存储data和log的跟路径，必须提前创建好
   port=22122 #tracker默认22122
   http.server_port=81 #随意
   ```

3. 启动tracker（支持start|stop|restart）：

   ```
   /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
   ```

4. 查看tracker启动日志：进入刚刚指定的base_path(/home/mm/fastdfs/tracker)中有个logs目录，查看tracker.log文件

5. 查看端口情况：netstat -apn|grep fdfs

> 可能遇到的报错：
>
> ```
> /usr/bin/fdfs_trackerd: error while loading shared libraries: libfastcommon.so: cannot open shared object file: No such file or directory
> 解决方案：建立libfastcommon.so软链接
> ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
> ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
> ```

#### 配置Storage服务

1. 进入/etc/fdfs目录，有cp命令拷贝storage.conf.sample，删除.sample后缀作为正式文件;

2. 编辑storage.conf：vi storage.conf，修改相关参数：

   ```yml
   base_path=/home/mm/fastdfs/storage   #storage存储data和log的跟路径，必须提前创建好
   port=23000  #storge默认23000，同一个组group的storage端口号必须一致
   group_name=group1  #默认组名，根据实际情况修改
   store_path_count=1  #存储路径个数，需要和store_path个数匹配
   store_path0=/home/mm/fastdfs/storage  #如果为空，则使用base_path
   tracker_server=10.122.149.211:22122 #配置该storage监听的tracker的ip和port，需要在服务器开放22122端口并使用内网ip，我使用公网ip出错
   http.server_port=82 # nginx中配置的监听端口保持一致
   ```

   同理修改client。conf配置文件

   ```yml
   base_path=/home/fastdfs # 这个路径是提前创建好的
   tracker_server=自己虚拟机的IP地址:22122
   http.tracker_server_port=82
   ```

   **创建目录用于存储文件**

   mkdir -m 777 -p  /home/fastdfs

3. 启动storage（支持start|stop|restart）：

   ```
   /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
   ```

4. 查看storage启动日志：进入刚刚指定的base_path(/home/mm/fastdfs/storage)中有个logs目录，查看storage.log文件

5. 此时再查看tracker日志：发现已经开始选举，并且作为唯一的一个tracker，被选举为leader
   ![img](https://www.cnblogs.com/handsomeye/p/media/15336343653165/15337312505783.jpg)￼![img](https://images2018.cnblogs.com/blog/872887/201808/872887-20180809201950722-1959554309.jpg)

6. 查看端口情况：netstat -apn|grep fdfs

7. 通过monitor来查看storage是否成功绑定：

   ```xml
   /usr/bin/fdfs_monitor /etc/fdfs/storage.conf
   ```

![1600069626889](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1600069626889.png)

查看端口使用情况  **netstat -tnlp**

### 测试上传功能

```yml
fdfs_test /etc/fdfs/client.conf upload /img/404.jpg
```

![1600073892621](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1600073892621.png)

### 删除文件

```yml
fdfs_delete_file /etc/fdfs/client.conf group1/M00/00/00/rBEAD19fLryAMdFdAABACdpQVl8723.jpg # 就是上图中的remote_filename
```



## 配置Nginx

```yml
sudo su #切换到管理员
```

**以下三个插件是必须的**

### 安装pcre

```go
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz
tar -zxvf pcre-8.42.tar.gz
cd pcre-8.42
./configure
make
make install
```

**记得安装完返回上一级目录，不然文件会很乱**

### 安装openssl

```go
wget https://www.openssl.org/source/openssl-1.1.1.tar.gz
tar -zxvf openssl-1.1.1.tar.gz
cd openssl-1.1.1
./config
make
make install
```

### 安装zlib

```go
wget http://prdownloads.sourceforge.net/libpng/zlib-1.2.11.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
cd  zlib-1.2.11
./configure
make
make install
```









假如启动时卡住了，报错：ERROR - file: storage_ip_changed_dealer.c, line: 180, connect to tracker server 172.16.1.11:22122 fail, errno: 113, error info: No route to host.

解决方法：关闭tracker防火墙

/etc/init.d/iptables stop

