---
title: Spring Boot 整合 Mybatis PageHelper 
tags: SpringBoot
---

## Spring Boot 整合 PageHelper

PageHelper 是 Mybatis 的分页插件，支持多数据库、多数据源。可以简化数据库的分页查询操作。

使用时，只需引入依赖即可。



### 引入依赖

```xml
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.5</version>
        </dependency>
```



### 测试

修改 IndexController

![image-20210126043834668](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126043834668.png)

![image-20210126043912132](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126043912132.png)