---
title: 关于Cookie和Session
abbrlink: 1184504042
categories: 
- java web
---

## 利用Cookie实现记住密码  自动登录

<!--more-->

用户首次与Web服务器建立连接的时候，服务器会给用户分发一个 SessionID作为标识。SessionID是一个由24个字符组成的随机字符串。用户每次提交页面，浏览器都会把这个SessionID包含在 HTTP头中提交给Web服务器，这样Web服务器就能区分当前请求页面的是哪一个客户端(一般指浏览器)。这个SessionID就是保存在客户端的，属于客户端Session。其实客户端Session默认是以cookie的形式来存储的。

Cookie是保存在浏览器,而浏览器会把他下载到本地,保存为txt文件

Session是保存在服务器,关闭浏览器,到期或者服务器关闭就消失了,

# 1.自动登录

## 1.1创建表单

```java
<form  method="post" id="loginForm" action="/LoginServlet"  >
<input type="text" id="username" name ="username" >
<input type="text" id="password" name ="password" >
<label> <input type="checkbox" name="autoLogin" id="autoLogin"> 自动登录</label>
<label> <input type="checkbox" name="remberpsw" id="remberpsw"> 记住密码</label>
<input type="submit" width="100" value="登录" name="submit">
</form>



```

## 1.2生成Cookie

​	判断前台是否点击了自动登录,是则创建Cookie,后跳转到主页

```java
String autoLogin = request.getParameter("autoLogin");
			if("on".equals(autoLogin)){
				//要自动登录
				//创建存储用户名的cookie
				Cookie cookie_username = new Cookie("cookie_username",user.getUsername());
				cookie_username.setMaxAge(10*60);
				//创建存储密码的cookie
				Cookie cookie_password = new Cookie("cookie_password",user.getPassword());
				cookie_password.setMaxAge(10*60);

				response.addCookie(cookie_username);
				response.addCookie(cookie_password);
			}
			//将user对象存到session中
			session.setAttribute("user", user);
			//重定向到首页
response.sendRedirect(request.getContextPath()+"/index.jsp");
```

## 1.3获取Cookie

下次再访问登录界面时,**在Cookie没有失效的前提下获取Cookie中的用户名与密码进行自动登录**,一般写在LoginFilter登录过滤器中

Cookie里边包含了很多键值对,cookie_username和cookie_password只是里边的一部分

所以要遍历整个cookie数组

遍历到键为cookie_username就把他赋值给字符串cookie_username

遍历到键为cookie_password就把值赋值给字符串cookie_password

而后可以用该字符串进行后续操作

比如与数据库表比对,实现自动登录

```java
	String cookie_username = null;
	String cookie_password = null;
			
	//获取携带用户名和密码cookie
	Cookie[] cookies = req.getCookies();
	if(cookies!=null){
    for(Cookie cookie:cookies){
        //获得想要的cookie
        if("cookie_username".equals(cookie.getName())){
						cookie_username = cookie.getValue();
					}
        if("cookie_password".equals(cookie.getName())){
						cookie_password = cookie.getValue();
					}
				}
			}
```

# 2.记住密码

把上述字符串,放入域中,jsp登录界面使用${}获取即可!



