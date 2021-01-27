---
title: Spring Boot 监控 Actuator
tags: SpringBoot
---

## Spring Boot 监控 Actuator

Spring Boot 遵循着“约定大于配置”的原则，许多功能不用配置即可使用，这是因为 Spring Boot 把约定的配置已经配置好了。好处在于我们不需要像使用 Spring 那样编写一大堆的配置文件，但过于简单的配置过程会让我们在了解各种依赖，配置之间的关系过程上带来一些困难。在 Spring Boot中，提供了 Actuator 监控，Actuator 提供了 RESTful API 让我们可以更为方便的了解配置信息。

 Spring Boot 2.0.1.RELEASE Actuator  [中文文档](https://www.cnblogs.com/powerwu/articles/9553333.html)



### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```



### Applicaiton.yml 配置 Actuator

web 请求默认只暴露两个端点，查看全部端点可访问 [官网](https://docs.spring.io/spring-boot/docs/1.5.14.RELEASE/reference/html/production-ready-endpoints.html)

![](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128004346954.png)

```yml
management:
  server:
    port: 80  #端口
    base-path: /manager  #请求路径
  endpoints:
    web:
      exposure:
          include: "*"  #暴露全部端点,也可以有选择性的暴露端点  
```



### 测试

访问 http://localhost/manager/actuator 查看暴露的端点，截取一部分

![image-20210128004542614](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128004542614.png)





访问 http://localhost/manager/actuator/mappings 查看 Mapping，截取一部分

![image-20210128004734994](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128004734994.png)