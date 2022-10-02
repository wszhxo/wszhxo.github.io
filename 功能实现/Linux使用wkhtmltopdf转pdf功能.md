# Linux使用wkhtmltopdf转pdf功能

## Windows版本

https://github.com/wkhtmltopdf/wkhtmltopdf/releases/

添加环境变量 把 bin目录放到Path中

``` bsh
wkhtmltopdf -V     查看版本，如果有说明安装成功
```

相关命令地址

https://www.cnblogs.com/colder/p/5819197.html

举例 ： 

```bsh
wkhtmltopdf https://www.baidu.com/ D:\baidu.pdf   //网络页面
wkhtmltopdf  test.html  test.pdf  //本地HTML页面
# 指定源html编码为utf8,并给转换后的pdf文件加上页眉页脚信息
wkhtmltopdf --encoding utf8 --header-center 访问www.361way.com获取更多信息  --footer-center email至itybku@139.com交流  test.html out.pdf
```

## Linux

### 下载linux版本wkhtmltopdf,后解压

```
　　wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz;
```

```
tar -xvf wkhtmltox-0.12.4_linux-generic-amd64.tar
```

### 配置环境变量修改  /etc/profile

```
#修改wkhtmltopdf运行环境
export WKHTMLTOPDF_HOME="/home/test/wkhtmltox"
export PATH="$PATH:$WKHTMLTOPDF_HOME/bin"
```

设置可执行权限   chmod g+x /xxx/wkhtmltox/bin/wkhtmltopdf

添加文件夹的写入权限 chmod -R 777 /dir1/xxx/

### 执行 source /etc/profile 更新

### 测试

wkhtmltopdf http://www.baidu.com /home/wwwroot/baidu.pdf

中文显示问题，把windows的字体C:\Windows\Fonts\simsun.ttc放入/usr/share/fonts/下的文件夹中

没有字体文件夹

执行  yum -y install fontconfig

### wkhtmltopdf报错提示error while loading shared libraries: libXrender.so.1

```
wkhtmltopdf: error while loading shared libraries: libXrender.so.1: cannot open shared object file: No such file or directory
```

执行  yum install libXrender libXext fontconfig   即可



## 导出的注意事项

- vue的foreach循环使用原生 【必须】

- body标签要加入字体 【必须】
- 分页加入样式page-break-after: always; 【必须】

- echart图表可以尝试加入animation: false
- ${function(){}}换成window.onload

