相关视频：https://www.bilibili.com/video/BV1Kr4y1i7ru

# 事务

> 2022-09-06 学习

## 事务简介

**事务是一组操作的集合**，这一组操作**要么全部成功，要么全部失败**。

## 事务操作

```sql
-- 开启事务
start transaction;

-- 提交事务
commit;

-- 回滚事务
rollback;
```

## 事务四大特性（ACID）

- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性（Durability）：事务一旦提交或回滚，它对数据的改变是永久的（存放在磁盘里）

## 并发事务问题

- 脏读：一个事务**读取**到另一个事务还**未提交**的数据
- 不可重复读：一个事务先后读取同一条记录，但**两次**读取的**数据结果都不同**
- 幻读：一个事务按照条件查询数据时没有对应的数据行，然后执行插入操作，发现又存在这行数据

## 事务隔离级别

不同的事务隔离级别可以解决并发下的事务问题

| 隔离级别         | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| 读未提交         |      |            |      |
| 读提交           | 解决 |            |      |
| 可重复读（默认） | 解决 | 解决       |      |
| 串行化           | 解决 | 解决       | 解决 |

```sql
-- 查询事务隔离级别
select @@transaction_isolation;

-- 设置事务隔离级别
set [sesstion|global] transaction isolation level {read uncommited | read commited | repeatable read | serializable}
```

# 存储引擎

> 2022-09-08 学习

## MySQL体系结构

- 连接层

    最上层是一些客户端和链接服务，主要完成一些类似连接处理、授权认证及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限

- 服务层

    第二层主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析与优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等

- 引擎层

    存储引擎真正负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们就可以根据不同的需求来选择不同的存储引擎

- 存储层

    主要将数据存储到文件系统之上，并完成与存储引擎的交互

## 存储引擎简介

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是**基于表**的，所以存储引擎也被称为表类型。

## 存储引擎特点

### InnoDB

- 介绍

    InnoDB是一种兼顾高性能和高可靠性的通用存储引擎，在MySQL5.5之后，InnoDB是默认的存储引擎。

- 特点

    DML（增删改）操作遵循ACID模型，**支持事务**；

    **行级锁**，提高并发访问能力；

    支持**外键**约束，保证数据的完整性和正确性；

- 文件

  xxx.ibd：xxx代表表名，InnoDB的每张表都会对应一个**表空间**文件，这个文件里包含该表的**表结构（frm、sdi）、数据和索引**。

  参数：innodb_file_per_table（如果打开，表示每一张表代表一个文件）
  
  ```sql
  -- 用该命令可用查看innodb_file_per_table是否打开
  show variables like 'innodb_file_per_table';
  ```

    ```cmd
    # Windows下cmd命令查看某个ibd文件的内容
    ibd2sdi	xxx.ibd
    ```
  
- 逻辑存储结构

    ![innodb逻辑存储结构](D:\学习笔记\技术相关\MySQL\MySQL学习笔记.assets\image-20220908111453102.png)

 ### MyISAM

- 介绍

    早期MySQL的默认存储引擎。

- 特点

    不支持事务，不支持外键

    **支持表锁**，不支持行锁

    **访问速度快**

- 文件

    xxx.sdi：存储表结构信息

    xxx.MYD：存储数据

    xxx.MYI：存储索引

### Memory

- 介绍

    表数据存储在内存中，由于受到硬件问题或断电问题的影响，只能将这些表用作零时表或缓存使用。

- 特点

    数据存放在内存中

    hash索引（默认）

- 文件

    xxx.sdi：存储表结构信息

# 索引

> 2022-09-08~2022-09-09 学习

## 索引简介

索引（index）是一种**有序的数据结构**，用来**高效地获取数据**。

优点：

- 提高数据检索效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序成本，降低CPU的消耗

缺点：

- 索引列也要占磁盘空间
- 降低对表的更新操作（如INSERT、UPDATE、DELETE）

## 索引结构

**重点学习B+Tree索引，平时说的索引，如果没有特别指明，那就是B+Tree索引**

MySQL的索引是在**存储引擎层**实现的，不同的存储引擎有不同的结构，结构主要包含如下：

- **B+Tree**索引：最常见的索引类型，大部分引擎都支持
- Hash索引：底层数据结构是用哈希表实现，只能精确查询，不能范围查询
- R-tree（空间索引）：MyISAM的特殊索引，主要用于地理空间数据类型（较少使用）
- Full-text（全文索引）：是一种通过建立倒排索引进行快速匹配文档的方式，类似于ES（较少使用）

不同存储引擎支持的索引情况：

