## 前期准备

在 ```user-service``` 服务编写一个接口，用来给 ```order-service``` 服务调用：

```java
@GetMapping("/user/{id}")
public String getById(@PathVariable("id") Long id) {
    return "用户ID: " + id;
}
```

## 使用 openfeign

1. 在 ```order-service``` 服务引入 ```openfeign``` 依赖：
    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    ```
    
2. 在启动类添加注解 ```@EnableFeignClients```（扫描所有带有 ```@FeignClient``` 注解的类，并注入到 Spring 容器中）：

    ```java
    @EnableFeignClients
    @SpringBootApplication
    public class OrderApplication {
        public static void main(String[] args) {
            SpringApplication.run(OrderApplication.class, args);
        }
    }
    ```

3. 编写 ```FeignClient``` 接口：

    ```java
    @FeignClient("user-service")
    public interface UserClient {
        @GetMapping("/user/{id}")
        String getById(@Pathvariable("id") Long id);
    }
    ```

4. 在 controller 中注入 ```UserClient```，调用方法：

    ```java
    @Autowired
    private UserClient userClient;
    
    @GetMapping("/test")
    public String test() {
        return userClient.getById(123456L);
    }
    ```

5. 输出结果：

    <img src="D:\学习笔记\技术相关\SpringCloudAlibaba\Untitled.assets\image-20220309115007468.png" alt="image-20220309115007468" style="zoom:67%;" />
