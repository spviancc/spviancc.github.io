---
title: Spring Boot 整合 Swagger2
tags: SpringBoot
---

## Spring Boot 整合 Swagger2

Swagger是一款可以快速生成符合RESTful风格API并进行在线调试的插件。



### 引入依赖

```XML
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.8.0</version>
        </dependency>
```



### Swagger2 注解

- `@Api`：修饰整个类，描述Controller的作用；
- `@ApiOperation`：描述一个方法，或者说一个接口；
- `@ApiParam`：单个参数描述；
- `@ApiModel`：用对象来接收参数；
- `@ApiProperty`：用对象接收参数时，描述对象的一个字段；
- `@ApiResponse`：HTTP响应其中1个描述；
- `@ApiResponses`：HTTP响应整体描述；
- `@ApiIgnore`：使用该注解忽略这个API；
- `@ApiError` ：发生错误返回的信息；
- `@ApiImplicitParam`：一个请求参数；
- `@ApiImplicitParams`：多个请求参数。



### Swagger2 配置

```java
@Configuration
@EnableSwagger2  //开启Swagger2
public class SwaggerConfig {
    @Bean
    public Docket buildDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(buildApiInf())
                .select()
            	//扫描路径
                .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
           	    //根据注解定制
            	//.apis(RequestHandlerSelectors.withMethodAnnotation(ApiImplicitParams.class))
                .paths(PathSelectors.any())
                .build();
    }
    private ApiInfo buildApiInf() {
        Contact contact = new Contact("姓名","网址","邮箱");
        return new ApiInfoBuilder()
           		 //文档首页标题
                .title("系统RESTful API文档") 
            	//文档描述信息
            	.description("swagger2-文档构建利器")
            	//创建者信息
            	.contact(contact) 
            	//文档版本
                .version("1.0")
                .build();
    }
}

```





### 修改 Controller 

![image-20210127221530198](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127221530198.png)



### 测试

http://localhost:8080/swagger-ui.html

![image-20210127221725794](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127221725794.png)

![image-20210127221816613](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127221816613.png)

![image-20210127222550393](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127222550393.png)