![image-20220908144758863](D:\学习笔记\技术相关\MySQL\MySQL学习笔记.assets\image-20220908144758863.png)

### B+Tree

在MySQL中，对B+Tree进行了优化。在原B+Tree的基础上，增加了一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree，提高了区间的访问性能。

![image-20220908151203692](D:\学习笔记\技术相关\MySQL\MySQL学习笔记.assets\image-20220908151203692.png)

### Hash

哈希索引就是用一定的hash算法，将键值计算成新的hash值，映射到对应的槽位上，然后存储在hash表中。

如果多个键值映射到同一个槽位上，说明他们产生了hash冲突（也叫hash碰撞），可以用链表解决（拉链法）。

![image-20220908151904686](D:\学习笔记\技术相关\MySQL\MySQL学习笔记.assets\image-20220908151904686.png)

- 特点
    - 只能用于对等匹配（=，in）不支持范围查询（between，>，<，...）
    - 无法进行排序（因为经过hash运算出来的结果是无序的）
    - **查询效率高**，通常只需要一次检索就可以了（除非出现hash碰撞）

目前只有Memory引擎支持hash索引，但InnoDB中具有自适应hash功能，在特定条件下可以将B+Tree索引自动构建为hash索引。

## 索引分类

| 分类     | 含义                                                 | 特点                     | 关键字   |
| -------- | ---------------------------------------------------- | ------------------------ | -------- |
| 主键索引 | 针对于表中的主键创建的索引                           | 默认自动创建且只能有一个 | PRIMARY  |
| 唯一索引 | 避免同一个表中某数据列中的值重复                     | 可以有多个               | UNIQUE   |
| 常规索引 | 快速定位特定数据                                     | 可以有多个               |          |
| 全文索引 | 全文索引查找的是文本中的关键字，而不是比较索引中的值 | 可以有多个               | FULLTEXT |

在InnoDB中，根据索引的存储形式，又可以分为以下两种：

| 分类                                        | 含义                                                         | 特点                 |
| ------------------------------------------- | ------------------------------------------------------------ | -------------------- |
| 聚集索引（Clustered Index）                 | 将数据存储与索引放到了一块，**索引结构和叶子节点保存了行数据** | 必须有，而且只有一个 |
| 二级索引（Secondary Index），也叫非聚集索引 | 将数据与索引分开存储，**索引结构的叶子节点关联的对应的主键** | 可以有多个           |

### 聚集索引选取规则

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引
- 如果不存在主键或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为**隐藏**的聚集索引

### 回表查询

先走二级索引，拿到主键，再到聚集索引根据主键拿到行数据

## 索引语法

### 创建索引

```sql
create [unique|fulltext] index index_name on table_name (index_col_name, ...);
```

### 查看索引

```sql
show index from table_name;
```

### 删除索引

```sql
drop index index_name on table_name;
```

## SQL性能分析

### 查看访问频次

```sql
-- 查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次
show global status like 'Com_______'; -- 7个下划线
```

### 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的SQL语句日志。

MySQL慢查询日志默认没有开，查看是否开启：

```sql
show variables like 'show_query_log';
```

如果需要开启，在配置文件（my.cnf）中配置：

```
show_query_log=1  # 开启慢查询日志
long_query_time=2  # 设置时间
```

### explain

使用explain或desc命令获取MySQL如何执行select语句：

```sql
explain select * from account where id = 1;
```

![image-20220909111911310](D:\学习笔记\技术相关\MySQL\MySQL学习笔记.assets\image-20220909111911310.png)

- id：select查询的序列号，表示查询中执行select子句或是操作表的顺序（id相同，执行顺序从上到下；id不同，值越大越先执行）
- select_type：表示select的类型，常见的有SIMPLE、PRIMARY、UNION、SUBQUERY 等（了解就行了）
- type：表示连接类型，性能由好到差：NULL、system、const、eq_ref、ref、range、index、all
- possible_key：显示可能应用在这张表上用到的索引
- **key**：实际上使用的索引，如果为NULL，则表示没有使用索引
- key_len：索引中使用的字节数，该值是索引字段最大的可能使用长度，并非实际长度
- rows：查询行数，在InnoDB中是一个估值，并不准确
- filtered：返回结果的行数所占需要读取行数的百分比，越大越好

## 索引使用

### 最左前缀法则

查询从索引的**最左列开始**，并且不跳过索引中的列（仅针对**联合索引**）。

如果跳过了某一列，**索引将部分失效**（后面的字段索引失效）。

