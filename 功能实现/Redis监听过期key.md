Redis监听过期key

把配置写入配置文件notify-keyspace-events Ex

如果不想重启可以进入redis命令行实时修改配置，重启会失效

```
config set notify-keyspace-events Ex
```



核心监听器

```java
package com.beitai.framework.config;

/**
 * @author zhx
 * @title
 * @description 监听某个key的变化
 * @date 2021/11/22 16:03
 */

import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.data.redis.listener.Topic;
import org.springframework.util.StringUtils;

import java.nio.charset.Charset;
import java.util.Properties;

public class RedisKeyChangeListener implements MessageListener{
    private final String listenerKeyName; // 监听的key的名称
    private static final Topic TOPIC_KEYNAMESPACE_EXPIRE = new PatternTopic("__keyevent@0__:expired");
    // 监控
    private static final Topic TOPIC_KEYEVENTS_NAME_SET_USELESS = new PatternTopic("__keyevent@0__:set myKey");
//    private String keyspaceNotificationsConfigParameter = "KEA";
    private String keyspaceNotificationsConfigParameter = "Ex";
    public RedisKeyChangeListener(RedisMessageListenerContainer listenerContainer, String listenerKeyName) {
        this.listenerKeyName = listenerKeyName;
        initAndSetRedisConfig(listenerContainer);
    }

    public void initAndSetRedisConfig(RedisMessageListenerContainer listenerContainer) {

        if (StringUtils.hasText(keyspaceNotificationsConfigParameter)) {

            RedisConnection connection = listenerContainer.getConnectionFactory().getConnection();

            try {

                Properties config = connection.getConfig("notify-keyspace-events");

                if (!StringUtils.hasText(config.getProperty("notify-keyspace-events"))) {
                    connection.setConfig("notify-keyspace-events", keyspaceNotificationsConfigParameter);
                }

            } finally {
                connection.close();
            }
        }
        // 注册消息监听
        listenerContainer.addMessageListener(this, TOPIC_KEYNAMESPACE_EXPIRE);
    }



    @Override
    public void onMessage(Message message, byte[] pattern) {
        System.err.println("key发生变化===》" + message);
        byte[] body = message.getBody();
        String string = new String(body, Charset.forName("utf-8"));
        System.out.println(string);

    }

}
```

redis配置类

```java

package com.beitai.framework.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.JsonTypeInfo;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * redis配置
 *
 * @author 
 */
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport
{
    @Bean
    @SuppressWarnings(value = { "unchecked", "rawtypes" })
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)
    {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);

        FastJson2JsonRedisSerializer serializer = new FastJson2JsonRedisSerializer(Object.class);

        ObjectMapper mapper = new ObjectMapper();
        mapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        mapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
        serializer.setObjectMapper(mapper);

        template.setValueSerializer(serializer);
        // 使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());
        template.afterPropertiesSet();
        return template;
    }
    @Autowired
    RedisConnectionFactory connectionFactory;
    // 创建基本的key监听器
    @Bean
    public RedisKeyChangeListener redisKeyChangeListener() throws Exception {
        RedisKeyChangeListener listener = new RedisKeyChangeListener(container(connectionFactory),"");
        return listener;
    }
    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory) {
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        return container;
    }
}

```

