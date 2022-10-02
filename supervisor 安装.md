

# supervisor

## 安装

```javascript
cd /usr/local/src
```

```
wget https://pypi.python.org/packages/7b/17/88adf8cb25f80e2bc0d18e094fcd7ab300632ea00b601cbbbb84c2419eae/supervisor-3.3.2.tar.gz
```

```
tar -zxvf supervisor-3.3.2.tar.gz
```

```
cd supervisor-3.3.2
```

```
python setup.py install #本地python版本为python2.7
```

## 配置

1.生成配置文件

```javascript
echo_supervisord_conf > /etc/supervisord.conf
```

修改supervisord.conf文件中最底下两行改为下边的

```
[include]
files = /etc/supervisor/*.conf
```

2.启动

```javascript
supervisord -c /etc/supervisord.conf
```

> 此时启动也许会报错，提示/var/log/supervisord/tornado_server.log找不到
>
> 因此需要在对应目录创建tornado_server.log文件里边写入[log for main_server] 保存
>
> ![image-20210330092956734](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210330092956734.png)

3. 用supervisor管理进程，配置如下：

```javascript
cd /etc/supervisor  #没有则创建
vim demo.conf # 这里的文件名称自定义
```

加入以下内容：

your_program_name就是自定义的应用名称

command：启动应用的命令比如java -jar 别使用守护线程

directory：脚本或者jar所处位置

```javascript
; 设置进程的名称，使用 supervisorctl 来管理进程时需要使用该进程名
[program:your_program_name] 
command=python server.py --port=9000
;numprocs=1                 ; 默认为1
;process_name=%(program_name)s   ; 默认为 %(program_name)s，即 [program:x] 中的 x
directory=/home/python/tornado_server ; 执行 command 之前，先切换到工作目录
user=root                 ; 使用 root 用户来启动该进程
; 程序崩溃时自动重启，重启次数是有限制的，默认为3次
autorestart=true            
redirect_stderr=true        ; 重定向输出的日志
stdout_logfile = /var/log/supervisord/tornado_server.log
loglevel=info
```

更改了supervisor配置文件，需要重启,运行以下指令：

```javascript
supervisorctl reload
```

supervisorctl的用法

```javascript
supervisord : 启动supervisor
supervisorctl reload :修改完配置文件后重新启动supervisor
supervisorctl status :查看supervisor监管的进程状态
supervisorctl start 进程名 ：启动XXX进程
supervisorctl stop 进程名 ：停止XXX进程
supervisorctl stop all：停止全部进程，注：start、restart、stop都不会载入最新的配置文件。
supervisorctl update：根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启
```