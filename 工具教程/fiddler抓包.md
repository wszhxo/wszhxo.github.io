# chrome调试
<https://dingjia.ceshi.che300.com/h5pages/H5pages/LoanCalculator?hiddenTitleBar=true>
### Elements面板介绍
![](https://assets.che300.com/feimg/easymock/2020-12-08/45e9446df34f440c753825a24b758d9b.png)
#### 改变元素伪类
![](https://assets.che300.com/feimg/easymock/2020-12-08/42f3fde93d42fd8c80f709e3175ee6f7.gif)
#### 查看元素绑定的事件
![](https://assets.che300.com/feimg/easymock/2020-12-08/858393434a9bc45d0d8693124f88268b.gif)
#### 对元素打断点，监听属性和结构变化
![](https://assets.che300.com/feimg/easymock/2020-12-08/fe32a9f960d22c55a089dced9e4311dd.gif)
#### 查看元素的属性和方法
![](https://assets.che300.com/feimg/easymock/2020-12-08/193c9c51c91cdba9fa4cff44d0318b52.gif)
#### 在Elements面板拖放元素
![](https://assets.che300.com/feimg/easymock/2020-12-09/73b2f1dac1643bcdafa3351065c2f6e7.gif)
#### 保存修改后的 CSS 文件
![](https://assets.che300.com/feimg/easymock/2020-12-09/6c4286cd6765e2106560e69e00042dde.gif)
#### 单个元素截图  
选择一个元素，然后按cmd-shift-p（或Windows系统中的ctrl-shift-p）打开“命令”菜单，然后选择Capture node screenshot进行屏幕截图
![](https://assets.che300.com/feimg/easymock/2020-12-09/aed211b9f32917046f5e5aaf00cf72b7.gif)
# Fiddler抓包工具的使用
#### Fiddler的工作原理
Fiddler在浏览器与服务器之间建立一个代理服务器。Fiddler 是以代理web服务器的形式工作的,它使用代理地址:127.0.0.1, 端口:8888. 当Fiddler退出的时候它会自动注销，这样就不会影响别的程序
![](https://assets.che300.com/feimg/easymock/2020-12-09/2f4d46e629735119619ef4952f59e461.png)
#### Fiddler的安装及配置
fiddler没有手机客户端，都是安装在PC上，要实现对手机上的程序抓包，则需要对PC上的fiddler和手机端做一些配置。步骤如下：

##### 前提条件 :
1. 测试手机需要支持Wifi
2. 测试手机与电脑需要同一网络
##### 下载 :
* [官网](https://www.telerik.com/fiddler)
* [svn](http://svn.guchele.cn/!/#sherry/view/head/trunk/app/test/测试工具/入职安装大礼包)
##### pc端配置:
打开Fiddler  Tool->Fiddler Options->HTTPS 。  

勾选上Capture HTTPS CONNECTs（捕获 HTTPS 连接）、 Decrypt HTTPS traffic （HTTPS 请求解密）和 ignore server certificate errors（忽略服务器证书错误）
![](https://assets.che300.com/feimg/easymock/2020-12-09/17f94f0442f004f4ffb97f61b4d7ddc5.png)
安装证书（首次使用无证书，会弹出是否信任fiddler证书和安全提示，直接点击yes就行），重启Fiddler生效。
![](https://assets.che300.com/feimg/easymock/2020-12-09/85d69e8e0c73b076205b168513a18fae.png)
###### 允许手机远程连接
点击 Fiddler->Tools -> Options，在 Connections 面板选中 Allow remote computers to connect 允许其他设备连接（此操作需重启Fiddler生效）。
“fiddler listens on port:”默认为8888，如你本机电脑的端口8888已被占用，那填写一个未被占用的端口
![](https://assets.che300.com/feimg/easymock/2020-12-09/09228907c0397c745ac8ef41435315fe.png)
###### 查看电脑ip地址
* 通过cmd命令行输入ipconfig查询
![](https://assets.che300.com/feimg/easymock/2020-12-09/6bd96959377f31b1b4105308f5ea2744.png)
* 或者将鼠标置于fiddler右上角的online中即可显示电脑的ip地址。
![](https://assets.che300.com/feimg/easymock/2020-12-09/9d33b9c7923ae8d7eec041d340608773.png)
##### 手机端配置:
将 Fiddler 代理服务器的证书导到手机上才能抓这些 APP 的包。

导入的过程:打开浏览器，在地址栏中输入代理服务器的 IP 和端口（即电脑的IP加fiddler的端口），会看到一个Fiddler 提供的页面，然后确定安装就好了
![](https://assets.che300.com/feimg/easymock/2020-12-09/4866c177c31d631f7ef204b0619d0cbf.png)
![](https://assets.che300.com/feimg/easymock/2020-12-09/ed79b46b4bb5fcbcfd00202575a355d6.png)
打开手机的  设置    —>   WlAN ，选择连接的wifi（必须保证PC端与移动端在同一个网段中）。
长按该wifi，然后选择“修改网络”，然后勾选“显示高级选项”,选择手动代理设置，主机名填写Fiddler所在电脑的ip，端口填写Fiddler端口，默认8888（我自己配置是10001），保存即可。
![](https://assets.che300.com/feimg/easymock/2020-12-09/9d78e24997f25471dc518c1872979ec4.png)
![](https://assets.che300.com/feimg/easymock/2020-12-09/48c48f3d71315d5e7fdb99c905ed06f0.png)
##### Fiddler的使用
###### 过滤域名
![](https://assets.che300.com/feimg/easymock/2020-12-09/b82e10ecad7b57e59e25a560edfd65e4.png)
###### 修改接口返回值
还用刚才的例子，我们先把fiddler设置成这样：选中Rules->Automatic Breakpoints->After Responses，或者点击fiddler左下角，直到出现向下的红色箭头（向下箭头表示返回过程中被fiddler拦截），如下：
![](https://assets.che300.com/feimg/easymock/2020-12-09/f75d4783182b6267da87309e66b93f6e.png)
![](https://assets.che300.com/feimg/easymock/2020-12-09/62c4c4a0bbc0165cde738cce955c1929.png)
![](https://assets.che300.com/feimg/easymock/2020-12-09/d810f54018d77f7ba2fe7ca5a92da2cf.png)
###### 模拟低网速
有时候，我们想看一下在网速慢的情况下，我们的web页面到底表现如何，页面的一个渲染情况如何，但无奈公司网络太快，怎么办？  
首先点击Rules->Customize Rules，搜索找到“m_SimulateModem”这个地方，代码如下：
```
if (m_SimulateModem) {
    // Delay sends by 300ms per KB uploaded.
    oSession["request-trickle-delay"] = "300"; 
    // Delay receives by 150ms per KB downloaded.
    oSession["response-trickle-delay"] = "150"; 
}
```
第一个数值设置的是请求上传过程中的延迟；第二个数值是返回下载过程中的延迟，比如，我把第一个数值的300(单位是毫秒)换成2000，然后保存，保存后，点击Rules->Performance->Simulate Modem Speeds进行勾选，然后就可以随便打开一个网页测测到底有没有变慢了。
