# Springboot整合Kafka案例

## 导入依赖

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

## yml配置

```
kafka:
  consumer:
    enable-auto-commit: true
    group-id: test
    auto-commit-interval: 1000
    auto-offset-reset: latest
    bootstrap-servers: xxxxx:xxx,xxxxx:xxx,xxxxx:xxx,xxxxx:xxx
    kafkaSecurityStatus: 0
    key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    value-deserializer: org.apache.kafka.common.serialization.ByteArrayDeserializer
    concurrency: 3
    max-poll-records: 50
    poll-timeout: 1500
    batch-listener: false
  producer:
    servers: xxxxx:xxx,xxxxx:xxx,xxxxx:xxx
    key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    value-deserializer: org.apache.kafka.common.serialization.ByteArrayDeserializer
    switch-enable: true
    kafkaSecurityStatus: 0
    retries: 3
    batch:
      size: 16384
    linger: 0
    buffer:
      memory: 33554432
```



## 生产者配置类

```java
package com.nies.kafka.config;

import org.apache.kafka.clients.CommonClientConfigs;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.config.SaslConfigs;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import java.util.HashMap;
import java.util.Map;

/**
 *概述：kafka生产者配置。
 */
@Configuration
@EnableKafka
public class KafkaProducerConfig {
    /**kafka 集群，broker-list*/
    @Value("${kafka.producer.servers}")
    private String servers;
    /**重试次数*/
    @Value("${kafka.producer.retries}")
    private int retries;
    /**批次大小*/
    @Value("${kafka.producer.batch.size}")
    private int batchSize;
    /**等待时间*/
    @Value("${kafka.producer.linger}")
    private int linger;
    /**RecordAccumulator 缓冲区大小*/
    @Value("${kafka.producer.buffer.memory}")
    private int bufferMemory;
    /**否启用权限认证*/
    @Value("${kafka.producer.kafkaSecurityStatus}")
    private int kafkaSecurityStatus;

    private Map<String, Object> producerConfigs() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        props.put(ProducerConfig.RETRIES_CONFIG, retries);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, batchSize);
        props.put(ProducerConfig.LINGER_MS_CONFIG, linger);
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, bufferMemory);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
         /*灵活配置开关是否启用权限认证*/
        if (kafkaSecurityStatus == 1) {
            props.put(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");
            props.put(SaslConfigs.SASL_MECHANISM, "SCRAM-SHA-256");
        }
        return props;
    }

    private ProducerFactory<String, String> producerFactory() {
        return new DefaultKafkaProducerFactory<>(producerConfigs());
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<String, String>(producerFactory());
    }

}
```

## 消费者配置类

```java
package com.nies.kafka.config;

import org.apache.kafka.clients.CommonClientConfigs;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.config.SaslConfigs;
import org.apache.kafka.common.serialization.ByteArrayDeserializer;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.kafka.KafkaProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.config.KafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;
import java.util.HashMap;
import java.util.Map;

/**
 *概述：kafka消费者配置。
 */
@Configuration
@EnableKafka
public class KafkaConsumerConfig {
     /**kafka 集群，broker-list*/
    @Value("${kafka.consumer.bootstrap-servers}")
    private String servers;
     /**开启自动提交*/
    @Value("${kafka.consumer.enable-auto-commit}")
    private boolean enableAutoCommit;
     /**自动提交延迟*/
    @Value("${kafka.consumer.auto-commit-interval}")
    private String autoCommitInterval;
     /**消费者组*/
    @Value("${kafka.consumer.group-id}")
    private String groupId;
    /**重置消费者的offset*/
    @Value("${kafka.consumer.auto-offset-reset}")
    private String autoOffsetReset;
    /**最多并发数*/
    @Value("${kafka.consumer.concurrency}")
    private int concurrency;
    /**是否批量拉取*/
    @Value("${kafka.consumer.batch-listener}")
    private boolean batchListener;
    /**批量拉取个数*/
    @Value("${kafka.consumer.max-poll-records}")
    private int maxPollRecords;
    /**拉取超时时间*/
    @Value("${kafka.consumer.poll-timeout}")
    private long pollTimeout;
    /**否启用权限认证*/
    @Value("${kafka.consumer.kafkaSecurityStatus}")
    private int kafkaSecurityStatus;

    @Bean
    @Primary
    public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, byte[]>> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, byte[]> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        /*并发数量*/
        factory.setConcurrency(concurrency);
        /*批量获取开关*/
        factory.setBatchListener(batchListener);
        /*设置拉取时间超时间隔*/
        factory.getContainerProperties().setPollTimeout(pollTimeout);
        return factory;
    }

    private ConsumerFactory<String, byte[]> consumerFactory() {
        return new DefaultKafkaConsumerFactory<>(consumerConfigs());
    }

    private Map<String, Object> consumerConfigs() {
        Map<String, Object> propsMap = new HashMap<>();
        propsMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        propsMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, enableAutoCommit);
        propsMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, autoCommitInterval);
        propsMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class);
        propsMap.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        propsMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, autoOffsetReset);
       /* 批量拉取数量*/
        propsMap.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, maxPollRecords);

//        propsMap.put(ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGY_CONFIG, StickyAssignor.class.getName());
         /*灵活配置是否启用权限认证开关*/
        if (kafkaSecurityStatus == 1) {
            propsMap.put(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");
            propsMap.put(SaslConfigs.SASL_MECHANISM, "SCRAM-SHA-256");
        }
        return propsMap;
    }

    @Bean
    public KafkaProperties.Listener listener() {
        return new KafkaProperties.Listener();
    }
}
```

