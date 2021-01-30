---
title: Spring Boot CORS
tags: SpringBoot
---

## Spring Boot CORS

HTML 5中新增的跨域资源访问（Cross-Origin Resource Sharing）特性可以让我们在开发后端系统的时候决定资源是否允许被跨域访问。

![image-20210130201709189](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130201709189.png)



### 使用 ajax 模拟跨域

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
</head>
<body>
跨域测试
</body>
<script>
    $.ajax({
        url: "http://127.0.0.1:8080/usr?id=1",
        method: "get",
        dataType: "application/json",
        success: function (data) {
            alert(data)
        }
    });
</script>
</html>
```



### 注册实现跨域

```java

@Configuration
public class ORCSConfig implements WebMvcConfigurer{

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**") //允许所有请求跨域
                .allowedMethods("GET") //支持GET方法
                .allowedOrigins("*"); //不限定域
    }

}
```

![image-20210130204958443](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130204958443.png)





### 注解实现跨域

![image-20210130205419510](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130205419510.png)



| 属性             | 含义                                                         |
| :--------------- | ------------------------------------------------------------ |
| value            | 指定所支持域的集合，`*  ` 表示所有域都支持，默认值为 `*`。这些值对应HTTP请求头中的`Access-Control-Allow-Origin ` |
| origins          | 同 value                                                     |
| allowedHeaders   | 允许请求头中的 header，默认都支持                            |
| exposedHeaders   | 响应头中允许访问的 header，默认为空                          |
| methods          | 支持请求的方法，比如 `GET`，`POST`，`PUT` 等，默认和 Controller 中的方法上标注的一致。 |
| allowCredentials | 是否允许 cookie 随请求发送，使用时必须指定具体的域           |
| maxAge           | 预请求的结果的有效期，默认 30 分钟                           |



### 过滤器实现

```java
@Bean
public FilterRegistrationBean corsFilter() {
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowCredentials(true);
    config.addAllowedOrigin("*");
    source.registerCorsConfiguration("/**", config);
    FilterRegistrationBean bean = new FilterRegistrationBean(new CorsFilter(source));
    bean.setOrder(0);
    return bean;
}
```

