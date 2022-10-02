# RedisTemplate简单使用

## properties文件配置

```properties
# REDIS
# Redis数据库索引（默认为0）
spring.redis.database=0  
# Redis服务器地址
spring.redis.host=ip地址
# Redis服务器连接端口
spring.redis.port=6379  
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制） 默认 8
spring.redis.lettuce.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接 默认 8
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接 默认 0
spring.redis.lettuce.pool.min-idle=0
```

## Controller层

```java
package com.neo.web;

import com.neo.model.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ValueOperations;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpSession;
import java.util.concurrent.TimeUnit;

@RestController
public class UserController {
    @Autowired
    private RedisTemplate redisTemplate;
    @RequestMapping("/getUser")
    public String getUser() {
        return  redisTemplate.opsForValue().get("user1").toString();
    }
    @RequestMapping("/d")
    String uid(HttpSession session) {
        redisTemplate.delete("user1");
        return "删除";
    }
    @RequestMapping("/r")
    String testr(HttpSession session) {
        User user = new User("aa@126.com", "aa", "aa123456", "aa", "123");
        ValueOperations<String, User> operations = redisTemplate.opsForValue();
//        operations.set("user1", user);
        operations.set("user1", user, 10, TimeUnit.SECONDS);//设置10秒过期时间
//        boolean exists = redisTemplate.hasKey("com.neo.f");//判断key存在true或false
        return "==";
    }
}
```

