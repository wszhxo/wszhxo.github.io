# docker 安装



先卸载旧Docler
```$xslt
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

安装

```
 yum install docker-ce docker-ce-cli containerd.io
```

启动docker守护进程

```
 systemctl start docker
```
docker 启动状态
```
systemctl status docker
```


打印docker版本

```
docker version 
```

## 启动失败情况
要注意安装那一步如果是这样的话,就说明安装失败了
```
    No package docker-ce available.
   No package docker-ce-cli available.
   No package containerd.io available.
   Error: Nothing to do
```
失败的话启动时就会出现如下的错误
```$xslt
Failed to start docker.service: Unit not found 
```

1. 卸载老版本的 docker 及其相关依赖

```
sudo yum remove docker docker-common container-selinux docker-selinux docker-engine
```
2，更新yum,这一步会很耗时,耐心等待
```
yum update
```
​ 3. 安装 yum-utils，它提供了 yum-config-manager，可用来管理yum源
```
sudo yum install -y yum-utils
```
​ 4. 添加yum源
```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
​ 5. 更新索引

centos7
```
sudo yum makecache fast
```
centos8
```
sudo yum makecache
```
上述都操作完成,就可以重新执行安装步骤​了



拉取hello-world镜像

```
docker pull hello-world
```

使用hello-world运行一个容器

```
docker run hello-world
```

# docker 使用

查看docker正在运行的容器
```java
docker ps
```
查看所有的docker容器
```java
docker ps -a 
```
查看所有的docker容器
```java
docker images
```
操作容器化的配置文件（举例nginx）
```java
docker exec -ti nginx /bin/bash
```
进入nginx目录修改配置文件
```java
cd /etc/nginx
```
安装vim命令
```java
apt-get update
apt-get install vim
```
进入mysql
```java
docker exec -it mysql5.7  bash
```

查看docker 可用的版本
```
 yum list docker-ce --showduplicates | sort -r
```

docker 端口映射问题
```java
   79  docker run –network host
   80  docker run –network host -p 3506:3506
   81  docker run --network host -p 8080:8080 -p 61616:61616 -p 5672:5672 -p 61613:61613 -p 5445:5445 -p 1883:1883
   82  docker run –-network host -p 3506:3506
   83  docker run –-network host -P 3506
   84  docker run –-network host -p 3506
   85  docker inspect mysql | grep IPAddress
   86  docker ps
   87  docker run -ti -d --name nysql -p 114.67.109.132:3506:3506 -p 127.0.0.1:3506:3506 mysql:5.7
   88  docker run -ti -d --name nysql -p 114.67.109.132:3506:3506 mysql:5.7
   89  docker run -ti -d --name mysql -p 114.67.109.132:3506:3506 mysql:5.7
   90  docker run -ti -d --name mysql -p 3506:3506 mysql:5.7
   91  docker ps
   92  firewall-cmd --permanent --zone=trusted --change-interface=docker0
   93  firewall-cmd --reload
   94  iptables -t nat -N DOCKER
   95  systemctl stop docker
   96  cat /etc/docker/daemon.json
   97  systemctl restart docker
   98  docker ps
   99  docker rm 26822941fcbe
  100  docker pull redis:4.0.9
  101  mkdir -p /mydata/redis/conf
  102  touch /mydata/redis/conf/redis.conf
  103  docker run -p 6479:6379 --name redis -v /mydata/redis/data:/data -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf -d redis:4.0.9 redis-server /etc/redis/redis.conf
  104  cd /mydata/redis/conf
  105  ls
  106  vi redis.conf '
  107  vi redis.conf

```
