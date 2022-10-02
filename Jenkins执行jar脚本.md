# Jenkins执行jar脚本

Jenkin会自动打包，因此只需要编写启动jar的脚本

```sh
mv /var/lib/jenkins/workspace/beitai1850/beitai-admin/target/beitai-admin.jar /usr/local/beitai-1850/
ps -ax | grep /usr/local/beitai-1850/beitai-admin.jar | grep -v grep | awk '{print $1}' | xargs kill
BUILD_ID=DONTKILLME
nohup java -Xms512m -Xmx512m -jar -Dspring.profiles.active=prod /usr/local/beitai-1850/beitai-admin.jar &
```

1. 把Jenkins打的beitai-admin.jar包移动到对应目录
2. ps -ax | grep /usr/local/beitai-1850/beitai-admin.jar表示查看beitai-admin.jar进程相关信息  grep -v grep表示过滤掉包含grep的，awk '{print $1}' 表示只得到返回信息的第一个列 也就是进程号， xargs kill表示杀掉改进程号
3. BUILD_ID=DONTKILLME表示防止Jenkins杀死执行java -jar的子进程，因此要放在执行jar的命令前边
4. 执行jar命令

