# 并发编程

## 1. 进程与线程



## 2. 并发与并行

> **单核** CPU 下，线程实际上还是**串行执行**的。
>
> 操作系统中有个组件叫**任务调度器**，将 CPU 的**时间片**分给不同的线程使用，只是由于 CPU 在线程之间切换得非常快，让我们感觉是同时运行。
>
> 总结一句话：微观串行，宏观并行。
>
> 一般将这种**线程轮流使用** CPU 的做法称为**并发**（concurrent）

<img src="D:\学习笔记\技术相关\JUC\JUC学习笔记.assets\image-20220401142047374.png" alt="image-20220401142047374" style="zoom:50%;" />

> 多核 CPU 下，每个**核（core）**都可以调度运行线程，这时候线程是**并行（parallel）**的

<img src="D:\学习笔记\技术相关\JUC\JUC学习笔记.assets\image-20220401142329249.png" alt="image-20220401142329249" style="zoom:50%;" />

引用 Rob Pike 的一段描述：

- 并发（concurrent）是同一时间**应对**（dealing with）**多件事**的能力
- 并行（parallel）是同一时间**动手做**（doing）多件事的能力

例子：

- 并发：家庭主妇做饭、打扫卫生、奶孩子，一个人轮流交替做多件事
- 又并发又并行：雇了一个保姆，她俩一起做这些事（会产生竞争，孩子只有一个，一个人奶的时候，另一个人就需要等待）
- 并行：雇了三个保姆，一个做饭，一个打扫卫生，一个奶孩子，互不干扰

## 3. 同步与异步

从**方法调用**角度来讲，如果

- **需要等待**结果返回才能继续运行，就是**同步**
- **不需要等待**结果返回就能继续运行，就是**异步**

注意：同步在**多线程中**的意思是**让多个线程步调一致**

## 4. Java 线程

### 4.1 创建和运行线程

#### 4.1.1 方法一：Thread

```java
// 创建线程 t1
Thread t1 = new Thread("t1") {
    @Override
    public void run() {
        System.out.println("hello");
    }
};
// 启动线程 t1
t1.start();
```

#### 4.1.2 方法二：Runnable 配合 Thread

把【线程】和【任务（要执行的代码）】分开

- Thread 表示线程
- Runnable 表示可运行的任务（线程要执行的代码）

```java
// 创建任务对象 task1
Runnable task1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("hello");
    }
};
// 创建线程对象 t2，第一个参数是任务对象，第二个参数是线程名
Thread t2 = new Thread(task1, "t2");
// 运行线程 t2
t2.start();
```

创建任务对象可以使用 lambda 来简化，上面的代码就可以简化为：

```java
Runnable task1 = () -> System.out.println("hello");

Thread t2 = new Thread(task1, "t2");

t2.start();
```

#### 4.1.3 Thread 和 Runnable 的关系

- 方法一是把【线程】和【任务】合并到一起，方法二则是把二者分开
- 用 Runnable 更容易与线程池等高级 API 配合
- **用 Runnable 让任务类脱离了 Thread 的继承体系，更灵活**

#### 4.1.4 方法三：FutureTask 配合 Thread

FutureTask 能够接收 Callable 类型的参数，用来处理**有返回结果**的情况

```java
// 创建任务对象 task
FutureTask<String> task = new FutureTask<>(new Callable<String>() {
    @Override
    public String call() throws Exception {
        System.out.println("running...");
        Thread.sleep(1000);
        return "end...";
    }
});
// 创建好任务后，需要用 Thread 来启动
// FutureTask 类实现了 RunnableFuture 接口，而 RunnableFuture 接口又集成了 Runnable 类
// 所以 Thread 的构造方法可以接收 task
Thread t1 = new Thread(task, "t1");
// 启动线程
t1.start();

// 主线程 main 运行到这里，发现有 task.get()，那么主线程 main 会阻塞，一直等到 t1 线程执行完毕
System.out.println(task.get());
```

创建任务对象的代码可以用 lambda 来简化：

```java
FutureTask<String> task = new FutureTask<>(() -> {
    System.out.println("running...");
    Thread.sleep(1000);
    return "end...";
});

Thread t1 = new Thread(task, "t1");

t1.start();

System.out.println(task.get());
```

### 4.2 查看进行和线程的方法

#### Windows

- 任务管理器可以查看进程和线程数，也可以用来杀死进程
- tasklist 查看进程
- taskkill 杀死进程

<img src="D:\学习笔记\技术相关\JUC\JUC学习笔记.assets\image-20220401153023753.png" alt="image-20220401153023753" style="zoom: 67%;" />

#### Linux

- ps -fe 查看所有进程

- ps -fT -p <PID> 查看某个进程（PID）的所有线程
- kill 杀死进程
- top 按大写 H 切换是否显示线程
- top -H -p <PID> 查看某个进程（PID）的线程

<img src="D:\学习笔记\技术相关\JUC\JUC学习笔记.assets\image-20220401153358177.png" alt="image-20220401153358177" style="zoom:67%;" />
