# 可见性、原子性和有序性
## 可见性
一个线程对共享变量的修改，另一个线程能够**立刻**看到。
## 原子性
一个或多个操作在CPU执行的过程中不会被中断。
## 有序性
程序按照代码的先后顺序执行。但编译器为了优化性能，有时候会改变程序中的语句顺序。
例如：双重检查创建单利对象
```
public class Singleton {
	static Singleton instance;
	static Singleton getInstance() {
		if (instance == null) {
			synchronized(Singleton.class) {
				if (instance == null) {
					instance = new Singleton();
				}
			}
		}
		return singleton;
	}
}
```
看上去没啥问题，但其实问题出在 new 操作上，以为的 new 操作应该是：
1. 分配一块内存 M；
2. 在内存 M 上初始化 Singleton 对象；
3. 将 M 的地址赋值给 instance 变量。
但实际上经过编译器优化后的操作却是：
1. 分配一块内存 M；
2. 将 M 的地址赋值给 instance 变量；
3. 在内存 M 上初始化 Singleton 对象。
![[双重检查创建单例对象的异常执行路径.png]]