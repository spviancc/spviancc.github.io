---
title: 初次Spring Boot
tages: Spring Boot
---



## 初始化项目

这里使用idea编辑器为例

![image-20210125011630642](https://i.loli.net/2021/01/25/uxKiHNJlTVfAdmF.png)

![image-20210125011838333](https://i.loli.net/2021/01/25/cloNqAFRng425Uv.png)

![image-20210125012049064](https://i.loli.net/2021/01/25/1AdVZcXEsoKG74Q.png)

建好后需要等它加载依赖包，可能会有点慢

## 项目演示

初始化完成后的目录结构

![image-20210125012446449](https://i.loli.net/2021/01/25/sBqA4IwHjfLgzce.png)





### 创建控制层

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class IndexController {

    @GetMapping("/")
    public String index(){
        return "Hello Spring Boot";
    }
}
```



## 测试

启动 DemoApplication

启动成功后，会告诉我们**端口号**和**路径**

在浏览器中输入`http://localhost:8080/`

![image-20210125012916249](https://i.loli.net/2021/01/25/iM26jsgrlqFIOmK.png)

如果没有出错的话，我们可以看到它展示了我们书写在控制层内容

![image-20210125013112848](https://i.loli.net/2021/01/25/wN9ils1fz8GHgoJ.png)