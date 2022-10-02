#  APIFOX的配置

## 登录后把token 放入环境变量

1. 添加全局变量  token

2. 在后置操作中加入脚本

```bash
var respObj = JSON.parse(responseBody);
pm.environment.set("token",respObj.token);
pm.globals.set("token",respObj.token);
```

# Postman的配置

## 关于验证码显示问题

在test中加入脚本能在Visuailze中看到预览

```bash
var jsData = JSON.parse(responseBody)
pm.globals.set("uuid",jsData.uuid);

//设置验证码图片
var template = `
  <img src="data:image/gif;base64,{{response.img}}" >
`;

pm.visualizer.set(template, {
    response: pm.response.json()
});
```

登录后的token设置

```bash
var respObj = JSON.parse(responseBody);
pm.globals.set("token",respObj.token);
```