```sql
-- 假设profession、age、status三个字段建立了联合索引
-- 1.走索引，因为存在最左索引
select * from tb_user where profession = '软件工程' and age = 31 and status = '0';
-- 2.走索引，因为存在最左索引
select * from tb_user where profession = '软件工程' and age = 31;
-- 3.走索引，因为存在最左索引
select * from tb_user where profession = '软件工程';
-- 4.不走索引，因为不存在最左索引
select * from tb_user where age = 31 and status = '0';
-- 5.不走索引，因为不存在最左索引
select * from tb_user where status = '0';
```

联合索引中，出现**范围查询**（>, <），**范围查询右侧的索引会失效**。

```sql
-- 假设profession、age、status三个字段建立了联合索引
-- age字段是范围查询，右侧的字段索引失效，所以status字段没走索引
select * from tb_user where profession = '软件工程' and age > 30 and status = '0';

-- 可以将 > 改为 >=，这样就走索引了
select * from tb_user where profession = '软件工程' and age >= 30 and status = '0';
```

### 索引失效

- 1、索引列运算

    ```sql
    -- 假设phone建立了索引
    -- 索引失效，因为对phone字段的值进行了函数运算
    select * from tb_user where substring(phone, 10, 2) = '15';
    ```

- 2、字符串不加引号

    ```sql
    -- 假设phone建立了索引，并且phone的类型是varchar
    -- 索引失效，因为字符串没加引号，存在类型转换
    select * from tb_user where phone = 17799990015
    ```

- 3、**头部**模糊查询

    ```sql
    -- 假设profession建立了索引
    -- % 放在前面索引会失效，应该放在后面（前后都加%也失效）
    select * from tb_user where profession = '%工程';
    ```

- 4、or 前后**只有一个**字段建立索引

    ```sql
    -- 假设id建立了索引，age没建立索引
    -- 索引失效，因为只有 or 前后只有一个索引
    select * from tb_user where id = 10 or age = 31;
    ```

- 5、全表扫描比索引还快

### SQL提示

SQL提示是优化数据库的一个重要手段。简单来说， 就是在SQL语句中加入一些**人为的提示**来达到优化的目的。

- use index：**建议**数据库用哪个索引，数据库接不接受不知道

    ```sql
    select * from tb_user use index(idx_user_profession) where profession = '软件工程';
    ```

- ignore index：告诉数据库**不用**哪个索引

    ```sql
    select * from tb_user ignore index(idx_user_profession) where profession = '软件工程';
    ```

- force index：告诉数据库**一定**用哪个索引

    ```sql
    select * from tb_user force index(idx_user_profession) where profession = '软件工程';
    ```

### 覆盖索引

查询使用到了索引，并且需要返回的列已经在该索引中全部能够找到。

尽量使用覆盖索引，减少使用 ```select *```（因为很可能出现回表查询，查询效率低）

```sql
-- id主键索引，profession和age和status组合索引，name没有索引
-- 此时需要回表查询，执行计划中Extra是using index condition
select id, profession, age, `status`, name from tb_user where profession = '软件工程' and age = 31 and status = '0';
```

- using index condition：查找使用了索引，但是**需要回表**查询数据

- using where; using index：查找使用了索引，但是需要的数据都在索引列中找到，所以**不需要回表**查询时间

### 前缀索引

当字段类型是varchar、text等字符串类型时，有时需要索引很长的字符串，这样会让索引变得很大，查询时浪费大量的磁盘IO，影响查询效率。此时可以只将字符串的一部分前缀建立索引，这样可以大大节约索引空间，提高查询效率。

语法：

```sql
create index idx_xxx on table_name(column(n));  -- n表示前n个字符
```

### 单列&联合索引

- 单列索引：一个索引只包含一个列
- 联合索引：一个索引包含很多个列

在业务场景中，如果存在多个查询条件，应该考虑建立联合索引。

## 锁

> 2022-09-13 学习

### 分类

锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算机资源的争用外，数据也是一种供许多用户共享的资源。

在MySQL中，按照锁的粒度，分为三种：

- 全局锁：锁定数据库中的**所有表**
- 表级锁：每次操作锁住**整张表**
- 行级锁：每次操作锁住对应的**行数据**

#### 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于**只读状态**，后续的DML写语句、DDL语句以及更新操作事务提交语句都将被阻塞。

```sql
flush tables with read lock; -- 加锁
unlock tables; -- 解锁
```

#### 表级锁

每次操作锁住整张表。锁的颗粒度大，发生锁冲突的概率最高，并发度低。

表级锁分为三类：

- 表锁

    ```sql
    lock tables 表名 read/write; -- 加锁
    unlock tables; -- 解锁
    ```

    - 表共享**读锁**（read lock）
    - 表独占**写锁**（write lock）

- 元数据锁（meta data lock，MDL）

- 意向锁
