---
title: Spring Boot Aop 记录用户操作 
tags: SpringBoot
---

## Spring Boot Aop 记录用户操作

在 Spring 框架中，使用 AOP 配合自定义注解可以方便的实现记录用户操作。



### 引入依赖

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
 </dependency>
```



### 自定义注解

用于标记用户操作的方法

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Log {
    String value() default "";
}
```



### 创建实体类来存储用户的操作

```java
import java.io.Serializable;
import java.util.Date;

public class myLog implements Serializable {
    private String Method;
    private String description ;
    private String parame;
    private Date date;

    public String getMethod() {
        return Method;
    }

    public void setMethod(String method) {
        Method = method;
    }

    public Date getDate() {
        return date;
    }

    public void setDate(Date date) {
        this.date = date;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getParame() {
        return parame;
    }

    public void setParame(String parame) {
        this.parame = parame;
    }

    @Override
    public String toString() {
        return "myLog{" +
                "Method='" + Method + '\'' +
                ", description='" + description + '\'' +
                ", parame='" + parame + '\'' +
                ", date=" + date +
                '}';
    }
}

```



### 切面类

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.core.LocalVariableTableParameterNameDiscoverer;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;
import java.util.Date;

@Aspect
@Component
public class LogAspect {

    @Pointcut("execution(* com.example.demo.contrller.*.*(..))")
    public void pointcut(){}

    @Around("pointcut()")
    public Object myAround(ProceedingJoinPoint point){
        Object result = null;
        try {
            // 执行方法
            result = point.proceed();
        } catch (Throwable e) {
            e.printStackTrace();
        }
        // 保存日志
        saveLog(point);
        return result;
    }

    private void saveLog(ProceedingJoinPoint joinPoint) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        myLog myLog = new myLog();
        Log logAnnotation = method.getAnnotation(Log.class);
        if (logAnnotation != null) {
            // 注解上的描述
            myLog.setDescription(logAnnotation.value());
        }
        // 请求的方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        myLog.setMethod(className + "." + methodName + "()");
        // 请求的方法参数值
        Object[] args = joinPoint.getArgs();
        // 请求的方法参数名称
        LocalVariableTableParameterNameDiscoverer u = new LocalVariableTableParameterNameDiscoverer();
        String[] paramNames = u.getParameterNames(method);
        if (args != null && paramNames != null) {
            String params = "";
            for (int i = 0; i < args.length; i++) {
                params += "  " + paramNames[i] + ": " + args[i];
            }
            myLog.setParame(params);
        }
        myLog.setDate(new Date());
        System.out.println(myLog);
    }
}
```



### 测试

修改 IndexController

![image-20210126232932606](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126232932606.png)

![image-20210126232901547](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126232901547.png)

![image-20210126233002574](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126233002574.png)