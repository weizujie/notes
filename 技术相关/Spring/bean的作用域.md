## bean 的作用域

Spring 定义了多种作用域，可以基于这些作用域创建 bean：

- 单例（singleton）：在整个应用中，**只创建 bean 的一个实例**（默认）
- 原型（prototype）：每次注入或每次从容器中获取 bean 时，**都会创建一个新的 bean 实例**
- 会话（session）：在 web 应用中，为**每个会话**创建一个 bean 实例
- 请求（request）：在 web 应用中，为**每个请求**创建一个 bean 实例

单例是默认的作用域，如果要声明其他作用域，要使用 @Scope 注解，它可以与 @Component 或 @Bean 一起使用。下面是声明原型（prototype）bean 的方式：

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class User {
    private String username;
    private String password;
    
    // 省略 getter/setter
}
```

