---
title: Spring Boot 整合 MyBatis 通用 Mapper
tags: SpringBoot
---



## Spring Boot 整合 MyBatis 通用 Mapper

tk.mybatis 是在 MyBatis 框架的基础上提供了很多工具，让开发更加高效。

更多使用可以查看 [官网](https://github.com/abel533/Mapper/wiki) 



### 引入依赖

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
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.2</version>
        </dependency>
```



### 在 pom 中配置 MyBatis Geneator

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.15</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>3.4.4</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <!-- 自动生成的配置 -->
                    <configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>
                    <!-- 是否覆盖 -->
                    <overwrite>true</overwrite>
                    <!--允许移动生成的文件 -->
                    <verbose>true</verbose>
                </configuration>
            </plugin>
        </plugins>
```



### application.yml 配置

```yml
//后面做测试，提前把数据库配置配好
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2?serverTimezone=UTC
    username: root
    password: 123456

mybatis:
  type-aliases-package: com.example.demo.domain  #实体类的存放路径
  mapper-locations: classpath:mapper/*.xml
```



### 配置 Geneator

在 src/main/resource 目录下创建 generatorConfig.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- 引入数据库连接配置 -->
    <!-- <properties resource="jdbc.properties"/> -->

    <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 配置 tk.mybatis 插件 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="com.example.demo.utils.MyMapper"/>
        </plugin>

        <!-- 配置数据库连接 -->
        <jdbcConnection
                driverClass="com.mysql.cj.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/db2?serverTimezone=UTC"
                userId="root"
                password="123456">
        </jdbcConnection>

        <!-- 配置实体类存放路径 -->
        <javaModelGenerator targetPackage="com.example.demo.domain" targetProject="src/main/java"/>

        <!-- 配置 XML 存放路径 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>

        <!-- 配置 DAO 存放路径 -->
        <javaClientGenerator
                targetPackage="com.example.demo.dao"
                targetProject="src/main/java"
                type="XMLMAPPER"/>

        <!-- 配置需要指定生成的数据库和表，% 代表所有表 -->
        <table catalog="db2" tableName="stu">
            <!-- mysql 配置 -->
            <generatedKey column="id" sqlStatement="Mysql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```



### 创建一个通用的父级接口

主要作用是让 DAO 层的接口继承该接口，以达到使用 tk.mybatis 的目的。值得注意的是，该接口不能被扫描到。

```java
import tk.mybatis.mapper.common.Mapper;
import tk.mybatis.mapper.common.MySqlMapper;

public interface MyMapper<T> extends Mapper<T>, MySqlMapper<T> {
}
```



### 代码生成

使用命令

```bash
mvn mybatis-generator:generate
```



使用 maven 的插件管理

![image-20210126040936011](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126040936011.png)



#### 生成的实体类

![image-20210126041244314](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126041244314.png)



#### 数据访问层

这里继承了通用的父级接口 MyMapper

![image-20210126041333702](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126041333702.png)



#### 生成的Mapper

![image-20210126041529678](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126041529678.png)



## 测试

修改 IndexController

![image-20210126041642863](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126041642863.png)



在启动类上配置数据访问层的扫描 （指定 Mapper 接口的路径）

![image-20210126042641572](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126042641572.png)

![image-20210126041710831](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210126041710831.png)