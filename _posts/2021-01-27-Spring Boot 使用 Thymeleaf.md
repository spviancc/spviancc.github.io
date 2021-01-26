---
title: Spring Boot 使用 Thymeleaf
tags: SpringBoot
---

## Spring Boot 使用 Thymeleaf

Thymeleaf 是适用于 Web 和独立环境的现代服务器端 Java 模板引擎，能够处理 HTML，XML，JavaScript，CSS 甚至纯文本。详情见 [官方文档](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#introducing-thymeleaf) 。



### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



### Thymeleaf 配置

可以在 application.properties 中配置 Thymeleaf 的属性

```properties
#在构建URL时添加到视图名称前的前缀（默认值：classpath:/templates/）
spring.thymeleaf.prefix=classpath:/templates/

#在构建URL时添加到视图名称后的后缀（默认值：.html）
spring.thymeleaf.suffix=.html

#开启模板缓存（默认值：true）
spring.thymeleaf.cache=true 

#Check that the template exists before rendering it.
spring.thymeleaf.check-template=true 

#检查模板位置是否正确（默认值:true）
spring.thymeleaf.check-template-location=true

#Content-Type的值（默认值：text/html）
spring.thymeleaf.content-type=text/html

#开启MVC Thymeleaf视图解析（默认值：true）
spring.thymeleaf.enabled=true

#模板编码
spring.thymeleaf.encoding=UTF-8

#要被排除在解析之外的视图名称列表，用逗号分隔
spring.thymeleaf.excluded-view-names=

#要运用于模板之上的模板模式。另见StandardTemplate-ModeHandlers(默认值：HTML5)
spring.thymeleaf.mode=HTML5

#Thymeleaf模板解析器在解析器链中的顺序。默认情况下，它排第一位。顺序从1开始，只有在定义了额外的TemplateResolver Bean时才需要设置这个属性。
spring.thymeleaf.template-resolver-order=

#可解析的视图名称列表，用逗号分隔
spring.thymeleaf.view-names=
```



### 创建 index.html

在 src/main/resource/templates 下创建 index.html 文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    Hello Spring Boot
</body>
</html>
```



### 创建控制层 IndexController

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class IndexController {

    @GetMapping("/")
    public String index(){
        return "index";
    }
}

```

![image-20210127010918209](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127010918209.png)