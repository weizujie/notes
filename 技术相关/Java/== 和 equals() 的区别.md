对于 ```==```，有两个场景：

- 如果是基本数据类型：使用 ```==``` 比较的是值

  ```java
  int a = 10;
  int b = 10;
  int c = 20;
  
  a == b --> true
  a == c --> false;
  ```

- 如果是对象：使用 ```==``` 比较的是内存地址

  ```java
  People p1 = new People();
  People p2 = new People();
  
  p --> false
  ```

- 不过有个特殊情况，那就是 ```Integer```，如果两个 ```Integer``` 的值在 ```[-128, 127]```，之间并且相等，那么使用 ```==``` 比较，两个对象也是相等的

  ```java
  Integer a = 100;
  Integer b = 100;
  
  a == b --> true
  ```

对于 ```e