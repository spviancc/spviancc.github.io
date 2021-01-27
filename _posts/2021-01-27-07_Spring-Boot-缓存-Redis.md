---
title: Spring Boot 缓存 Redis
tags: SpringBoot
---

## Spring Boot 缓存 Redis

[下载 Redis x64](https://spvian.lanzous.com/ieFf7kxop5c) 

以下部分是把 [Spring Boot 缓存 EhCahe](https://spviancc.github.io/_posts/2021-01-27-06_Spring-Boot-%E7%BC%93%E5%AD%98-EhCache/) 中的 EhCahe 替换为 Redis

### 引入依赖

```xml
        <!-- spring-boot redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```



### 开启 Redis 服务

开启服务时，会有一个黑窗口一闪而过。默认端口是 6379

![image-20210127203158530](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127203158530.png)



你也可以在服务管理那里开启

![image-20210127203521187](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127203521187.png)



### application.yml 中配置 Redis

```yml
//这是默认配置，可以不配置
spring:
  redis:
    # Redis数据库索引（默认为0）
    database: 0
    # Redis服务器地址
    host: localhost
    # Redis服务器连接端口
    port: 6379
    pool:
      # 连接池最大连接数（使用负值表示没有限制）
      max-active: 8
      # 连接池最大阻塞等待时间（使用负值表示没有限制）
      max-wait: -1
      # 连接池中的最大空闲连接
      max-idle: 8
      # 连接池中的最小空闲连接
      min-idle: 0
    # 连接超时时间（毫秒）
    timeout: 0
```



有关更多 Redis 的配置可以去 [官网](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties-cache) 查看

![image-20210127203949562](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127203949562.png)



### 实体类序列化

![image-20210127204200123](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127204200123.png)



### 测试

我之前已经测试过一次，可以看到没有去数据库中查询

![image-20210127204753616](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127204753616.png)



我们来看 Redis 中有没有缓存，Redis 中有一个 Key ，我们获取这个 Key 可以得到我们缓存的数据

![image-20210127205037738](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127205037738.png)