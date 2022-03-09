## 一、整合步骤

### 1、在 nacos 配置列表中新建配置文件

![image-20220308164837198](D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220308164837198.png)

### 2、填入配置信息

> Data ID 唯一，命名格式：服务名称+开发环境

<img src="D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220309105226660.png" alt="image-20220309105226660" style="zoom:67%;" />

### 3、引入依赖 

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

### 4、新建 ```bootstrap.yml``` 

在子工程中新建 ```bootstrap.yml``` 

**填入 nacos 地址、当前环境、服务名称、文件后缀名，这些决定了程序在启动时去 nacos 读取哪个文件**

```yaml
spring:
  application:
    name: user-service # 服务名称
  profiles:
    active: dev # 环境
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
      config:
        file-extension: yaml # 后缀
```

> 服务名称-环境.后缀 = Data ID
>
> user-service-dev.yaml

### 5、获取 nacos 配置文件里的内容

```java
@RestController
public class UserController {
    @Value("${pattern.dateFormat}")
    private String dateFormat;

    @GetMapping("/dateFormat")
    public String getDateFormat() {
        return dateFormat;
    }
}
```

> 如果用 @Value 注解获取不到值：
>
> 1. 将 nacos 版本换成 1.4.2
> 2. 检查 nacos 上的配置文件名称

访问 ```http://localhost:8082/dateFormat```，返回结果：

<img src="D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220309105353742.png" alt="image-20220309105353742" style="zoom:67%;" />

### 配置文件获取步骤

#### 1、未使用 Nacos

![image-20220308165500842](D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220308165500842.png)

#### 2、使用 Nacos

![image-20220308165700218](D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220308165700218.png)

> bootstrap.yml 的优先级高于 application.yml

## 二、配置热更新

nacos 中的配置文件变更后，可以通过以下两种方式，使程序无需重启即可更新

### 1、使用 @Value + @RefreshScope 注解

在 @Value 所在的类上添加注解 @RefreshScope 即可：

```java
@RefreshScope
@RestController
public class UserController {
    @Value("${pattern.dateFormat}")
    private String dateFormat;

    @GetMapping("/dateFormat")
    public String getDateFormat() {
        return dateFormat;
    }
}
```

### 2、使用 @ConfigurationProperties 注解（推荐，无需别的注解）

在 ```com.demo.config``` 下新建一个类，在类上添加 ```@ConfigurationProperties ``` 注解（prefix 表示前缀）：

```java
@Data
@Component
@ConfigurationProperties(prefix = "pattern")
public class Properties {
    private String dateFormat;
}
```

在 controller 中注入该类：

```java
@RestController
public class UserController {
    @Autowired
    private Properties properties;
    
    @GetMapping("/dateFormat")
    public String getDateFormat() {
        return properties.getDateFormat();
    }
}
```

在 nacos 上更变配置信息，服务的控制台输出日志：

![image-20220309110046095](D:\学习笔记\技术相关\SpringCloudAlibaba\02-整合Nacos作为配置中心.assets\image-20220309110046095.png)

### 3、注意事项

- 不是所有配置都适合放到配置中心（维护起来较麻烦）
- 建议将一些关键参数，需要运行时调整的参数放到 nacos 配置