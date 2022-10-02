#  RestTemplate接口调用

需要在application启动类中加入,不然无法注入

```
@Bean
public RestTemplate restTemplate(){
    return new RestTemplate();
}
```

LoginVo实体类

```
@Data
@ApiModel(value="登录对象", description="登录对象")
public class LoginVo {
    @ApiModelProperty(value = "手机号")
    private String mobile;
    @ApiModelProperty(value = "密码")
    private String password;
}
```



## 方法1 

原方法,注意@RequestBody

```java
@PostMapping("/xx")
public String xx(@RequestBody LoginVo loginVo) {
    return loginVo.toString();
}
```

调用方法

```java
 @Resource
    RestTemplate restTemplate;

    @PostMapping("/consumer/restTemplate")
    public String login( LoginVo loginVo) {
        return restTemplate.postForObject("http://localhost:8006/xx",loginVo,String.class);

    }
```

调用接口http://localhost:8006/consumer/restTemplate/xx 传递json数据{"mobile":15150576333,"password":123}

## 方法2

此时没有@RequestBody注解

```java
@PostMapping("/xx")
public String xx( LoginVo loginVo) {
    return loginVo.toString();
}
```

```java
 @PostMapping("/consumer/restTemplate")
    public String login( LoginVo loginVo) {

        return restTemplate.postForObject("http://localhost:8006/xx?mobile={mobile}&password={password}",loginVo,String.class);
    }
```

http://localhost:8006/ucenterservice/apimember/xx?mobile=123123&password=2331