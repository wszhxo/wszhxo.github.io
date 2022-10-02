## 在服务器搭建git仓库

**何谓公钥：**

　　1.很多服务器都是需要认证的，ssh认证是其中的一种。在客户端生成公钥，把生成的公钥添加到服务器，你以后连接服务器就不用每次都输入用户名和密码了。

　　2.很多git服务器都是用ssh认证方式，你需要把你生成的公钥发送给代码仓库管理员，让他给你添加到服务器上，你就可以通过ssh自由地拉取和提交代码了。



**生成公钥：**

　　1.如果通过上面的方式找不到公钥，你就需要先生成公钥了：ssh-keygen

　　2.接着会确认存放公钥的地址，默认就是上面说的路径，直接enter键确认

　　3.接着会要求输入密码和确认密码，如果不想设置密码直接不输入内容 按enter键



## 登录到服务器

### 1.安装git

```java
git --version // 看看服务器版本  ，没有就用下边的安装
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
yum install -y git
```

### 2.添加新用户（Git仓库所有者）

```yml
useradd gituser  # gituser是用户名字
```

### 3.设置密码

```yml
passwd gituser  # 给gituser设置密码，回车才可以设置
```

### 4.添加新用户（Git仓库所有者）

```yml
useradd gituser  # gituser是用户名字
```

### 5.配置sshd服务参数

```yml
vi /etc/ssh/sshd_config  #我一般直接使用工具打开这个文件把下边三个添加到Host下边
RSAAuthentication yes   # 启用RSA 非对称加密算法
PubkeyAuthentication yes   # 公钥认证
PasswordAuthentication yes  #允许密码认证
```

### 6.修改配置后需要重启服务

```yml
systemctl restart sshd.service
```

### 7.添加新用户（Git仓库所有者）

```yml
useradd gituser  # gituser是用户名字
```



### 8.切换到用户gituser（刚新建的用户）

```yml
su - gituser        # 因为你是root 无需密码
```

### 9.切换cd到/home/gituser目录下

### 10.创建一个git仓库

```yml
git init --bare myproject.git  #  myproject.git --> 仓库名字
```

## 11.创建一个 .ssh 文件夹

```yml
mkdir .ssh
```

## 12.设置.ssh目录权限

```
chmod 700 .ssh
```

## 13.进入文件夹

```
cd .ssh
```

## 14.创建文件 authorized_keys

```
touch authorized_keys
```

## 15.设置authorized_keys权限

```yml
chmod 600 authorized_keys
```

### 16.编辑authorized_keys

```yml
vi authorized_keys   # 里面写入本机的公钥
i       # 写入
Esc :x  # 保存并退出
```

这里本机不是服务器，在本地的git创建公钥,右键git bash here

```bat
 cd ~/.ssh  #进入此目录
 ssh-keygen #生成公私秘钥，后边会有一些操作回车回车回车即可
 cat id_rsa.pub  #查看公钥 id_rsa就是私钥
```

这样本机就有权克隆git项目 ，如果另一台需要克隆，在这个文件authorized_keys换行加入另一台电脑的公钥即可

## 17.在本机的 .ssh 创建文件config里面写入

```yml
Host myserver_git    # 本机要连接服务器的名字
HostName 127.0.0.1   # 举例 服务器的IP
User gituser             # 服务器上的用户
Port 22  
PreferredAuthentications publickey
IdentityFile C:\Users\zhx\.ssh\id_rsa     # 指定本机的私钥地址
```

**记得复制的时候要把注释#的内容删掉**

### 18.最后就可以在本地克隆仓库了

```yml
git clone zhx@zhxblog:/home/zhx/hexozhxblog.git  #两种都可以了
git clone zhx@zhxblog:~/hexozhxblog.git  #也许会让你输入yes或者no，输入yes即可
```



### 可能出现的问题

The authenticity of host 'xxxxx' can't be established.

修改 /etc/ssh/ssh_config 

 ```yml
StrictHostKeyChecking no
UserKnownHostsFile /dev/null
 ```



> linux chmod 777 表示 User Group  Other     三者都可 读 写 运行  
>
> 文件的所有者（User）
> 文件的所有者一般是创建该文件的用户，对该文件具有完全的权限。在一台允许多个用户访问的 Linux 主机上，可以通过文件的所有者来区分一个文件属于某个用户。当然，一个用户也无权查看或更改其它用户的文件。
>
> 文件所属的组（Group）
> 假如有几个用户合作开发同一个项目，如果每个用户只能查看和修改自己创建的文件就太不方便了，也就谈不上什么合作了。所以需要一个机制允许一个用户查看和修改其它用户的文件，此时就用到组的概念的。我们可以创建一个组，然后把需要合作的用户都添加都这个组中。在设置文件的访问权限时，允许这个组中的用户对该文件进行读取和修改。
>
> 其他人（Other）
> 如果我想把一个文件共享给系统中的所有用户该怎么办？通过组的方式显然是不合适的，因为需要把系统中的所有用户都添加到一个组中。并且系统中添加了新用户该怎么办，每添加一个新用户就把他添加到这个组中吗？这个问题可以通过其他人的概念解决。在设置文件的访问权限时，允许其他人户对该文件进行读取和修改。
>
> 如：
>
> rwx = 4 + 2 + 1 = 7
>
> rw = 4 + 2 = 6
>
> rx = 4 +1 = 5
>
> 即
>
> 若要同时设置 rwx (可读写运行） 权限则将该权限位 设置 为 4 + 2 + 1 = 7
>
> 若要同时设置 rw- （可读写不可运行）权限则将该权限位 设置 为 4 + 2 = 6
>
> 若要同时设置 r-x （可读可运行不可写）权限则将该权限位 设置 为 4 +1 = 5



