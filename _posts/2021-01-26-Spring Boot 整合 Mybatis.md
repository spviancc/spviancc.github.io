---
title: Spring Boot 整合 Mybatis 
tags: SpringBoot
---

## Spring Boot 整合 Mybatis

继初试 Spring Boot 的基础上来继续开发。

我用的试 MySql 数据库，如有不同，请导入相应的驱动包，不清楚可以百度。



### 导入依赖

不同版本的 Spring Boot 和 MyBatis版本对应不一样，具体可查看 [官方文档](http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)

![image-20210126005935711](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126005935711.png)

```xml
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.3</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```



### MySql 数据源配置

创建表

```sql
create table `stu` (
	`id` int (11),
	`name` varchar (60),
	`age` int (11),
	`sex` varchar (30),
	`add_time` timestamp ,
	`update_time` timestamp 
); 
insert into `stu` (`id`, `name`, `age`, `sex`, `add_time`, `update_time`) values('1','ss1','20','男',NULL,NULL);
insert into `stu` (`id`, `name`, `age`, `sex`, `add_time`, `update_time`) values('2','sss','18','女',NULL,NULL);
insert into `stu` (`id`, `name`, `age`, `sex`, `add_time`, `update_time`) values('3','ssss','12','男',NULL,NULL);

```

application.yml 的配置

> 我这里把 application.properties 改为 application.yml ，

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2?serverTimezone=UTC
    username: root
    password: 123456
  
```



### 创建实体类

```java
import java.io.Serializable;
import java.sql.Date;

public class Stu implements Serializable {

    private Integer id;
    private String name;
    private Integer age;
    private String sex;
    private Date add_time;
    private Date update_time;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Date getAdd_time() {
        return add_time;
    }

    public void setAdd_time(Date add_time) {
        this.add_time = add_time;
    }

    public Date getUpdate_time() {
        return update_time;
    }

    public void setUpdate_time(Date update_time) {
        this.update_time = update_time;
    }


}

```



### 编写数据访问层

我这里使用 myabit 的注解来实现，也可以使用 xml 的方式来配置。更多注解的使用看以去 [官网](https://mybatis.org/mybatis-3/zh/java-api.html) 查看

![image-20210126015708402](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126015708402.png)

```java
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

@Repository
@Mapper
public interface StuDao {

    @Select("select * from stu where id=#{id}")
    Stu findById(Integer id);
}

```

xml 的配置需要在 application.yml 中添加以下配置

```yml
mybatis:
  # type-aliases扫描路径
  # type-aliases-package:
  # mapper xml实现扫描路径
  mapper-locations: classpath:mapper/*.xml
  property:
    order: BEFORE
```



### 测试

在 indexController 中修改

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class IndexController {

    @Autowired
    StuDao stuDao;

    @GetMapping(value = "/")
    public Stu index() {
        return stuDao.findById(1);
    }
}

```

![image-20210126020140674](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126020140674.png)
