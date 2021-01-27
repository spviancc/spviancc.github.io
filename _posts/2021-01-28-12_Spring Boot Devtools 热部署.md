---
title: Spring Boot Devtools 热部署
tags: SpringBoot
---

## Spring Boot Devtool 热部署

热部署就是在修改了代码后不需要手动重启，工具会帮你快速的自动重启是修改生效。深层原理是使用了两个 `ClassLoader`，一个 `Classloader` 加载那些不会改变的类（第三方Jar包），另一个 `ClassLoader` 加载会更改的类，称为 `restart ClassLoader`，这样在有代码更改的时候，原来的 `restart ClassLoader` 被丢弃，重新创建一个 `restart ClassLoader`，由于需要加载的类相比较少，从而实现了较快的重启。[官网](![](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128021026864.png))



### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                </configuration>
            </plugin>
        </plugins>
    </build>            
```



### 修改 IDEA 设置

切换 IDEA 界面到其他应用（浏览器）之后自动部署

![image-20210128020146593](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128020146593.png)



快捷键 `CTRL + SHFT + ALT + / `  选择 `Registry`。可以按你自己按的字母搜索

![image-20210128020525075](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128020525075.png)



### 测试

启动应用——修改代码——切换到其他应用——查看结果

![image-20210128021026864](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210128021026864.png)