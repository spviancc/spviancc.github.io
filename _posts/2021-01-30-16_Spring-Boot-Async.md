---
title: Spring Boot Async
tags: SpringBoot
---

## Spring Boot Async

通常我们的程序都是同步的，但有时候也会使用异步的方式，来提高程序的效率。



### 开启异步

在启动类上添加 `@EnableAsync` 注解来开启异步

![image-20210130183820379](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130183820379.png)



### 异步方法

使用 `@Async` 注解来标明此方法是异步方法

```java
@Component
public class AsyncUtils {

    @Async
    public void asyncOut() {
        long start = System.currentTimeMillis();
        System.out.println("异步线程：开始");
        System.out.println("异步线程：" + this.getClass().getName());
        System.out.println("异步线程：结束");
        long end = System.currentTimeMillis();
        System.out.println("异步线程耗时："+(end-start)+" ms");
    }

}

```



### 测试

![image-20210130184050140](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130184050140.png)



从结果上可以看到，它和平时的执行顺序不一样，异步方法是另起一个线程来执行的

![image-20210130184234595](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130184234595.png)



### 接受异步的返回结果



##### 修改异步方法

给异步方法添加返回值，用 AsyncResult<> 来返回我们的结果，用 Future<String> 来接受结果

```java
    @Async
    public Future<String> asyncOut() {
        long start = System.currentTimeMillis();
        System.out.println("异步线程：开始");
        System.out.println("异步线程：" + this.getClass().getName());
        System.out.println("异步线程：结束");
        long end = System.currentTimeMillis();
        System.out.println("异步线程耗时："+(end-start)+" ms");
        return new AsyncResult<>("异步方法执行完毕");

    }
```

![image-20210130185952801](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130185952801.png)



##### 测试

![image-20210130190254776](G:\images\image-20210130190254776.png)



同步方法并没有一次性执行完，而是在 `future.get()` 这里处于阻塞状态，等待异步执行完毕后返回的结果

![image-20210130190420374](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130190420374.png)



### 自定义线程池

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.ThreadPoolExecutor;

@Configuration
public class ThreadPool {
    @Bean
    public ThreadPoolTaskExecutor asyncThreadPoolTaskExecutor(){
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        //线程池核心线程的数量，默认值为1（这就是默认情况下的异步线程池配置使得线程不能被重用的原因）
        executor.setCorePoolSize(20);
        //线程池维护的线程的最大数量，只有当核心线程都被用完并且缓冲队列满后，才会开始申超过请核心线程数的线程，默认值为Integer.MAX_VALUE。
        executor.setMaxPoolSize(200);
        //缓冲队列。
        executor.setQueueCapacity(25);
        //超出核心线程数外的线程在空闲时候的最大存活时间，默认为60秒。
        executor.setKeepAliveSeconds(200);
        //线程名前缀。
        executor.setThreadNamePrefix("asyncThread");
        //是否等待所有线程执行完毕才关闭线程池，默认值为false。
        executor.setWaitForTasksToCompleteOnShutdown(true);
        //waitForTasksToCompleteOnShutdown的等待的时长，默认值为0，即不等待
        executor.setAwaitTerminationSeconds(60);

        //当没有线程可以被使用时的处理策略（拒绝任务），默认策略为abortPolicy
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());

        executor.initialize();
        return executor;
    }
}
```

![image-20210130193657121](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130193657121.png)

```
discardOldestPolicy：当线程池中的数量等于最大线程数时、抛弃线程池中最后一个要执行的任务，并执行新传入的任务。
abortPolicy：直接抛出java.util.concurrent.RejectedExecutionException异常。
callerRunsPolicy：用于被拒绝任务的处理程序，它直接在 execute 方法的调用线程中运行被拒绝的任务；如果执行程序已关闭，则会丢弃该任务。
discardPolicy：当线程池中的数量等于最大线程数时，不做任何动作。
```



使用自定义线程池时，只需在注解 `@Async` 的 Value 属性中添加线程池的 Bean name

![image-20210130194544356](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130194544356.png)

![image-20210130194523295](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210130194523295.png)

