# 进程和线程

进程：

线程：

Java 默认有两个线程：main、GC

Java 不能开启线程，只能用 native 修饰的方法 start0() 调用

## 并发、并行

并发：多个线程同时操作一个资源

并行：多个人一起行走

并发编程的本质：**充分利用 CPU 的资源**

## 多线程

线程的状态：

```java
public enum State {
    // 新生
    NEW,

    // 运行
    RUNNABLE,

    // 阻塞
    BLOCKED,

    // 等待（死死地等）
    WAITING,

    // 超时等待（过期不候）
    TIMED_WAITING,

    // 终止
    TERMINATED;
}
```

wait 和 sleep 的区别：

1. 来自不同的类

   wait => Object 类

   sleep => Thread 类

2. 锁的释放

   wait 会释放锁

   sleep 不会释放锁

3. 使用的范围

   wait 必须在同步代码块使用

   sleep 可以在任何地方使用

# Lock

Synchronized







