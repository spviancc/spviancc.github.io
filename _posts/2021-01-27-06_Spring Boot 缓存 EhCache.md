---
title: Spring Boot 缓存 EhCache
tags: SpringBoot
---

## Spring Boot 缓存 EhCache

EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是一种广泛使用的开源 Java 分布式缓存。

Spring 本身不直接提供缓存功能的实现。它支持注解方式使用缓存。注解的使用可以查看 [官方文档](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#cache)



### 注解说明

`@CacheConfig`：主要用于配置该类中会用到的一些共用的缓存配置。在这里 `@CacheConfig(cacheNames = "stu")`，配置了该数据访问对象中返回的内容将存储于名为 stu 的缓存对象中。我们也可以不使用该注解，直接通过`@Cacheable`自己配置缓存集的名字来定义。

`@Cacheable`：将方法的返回值加入缓存。同时在查询时，会先从缓存中获取，若不存在才再发起对数据库的访问。该注解主要有下面几个参数：

- `value、cacheNames`：两个等同的参数，非必需，用于指定缓存存储的集合名。

- `key`：缓存对象存储在Map集合中的 key。默认为 “ ”，意味着所有方法参数都被视为一个 key，除非已经配置了自定义的keyGenerator。
- `condition`：缓存对象的条件，非必需，也需使用 SpEL 表达式，只有满足表达式条件的内容才会被缓存。`@Cacheable(key = "#p0", condition = "#p0.length() < 3")`，表示只有当第一个参数的长度小于3的时候才会被缓存。
- `unless`：另外一个缓存条件参数，非必需，需使用SpEL表达式。它不同于 `condition` 参数的地方在于它的判断时机，该条件是在函数被调用之后才做判断的，所以它可以通过对result进行判断。
- `keyGenerator`：用于指定 key 生成器，非必需。若需要指定一个自定义的 key，我们需要去实现org.springframework.cache.interceptor.KeyGenerator 接口，并使用该参数来指定。
- `cacheManager`：用于指定使用哪个缓存管理器，非必需。只有当有多个时才需要使用。
- `cacheResolver`：用于指定使用那个缓存解析器，非必需。需通过 org.springframework.cache.interceptor.CacheResolver 接口来实现自己的缓存解析器，并用该参数指定。

`@CachePut`：配置方法上，能够根据参数定义条件来进行缓存，其缓存的是方法的返回值，它与 `@Cacheable` 不同的是，它每次都会真实调用函数，所以主要用于数据新增和修改操作上，它的参数与 `@Cacheable` 类似。

`@CacheEvict`：配置于方法上，通常用在删除方法上，用来从缓存中移除相应数据。除了同 `@Cacheable` 一样的参数之外，它还有两个参数：

- `allEntries`：非必需，默认为 false。当为 true 时，会移除所有数据；
- `beforeInvocation`：非必需，默认为 false，会在调用方法之后移除数据。当为 true 时，会在调用方法之前移除数据。



### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        <!-- ehcache -->
        <dependency>
            <groupId>net.sf.ehcache</groupId>
            <artifactId>ehcache</artifactId>
        </dependency>
```



### 使用注解

![image-20210127040748227](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127040748227.png)



### ehcache配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd">
    <!-- 默认配置 -->
    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="3600"
            timeToLiveSeconds="0"
            overflowToDisk="false"
            diskPersistent="false"
            diskExpiryThreadIntervalSeconds="120" />
    
    <!-- 自定义配置 -->
    <cache
            name="stu"
            maxEntriesLocalHeap="2000"
            eternal="false"
            timeToIdleSeconds="3600"
            timeToLiveSeconds="0"
            overflowToDisk="false"
            statistics="true"/>
    
<!--
cache ：自定的缓存，当自定的配置不满足实际情况时可以通过自定义（可以包含多个cache节点）
name : 缓存的名称，可以通过指定名称获取指定的某个Cache对象
maxElementsInMemory ：内存中允许存储的最大的元素个数，0代表无限个
clearOnFlush：内存数量最大时是否清除。
eternal ：设置缓存中对象是否为永久的，如果是，超时设置将被忽略，对象从不过期。根据存储数据的不同，例如一些静态不变的数据如省市区等可以设置为永不过时
timeToIdleSeconds ： 设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
timeToLiveSeconds ：缓存数据的生存时间（TTL），也就是一个元素从构建到消亡的最大时间间隔值，这只能在元素不是永久驻留时有效，如果该值是0就意味着元素可以停顿无穷长的时间。
overflowToDisk ：内存不足时，是否启用磁盘缓存。
maxEntriesLocalDisk：当内存中对象数量达到maxElementsInMemory时，Ehcache将会对象写到磁盘中。
maxElementsOnDisk：硬盘最大缓存个数。
diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
diskPersistent：是否在VM重启时存储硬盘的缓存数据。默认值是false。
diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
-->    
     
</ehcache>
```





### 开启配置

在 application.properties 中指明 EhCache 的配置文件位置

```properties
spring.cache.ehcache.config=classpath:ehcache.xml

#在配置中开启 debug 可以在测试执行过程中看到 sql 语句
#com.example.demo.mapper 是我数据访问层所在的包
logging.level.com.example.demo.mapper=debug  
```

在启动类上使用注解 `@EnableCaching` 来开启缓存

![image-20210127041502955](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127041502955.png)



### 测试

使用缓存前，去数据库中查询了两次。

![image-20210127041226732](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127041226732.png)

![image-20210127041115052](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127041115052.png)



使用缓存后，只查了一次数据

![image-20210127041427402](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210127041427402.png)