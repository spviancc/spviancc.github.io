---
title: Spring Boot Filter and Interceptor
tags: SpringBoot
---

## Spring Boot Filter and Interceptor

在 Spring Boot 中使用过滤器和拦截器来计算 Controller 中方法的执行时长。 [Filter 文档](https://docs.spring.io/spring-boot/docs/1.5.14.RELEASE/reference/html/howto-embedded-servlet-containers.html#howto-add-a-servlet-filter-or-listener)



### Filter

实现 `javax.servlet.Filter`

```java
import org.springframework.stereotype.Component;
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@Component
@WebFilter(urlPatterns = "/usr/")
public class MyFilter implements Filter {
    
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("过滤器： 初始化");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("过滤器：开始过滤");
        long startime = System.currentTimeMillis();
        filterChain.doFilter(servletRequest,servletResponse);
        long endTime=System.currentTimeMillis();
        System.out.println("过滤器：结束过滤, 中间耗时："+(endTime-startime)+" ms");
    }

    @Override
    public void destroy() {
        System.out.println("过滤器：销毁");
    }
}

```



#### Filter 配置

**注册**

```java
@Configuration
public class FilterConfig {
    @Bean
    public FilterRegistrationBean<MyFilter> registrationBean(MyFilter myFilter){
        FilterRegistrationBean<MyFilter> registrationBean=new FilterRegistrationBean<>(myFilter);
        Collection<String> collection=new LinkedHashSet<>();
        collection.add("/usr/");
        collection.add("/login/");
        registrationBean.setUrlPatterns(collection);
        return  registrationBean;
    }
}
```



**注解**

![image-20210129222153375](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210129222153375.png)



#### 测试

![image-20210129222858671](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210129222858671.png)



### Interceptor

```java
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class MyHandlerInterceptor implements HandlerInterceptor {

    //先执行
   @Override
   public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
       System.out.println("拦截器： 开始");
       long startime = System.currentTimeMillis();
       request.setAttribute("startTime",startime);
        return true;
    }

    //在 preHandle 执行后执行，有异常不执行，跳到 afterCompletion
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("拦截器：处理");
    }

    //最后执行，不管有没有异常
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        Long startTime = (Long) request.getAttribute("startTime");
        long endTime=System.currentTimeMillis();
        System.out.println("拦截器：关闭,  中间耗时："+(endTime-startTime)+" ms");
    }
}

```



#### Interceptor 配置

![image-20210129223301053](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210129223301053.png)



现在不需要继承 WebMvcConfigurerAdapter，可以直接实现 WebMvcConfigurer

```java
import com.example.demo.filter.MyHandlerInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class HanderInterceptorConfig implements WebMvcConfigurer{

    @Autowired
    MyHandlerInterceptor myHandlerInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myHandlerInterceptor);
    }

}

```



#### 测试

**有异常**

![image-20210129224013699](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210129224013699.png)



**无异常**

![image-20210129224112432](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210129224112432.png)





