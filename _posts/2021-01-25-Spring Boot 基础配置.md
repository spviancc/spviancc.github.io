---
title: Spring Boot 基础配置
tags: SpringBoot
---

## 定制Banner

Spring Boot 在启动时会有一个默认的启动图案，我们可以更改这个图案。图案可以在 [ASCII Generator](http://www.network-science.de/ascii/)  生成，不支持中文。

![image-20210125015123462](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210125015123462.png)



生成自己的Banner后，在 `src/main/resource` 下新建 banner.txt 文件，文件中放入我们生成的图案，启动项目就可以看到了我们自己的图案了。

![image-20210125015832395](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210125015832395.png)



如果不需要图案也是可以关闭的，只不过我们需要去修改启动程序的代码，重新启动就好了。

```java
public static void main(String[] args) {
       //SpringApplication.run(DemoApplication.class, args);
        SpringApplication app=new SpringApplication(DemoApplication.class);
        app.setBannerMode(Banner.Mode.OFF);  //这里可以设置banner的开启可关闭
        app.run(args);
    }
```



## 全局配置

在 `src/main/resources` 目录下，Spring Boot 提供了一个名为 application.properties 的全局配置文件，可对一些默认配置的配置值进行修改，英语好的可以点击 [这里](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html) 去官网查看相关的配置。

除了默认配置，我们还可以定义自己的配置值。   



### 自定义属性值

在 `application.properties` 下自定义一些属性值，如

```properties
you.name=spvian
you.tages=Spring Boot
```



创建 user 类，并用注解 @Component 来把它加入到容器。属性的值用注解 @Value 来获取配置中设置好的。

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class User {

    @Value("${you.name}")
    private String name;

    @Value("${you.tages}")
    private String tages;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getTages() {
        return tages;
    }

    public void setTages(String tages) {
        this.tages = tages;
    }
}
```



修改IndexController，注入Bean，完成后启动项目。

```java
@RestController
public class IndexController {

    @Autowired
    User user;

    @GetMapping("/")
    public String index(){
        return user.getName()+"--"+user.getTages();
    }

}
```

![image-20210125024956146](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210125024956146.png)



### 属性间的引用

```properties
you.name=spvian
you.tages=Spring Boot
you.title=${you.name}--${you.tages}
```



## 自定义配置文件

除了可以在 `application.properties` 里配置属性，我们还可以自定义配置文件。在 `src/main/resources` 目录下新建一个 `test.properties`。

```properties
test.name=spvian
test.tages=Spring Boot
```



创建对应的类，并修改 IndexController，除此之外还需要在启动类上加上 @EnableConfigurationProperties(TestConfig.class) 来启用该配置

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;


@Configuration
@ConfigurationProperties(prefix = "test")
@PropertySource("classpath:test.properties")
public class TestConfig {

    private String name;

    private String tages;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getTages() {
        return tages;
    }

    public void setTages(String tages) {
        this.tages = tages;
    }
}

```

使用 `@Configuration` 把它标识为配置类

注解 `@PropertySource("classpath:test.properties")` 指明使用哪个配置文件

注解 `@ConfigurationProperties(prefix = "test")` 可有效绑定到此对象的属性的前缀

  prefix属性的英文解释

> The prefix of the properties that are valid to bind to this object. Synonym for value(). A valid prefix is defined by one or more words separated with dots (e.g. "acme.system.feature").
> Returns:  the prefix of the properties to bind



## 通过命令行设置属性值

在运行 Spring Boot 的 jar 文件时，可以使用命令 `java -jar xxx.jar --server.port=8081` 来改变端口的值。这条命令等价于我们手动到 application.properties 中修改（如果没有这条属性的话就添加）server.port 属性的值为8081。

如果不想项目的配置被命令行修改，可以在启动类中的main方法中进行如下设置： 

```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(DemoApplication.class);
    app.setAddCommandLineProperties(false);
    app.run(args);
}
```



## 使用xml配置

Spring Boot 并不推荐我们使用 xml 配置，但如果出现不得不使用 xml 配置的情况，Spring Boot 允许我们在启动类里通过注解`@ImportResource({"classpath:xxxx.xml"})`来引入 xml 配置文件。



## Profile配置

Profile 用来针对不同的环境下使用不同的配置文件，多环境配置文件必须以 `application-{profile}.properties` 的格式命，其中`{profile}`为环境标识。

- application-dev.properties ：开发环境

  ```properties
  server.port=8080
  ```

- application-prod.properties ：生产环境

  ```properties
  server.port=8081
  ```

至于哪个配置文件会被加载，需要在 application.properties 文件中通过 `spring.profiles.active` 属性来设置，其值对应`{profile}` 的值。

如：`spring.profiles.active=dev` 就会加载 application-dev.properties 配置文件内容。