## 发送消息工具类

```
package com.nies.kafka.utils;

import lombok.extern.slf4j.Slf4j;
import org.apache.kafka.common.PartitionInfo;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.KafkaTemplate;
import javax.annotation.Resource;
import java.util.List;

/**
 *功能描述：kafka生产者，发送消息到kafka。
 */
@Configuration
@Slf4j
public class KafkaProducerUtil {

    @Resource(name ="kafkaTemplate")
    private KafkaTemplate<String, String> kafkaTemplate;

    public void sendToKafkaMessage(String topic ,String messageData) {
        if(kafkaTemplate==null){
            log.info("发送消息失败:未获取到KafkaTemplate信息");
            return ;
        }
        kafkaTemplate.send(topic, messageData).addCallback(success -> {
//            // 消息发送到的topic
//            String topicBack = success.getRecordMetadata().topic();
//            // 消息发送到的分区
//            int partition = success.getRecordMetadata().partition();
//            // 消息在分区内的offset
//            long offset = success.getRecordMetadata().offset();
//            log.info("发送消息成功:" + topicBack + "-" + partition + "-" + offset);
        }, failure -> {
            log.error("发送消息失败,topic{},消息内容{},异常原因{}" ,topic,messageData, failure.getMessage());
        });

    }
    public void sendToKafkaMessage(String topic ,int partition,String key,String messageData) {
        if(kafkaTemplate==null){
            log.info("发送消息失败:未获取到KafkaTemplate信息");
            return ;
        }
        kafkaTemplate.send(topic,partition,key, messageData).addCallback(success -> {
//            // 消息发送到的topic
//            String topicBack = success.getRecordMetadata().topic();
//            // 消息发送到的分区
//            int partition2 = success.getRecordMetadata().partition();
//            // 消息在分区内的offset
//            long offset = success.getRecordMetadata().offset();
//            log.info("发送消息成功:" + topicBack + "-" + partition2 + "-" + offset);
        }, failure -> {
            log.error("发送消息失败,topic{},消息内容{},异常原因{}" ,topic,messageData, failure.getMessage());
        });

    }
    public void sendToKafkaMessage(String topic ,String key,String messageData) {
        if(kafkaTemplate==null){
            log.info("发送消息失败:未获取到KafkaTemplate信息");
            return ;
        }
        kafkaTemplate.send(topic,key, messageData).addCallback(success -> {
//            // 消息发送到的topic
//            String topicBack = success.getRecordMetadata().topic();
//            // 消息发送到的分区
//            int partition = success.getRecordMetadata().partition();
//            // 消息在分区内的offset
//            long offset = success.getRecordMetadata().offset();
//            log.info("通道编码："+key+"发送消息成功:" + topicBack + "-" + partition + "-" + offset);
////            byte[] bytes = key.getBytes();
////            int toPositive = Utils.toPositive(Utils.murmur2(bytes)) % 3;//%分区数
////            log.info("打印结果:toPositive----key：" + key + "hash值："+toPositive+"partition:" + partition);
        }, failure -> {
            log.error("发送消息失败,topic{},消息内容{},异常原因{}" ,topic,messageData, failure.getMessage());
        });

    }

    /**
     * 获取topic的分区个数
     * @param topic kafka的topic
     */
    public int partitionsFor(String topic) {
        if(kafkaTemplate==null){
            log.info("发送消息失败:未获取到KafkaTemplate信息");
            return 0;
        }
        List<PartitionInfo> partitionInfos = kafkaTemplate.partitionsFor(topic);
        return partitionInfos.size();
    }

}
```

## 消息监听器

```java
@Component
@Slf4j
public class KafkaControlListener {
    
      @KafkaListener(topics = xxxx)
    public void listenTopic(String msg) {
        log.info("监听到实时数据信息，{}",msg);
    }
}
```

