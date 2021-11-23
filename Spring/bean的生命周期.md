## Java Bean 的生命周期

在传统的 Java 应用中，bean（也可以说是对象）的生命周期很简单，一句话理解就是：使用 new 关键字进行 bean 的实例化，然后该 bean 就可以使用了，一旦该 bean 不再被使用，则由 Java 自动进行垃圾回收。

## Spring 容器中 Bean 的生命周期

与传统的 Java Bean 生命周期相比，Spring Bean 的生命周期则要复杂得多，分为以下几个步骤：

1. 对 bean 进行实例化
2. 将值和 bean 的引用注入到 bean 对应的属性中
3. 如果 bean 实现了 BeanNameAware 接口，调用 BeanNameAware 的 setBeanName() 方法
4. 如果 bean 实现了 BeanFactoryAware 接口，调用 BeanFactoryAware 的 setBeanFactory() 方法，将 BeanFactory 实例传进来
5. 如果 bean 实现了 ApplicationContextAware 接口，调用 ApplicationContextAware 的 setApplicationContext() 方法，将 bean 所在的应用上下文（Spring 容器中的一种）的引用传进来
6. 如果 bean 实现了 BeanPostProcessor 接口，调用 BeanPostProcessor 的 postProcessBeforeInitialization() 方法
7. 如果 bean 实现了 InitializingBean 接口，调用 InitializingBean 的 afterPropertiesSet() 方法
8. 如果 bean 实现了 ApplicationContextAware 接口，调用自定义的初始化方法
9. 如果 bean 实现了 BeanPostProcessor 接口，调用 BeanPostProcessor 的 postProcessAfterInitialization() 方法。此时 bean 就可以使用了，该 bean 会一直存在于 Sping 容器中，直到该应用上下文被销毁
10. 如果 bean 实现了 DisposableBean 接口，调用 DisposableBean 的 destroy() 接口方法。