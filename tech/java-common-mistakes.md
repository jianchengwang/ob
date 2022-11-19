---
title: java-common-mistakes
createdAt: 2022-11-17
categories: 
- java
- mistake
tags: 
- java
- mistake
---

最近在喵极客的[Java业务常见的错误100例](https://time.geekbang.org/column/article/213295)，好记性不如烂笔头，这边简单做个笔记。源码拷贝于讲师的源码，放置在[todo-java/geekbang/java-common-mistakes](https://github.com/jianchengwang/todo-java/tree/main/geekbang/java-common-mistakes)

<!--more-->

## 并发工具不等于线程安全

### ThreadLocal线程复用

程序运行在 Tomcat 中，执行程序的线程是 Tomcat 的工作线程，而 Tomcat 的工作线程是基于线程池的。

线程池会重用固定的几个线程，一旦线程重用，那么很可能首次从 ThreadLocal 获取的值是之前其他用户的请求遗留的值

```properties
server.tomcat.max-threads=1
```

因此使用类似 ThreadLocal 工具来存放一些数据时，需要特别注意在代码运行完后，显式地去清空设置的数据


### ConrruentMap只能保证提供原子性读写是线程安全

我们需要注意 [ConcurrentHashMap](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html) 对外提供的方法或能力的限制：
1. 使用了 ConcurrentHashMap，不代表对它的多个操作之间的状态是一致的，是没有其他线程在操作它的，如果需要确保需要手动加锁。
2. 诸如 size、isEmpty 和 containsValue 等聚合方法，在并发情况下可能会反映 ConcurrentHashMap 的中间状态。因此在并发情况下，这些方法的返回值只能用作参考，而不能用于流程控制。显然，利用 size 方法计算差异值，是一个流程控制。
3. 诸如 putAll 这样的聚合方法也不能确保原子性，在 putAll 的过程中去获取数据可能会获取到部分数据。

### 并发工具的特性

```java
ConcurrentHashMap<String, Long> freqs = new ConcurrentHashMap<>(ITEM_COUNT);
synchronized (freqs) {      
	if (freqs.containsKey(key)) {
		//Key存在则+1
		freqs.put(key, freqs.get(key) + 1);
	} else {
		//Key不存在则初始化为1
		freqs.put(key, 1L);
	}
}
```

如果我们知道ConcurrentMap的computeIfAbsent方法，则可以大大提高性能，

```java
ConcurrentHashMap<String, LongAdder> freqs = new ConcurrentHashMap<>(ITEM_COUNT);
freqs.computeIfAbsent(key, k -> new LongAdder()).increment();
```

computeIfAbsent和putIfAbsent区别是三点： 
1. 当Key存在的时候，如果Value获取比较昂贵的话，putIfAbsent就白白浪费时间在获取这个昂贵的Value上（这个点特别注意） 
2. Key不存在的时候，putIfAbsent返回null，小心空指针，而computeIfAbsent返回计算后的值 
3. 当Key不存在的时候，putIfAbsent允许put null进去，而computeIfAbsent不能，之后进行containsKey查询是有区别的（当然了，此条针对HashMap，ConcurrentHashMap不允许put null value进去）

### 并发工具的使用场景

在 Java 中，CopyOnWriteArrayList 虽然是一个线程安全的 ArrayList，但因为其实现方式是，每次修改数据时都会复制一份数据出来，所以有明显的适用场景，即读多写少或者说希望无锁读的场景。

```
List copyOnWriteArrayList = new CopyOnWriteArrayList<>(); List synchronizedList = Collections.synchronizedList(new ArrayList<>());
```

## 代码加锁

### 锁和被保护的对象是不是一个层面的

我们知道静态字段属于类，类级别的锁才能保护；而非静态字段属于类实例，实例级别的锁就可以保护。

### 锁的粒度跟应用场景

如果精细化考虑了锁应用范围后，性能还无法满足需求的话，我们就要考虑另一个维度的粒度问题了，区分读写场景以及资源的访问冲突，考虑使用悲观方式的锁还是乐观方式的锁。

### 死锁问题

死锁条件
1. 互斥条件：并发进程所要求和占有的资源是不能同时被两个以上进程使用或操作的，进程对它所需要的资源进行排它性控制。
2. 不剥夺条件：进程所获得的资源在未使用完毕之前，不能被其它进程强行剥夺，而只能由获得该资源的进程自己释放。
3. 部分分配：进程每次申请它所需要的一部分资源，在等待新资源的同时，继续占用已分配到的资源。
4. 环路条件：存在一种进程循环链，链中每一个进程已获得的资源同时被下一个进程所请求。

要考虑多把锁的执行顺序

## 线程池

### 手动声明线程池

1. `Executors.newFixedThreadPool`，线程池的工作队列直接 new 了一个 LinkedBlockingQueue，而默认构造方法的 LinkedBlockingQueue 是一个 Integer.MAX_VALUE 长度的队列，可以认为是无界的
2. `Executors.newCachedThreadPool`, 最大线程数是 Integer.MAX_VALUE，可以认为是没有上限的，而其工作队列 SynchronousQueue 是一个没有存储空间的阻塞队，这意味着，只要有请求到来，就必须找到一条工作线程来处理，如果当前没有空闲的线程就再创建一条新的。
3. 推荐手动创建
```java
//创建一个具有2个核心线程、5个最大线程，使用容量为10的ArrayBlockingQueue阻塞队列作为工作队列的线程池，使用默认的AbortPolicy拒绝策略 
ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
	2, 5, 5, TimeUnit.SECONDS, 
	new ArrayBlockingQueue<>(10), 
	new ThreadFactoryBuilder().setNameFormat("demo-threadpool-%d").get(), 
	new ThreadPoolExecutor.AbortPolicy()
);
```

至此，我们可以总结出线程池默认的工作行为：

1. 不会初始化 corePoolSize 个线程，有任务来了才创建工作线程；
2. 当核心线程满了之后不会立即扩容线程池，而是把任务堆积到工作队列中；
3. 当工作队列满了后扩容线程池，一直到线程个数达到 maximumPoolSize 为止；如果队列已满且达到了最大线程后还有任务进来，按照拒绝策略处理；
4. 当线程数大于核心线程数时，线程等待 keepAliveTime 后还是没有任务需要处理的话，收缩线程到核心线程数。

### 线程池考虑复用，但要符合各自场景

Java 8 的 parallel stream 功能，可以让我们很方便地并行处理集合中的元素，其背后是共享同一个 ForkJoinPool，默认并行度是 CPU 核数 -1，对于 CPU 绑定的任务来说，使用这样的配置比较合适，但如果集合操作涉及同步 IO 操作的话（比如数据库操作、外部服务调用等），建议自定义一个 ForkJoinPool（或普通线程池）

## 连接池

### Jedis线程安全

我们在多线程环境下复用 Jedis 对象，其实就是在复用 RedisOutputStream。如果多个线程在执行操作，那么既无法确保整条命令以一个原子操作写入 Socket，也无法确保写入后、读取前没有其他数据写到远端。

修复方式是，使用 Jedis 提供的另一个线程安全的类 JedisPool 来获得 Jedis 的实例。JedisPool 可以声明为 static 在多个线程之间共享，扮演连接池的角色。使用时，按需使用 try-with-resources 模式从 JedisPool 获得和归还 Jedis 实例。

JedisPool 的 getResource 方法在拿到 Jedis 对象后，将自己设置为了连接池。连接池 JedisPool，继承了 JedisPoolAbstract，而后者继承了抽象类 Pool，Pool 内部维护了 Apache Common 的通用池 GenericObjectPool。JedisPool 的连接池就是基于 GenericObjectPool 的。看到这里我们了解了，Jedis 的 API 实现是我们说的三种类型中的第一种，也就是连接池和连接分离的 API，JedisPool 是线程安全的连接池，Jedis 是非线程安全的单一连接。知道了原理之后，我们再使用 Jedis 就胸有成竹了。

### HttpClient线程泄露

```java
CloseableHttpClient client = HttpClients.custom() .setConnectionManager(new PoolingHttpClientConnectionManager()) .evictIdleConnections(60, TimeUnit.SECONDS).build();
```

复用方式很简单，你可以把 CloseableHttpClient 声明为 static，只创建一次，并且在 JVM 关闭之前通过 addShutdownHook 钩子关闭连接池，在使用的时候直接使用 CloseableHttpClient 即可，无需每次都创建。

```java

private static CloseableHttpClient httpClient = null;
static {
    //当然，也可以把CloseableHttpClient定义为Bean，然后在@PreDestroy标记的方法内close这个HttpClient
    httpClient = HttpClients.custom().setMaxConnPerRoute(1).setMaxConnTotal(1).evictIdleConnections(60, TimeUnit.SECONDS).build();
    Runtime.getRuntime().addShutdownHook(new Thread(() -> {
        try {
            httpClient.close();
        } catch (IOException ignored) {
        }
    }));
}
```

### 配置合理的连接池参数

连接池参数配置中，最重要的是最大连接数，许多高并发应用往往因为最大连接数不够导致性能问题。但，最大连接数不是设置得越大越好，够用就好。需要注意的是，针对数据库连接池、HTTP 连接池、Redis 连接池等重要连接池，务必建立完善的监控和报警机制，根据容量规划及时调整参数配置。

### 思考题

假设我们希望设置连接超时5s，获取连接超时10s： 

hikari两个参数设置方式：

```properties
spring.datasource.hikari.connection-timeout=10000 spring.datasource.url=jdbc:mysql://localhost:6657/common_mistakes?connectTimeout=5000&characterEncoding=UTF-8&useSSL=false&rewriteBatchedStatements=true
```

jedis两个参数设置：

```java
JedisPoolConfig config = new JedisPoolConfig(); config.setMaxWaitMillis(10000); try (JedisPool jedisPool = new JedisPool(config, "127.0.0.1", 6379, 5000); Jedis jedis = jedisPool.getResource()) { return jedis.set("test", "test"); }
```

httpclient两个参数设置：

```java
RequestConfig requestConfig = RequestConfig.custom() .setConnectTimeout(5000) .setConnectionRequestTimeout(10000) .build(); HttpGet httpGet = new HttpGet("http://127.0.0.1:45678/twotimeoutconfig/test"); httpGet.setConfig(requestConfig); try (CloseableHttpResponse response = httpClient.execute(httpGet)) {...
```

## Http调用

首先，框架设置的默认超时是否合理；其次，考虑到网络的不稳定，超时后的请求重试是一个不错的选择，但需要考虑服务端接口的幂等性设计是否允许我们重试；最后，需要考虑框架是否会像浏览器那样限制并发连接数，以免在服务并发很大的情况下，HTTP 调用的并发数限制成为瓶颈。

### fegin坑点

结论一，默认情况下 Feign 的读取超时是 1 秒，如此短的读取超时算是坑点一。

结论二，也是坑点二，如果要配置 Feign 的读取超时，就必须同时配置连接超时，才能生效。

```properties
feign.client.config.default.readTimeout=3000
feign.client.config.default.connectTimeout=3000
feign.client.config.clientsdk.readTimeout=2000
feign.client.config.clientsdk.connectTimeout=2000
```

结论三，单独的超时可以覆盖全局超时，这符合预期，不算坑：

结论四，除了可以配置 Feign，也可以配置 Ribbon 组件的参数来修改两个超时时间。这里的坑点三是，参数首字母要大写，和 Feign 的配置不同。

```properties
ribbon.ReadTimeout=4000
ribbon.ConnectTimeout=4000
```

结论五，同时配置 Feign 和 Ribbon 的超时，以 Feign 为准

结论六，Ribbon 会自动重试请求

翻看 Ribbon 的源码可以发现，MaxAutoRetriesNextServer 参数默认为 1，也就是 Get 请求在某个服务端节点出现问题（比如读取超时）时，Ribbon 会自动重试一次

解决办法有两个：

一是，把发短信接口从 Get 改为 Post。其实，这里还有一个 API 设计问题，有状态的 API 接口不应该定义为 Get。根据 [HTTP 协议](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)的规范，Get 请求用于数据查询，而 Post 才是把数据提交到服务端用于修改或新增。选择 Get 还是 Post 的依据，应该是 API 的行为，而不是参数大小。这里的一个误区是，Get 请求的参数包含在 Url QueryString 中，会受浏览器长度限制，所以一些同学会选择使用 JSON 以 Post 提交大参数，使用 Get 提交小参数。

二是，将 MaxAutoRetriesNextServer 参数配置为 0，禁用服务调用失败后在下一个服务端节点的自动重试。在配置文件中添加一行即可：

```properties
ribbon.MaxAutoRetriesNextServer=0
```

### 并发限制爬虫能力

查看 PoolingHttpClientConnectionManager 源码，可以注意到有两个重要参数：defaultMaxPerRoute=2，也就是同一个主机 / 域名的最大并发请求数为 2。

我们的爬虫需要 10 个并发，显然是默认值太小限制了爬虫的效率。maxTotal=20，也就是所有主机整体最大并发为 20，这也是 HttpClient 整体的并发度。

目前，我们请求数是 10 最大并发是 10，20 不会成为瓶颈。

举一个例子，使用同一个 HttpClient 访问 10 个域名，defaultMaxPerRoute 设置为 10，为确保每一个域名都能达到 10 并发，需要把 maxTotal 设置为 100。

```java
httpClient2 = HttpClients.custom().setMaxConnPerRoute(10).setMaxConnTotal(20).build();
```

## Spring声明式事务

@Transactional 生效原则 1，除非特殊配置（比如使用 AspectJ 静态织入实现 AOP），否则只有定义在 public 方法上的 @Transactional 才能生效。

@Transactional 生效原则 2，必须通过代理过的类从外部调用目标方法才能生效。

只有异常传播出了标记了 @Transactional 注解的方法，事务才能回滚

默认情况下，出现 RuntimeException（非受检异常）或 Error 的时候，Spring 才会回滚事务。

第一，如果你希望自己捕获异常进行处理的话，也没关系，可以手动设置让当前事务处于回滚状态：

```java
@Transactional
public void createUserRight1(String name) {
    try {
        userRepository.save(new UserEntity(name));
        throw new RuntimeException("error");
    } catch (Exception ex) {
        log.error("create user failed", ex);
        TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
    }
}
```

第二，在注解中声明，期望遇到所有的 Exception 都回滚事务（来突破默认不回滚受检异常的限制）：

```java
@Transactional(rollbackFor = Exception.class)
public void createUserRight2(String name) throws IOException {
    userRepository.save(new UserEntity(name));
    otherTask();
}
```

如果方法涉及多次数据库操作，并希望将它们作为独立的事务进行提交或回滚，那么我们需要考虑进一步细化配置事务传播方式，也就是 @Transactional 注解的 Propagation 属性。

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

## Sql 索引

### InnoDB存储数据

InnoDB 采用页而不是行的粒度来保存数据，即数据被分成若干页，以页为单位保存在磁盘中。InnoDB 的页大小，一般是 16KB。

各个数据页组成一个双向链表，每个数据页中的记录按照主键顺序组成单向链表；每一个数据页中有一个页目录，方便按照主键查询记录。数据页的结构如下：

![](../media/java-common-mistakes/Pasted%20image%2020221117114603.png)

页目录通过槽把记录分成不同的小组，每个小组有若干条记录。如图所示，记录中最前面的小方块中的数字，代表的是当前分组的记录条数，最小和最大的槽指向 2 个特殊的伪记录。有了槽之后，我们按照主键搜索页中记录时，就可以采用二分法快速搜索，无需从最小记录开始遍历整个页中的记录链表。

举一个例子，如果要搜索主键（PK）=15 的记录：先二分得出槽中间位是 (0+6)/2=3，看到其指向的记录是 12＜15，所以需要从 #3 槽后继续搜索记录；再使用二分搜索出 #3 槽和 #6 槽的中间位是 (3+6)/2=4.5 取整 4，#4 槽对应的记录是 16＞15，所以记录一定在 #4 槽中；再从 #3 槽指向的 12 号记录开始向下搜索 3 次，定位到 15 号记录。理解了 InnoDB 存储数据的原理后，我们就可以继续学习 MySQL 索引相关的原理和坑了。

### 聚簇索引和二级索引

InnoDB 引入了 B+ 树，如下所示

![](../media/java-common-mistakes/Pasted%20image%2020221117114948.png)

B+ 树的特点包括：
1. 最底层的节点叫作叶子节点，用来存放数据；
2. 其他上层节点叫作非叶子节点，仅用来存放目录项，作为索引；
3. 非叶子节点分为不同层次，通过分层来降低每一层的搜索量；
4. 所有节点按照索引键大小排序，构成一个双向链表，加速范围查找。

InnoDB 会自动使用主键（唯一定义一条记录的单个或多个字段）作为聚簇索引的索引键（如果没有主键，就选择第一个不包含 NULL 值的唯一列）。上图方框中的数字代表了索引键的值，对聚簇索引而言一般就是主键。

由于数据在物理上只会保存一份，所以包含实际数据的聚簇索引只能有一个

我们再看看 B+ 树如何实现快速查找主键。比如，我们要搜索 PK=4 的数据，通过根节点中的索引可以知道数据在第一个记录指向的 2 号页中，通过 2 号页的索引又可以知道数据在 5 号页，5 号页就是实际的数据页，然后再通过二分法查找页目录马上可以找到记录的指针。

二级索引的叶子节点中保存的不是实际数据，而是主键，获得主键值后去聚簇索引中获得数据行。这个过程就叫作**回表**。

### 创建二级索引的代价

首先是维护代价。创建 N 个二级索引，就需要再创建 N 棵 B+ 树，新增数据时不仅要修改聚簇索引，还需要修改这 N 个二级索引。

页中的记录都是按照索引值从小到大的顺序存放的，新增记录就需要往页中插入数据，现有的页满了就需要新创建一个页，把现有页的部分数据移过去，这就是页分裂；如果删除了许多数据使得页比较空闲，还需要进行页合并。页分裂和合并，都会有 IO 代价，并且可能在操作过程中产生死锁。[查看更多](https://dev.mysql.com/doc/refman/5.7/en/index-page-merge-threshold.html)

其次是空间代价。虽然二级索引不保存原始数据，但要保存索引列的数据，所以会占用更多的空间。比如，person 表创建了两个索引后，使用下面的 SQL 查看数据和索引占用的磁盘：

```mysql
SELECT DATA_LENGTH, INDEX_LENGTH FROM information_schema.TABLES WHERE TABLE_NAME='person'
```

最后是回表的代价。二级索引不保存原始数据，通过索引找到主键后需要再查询聚簇索引，才能得到我们要的数据。比如，使用 SELECT * 按照 name 字段查询用户，使用 EXPLAIN 查看执行计划：

```msyql
EXPLAIN SELECT * FROM person WHERE NAME='name1'
```

如果我们需要查询的是索引列索引或联合索引能覆盖的数据，那么查询索引本身已经“覆盖”了需要的数据，不再需要回表查询。因此，这种情况也叫作**索引覆盖**

### 索引失效

第一，索引只能匹配列前缀。比如下面的 LIKE 语句，搜索 name 后缀为 name123 的用户无法走索引，执行计划的 type=ALL 代表了全表扫描：

```mysql
EXPLAIN SELECT * FROM person WHERE NAME LIKE '%name123' LIMIT 100
```

第二，条件涉及函数操作无法走索引

```mysql
EXPLAIN SELECT * FROM person WHERE LENGTH(NAME)=7
```

第三，联合索引只能匹配左边的列。也就是说，虽然对 name 和 score 建了联合索引，但是仅按照 score 列搜索无法走索引：

```msyql
EXPLAIN SELECT * FROM person WHERE SCORE>45678
```

原因也很简单，在联合索引的情况下，数据是按照索引第一列排序，第一列数据相同时才会按照第二列排序

```mysql

EXPLAIN SELECT * FROM person WHERE SCORE>45678 AND NAME LIKE 'NAME45%'
```

需要注意的是，因为有查询优化器，所以 name 作为 WHERE 子句的第几个条件并不是很重要。

第四，数据库基于成本考虑是否走索引，MySQL 在查询数据之前，会先对可能的方案做执行计划，然后依据成本决定走哪个执行计划。

这里的成本，包括 IO 成本和 CPU 成本：IO 成本，是从磁盘把数据加载到内存的成本。默认情况下，读取数据页的 IO 成本常数是 1（也就是读取 1 个页成本是 1）。CPU 成本，是检测数据是否满足条件和排序等 CPU 操作的成本。默认情况下，检测记录的成本是 0.2。

InnoDB一页16KB

要计算全表扫描的代价需要两个信息：聚簇索引占用的页面数，用来计算读取数据的 IO 成本；表中的记录数，用来计算搜索的 CPU 成本。

```mysql
SHOW TABLE STATUS LIKE 'person'

Rows = 100086 -> CPU 100086*0.2 = 20017
Data_Length = 4734976B -> 4734976/16KB = 289
Total = 20017 + 289 = 20306
```

我们也可以强制走某个索引，

```mysql
EXPLAIN SELECT * FROM person FORCE INDEX(name_score) WHERE NAME >'name84059' AND create_time>'2020-01-24 05:00:00' 
```

在 MySQL 5.6 及之后的版本中，我们可以使用 optimizer trace 功能查看优化器生成执行计划的整个过程

```mysql
SET optimizer_trace="enabled=on";
SELECT * FROM person WHERE NAME >'name84059' AND create_time>'2020-01-24 05:00:00';
SELECT * FROM information_schema.OPTIMIZER_TRACE;
SET optimizer_trace="enabled=off";
```

有关 optimizer trace 的更多信息，你可以参考[MySQL 的文档](https://dev.mysql.com/doc/dev/mysql-server/latest/)。

第五，排序索引失效

排序使用到索引，在执行计划中的体现就是 key 这一列。如果没有用到索引，会在 Extra 中看到 Using filesort，代表使用了内存或磁盘进行排序。而具体走内存还是磁盘，是由 sort_buffer_size 和排序数据大小决定的。

排序无法使用到索引的情况有：

1. 对于使用联合索引进行排序的场景，多个字段排序 ASC 和 DESC 混用；
2. a+b 作为联合索引，按照 a 范围查询后按照 b 排序；
3. 排序列涉及到的多个字段不属于同一个联合索引；
4. 排序列使用了表达式。

## 判等

### equals 跟 == 的区别

比较值的内容，除了基本类型只能使用 == 外，其他类型都需要使用 equals。

Integer例子

```java
Integer a = 127;
Integer b = 127;
a == b ? true

// 默认情况下会缓存[-128, 127]的数值，而 128 处于这个区间之外。可以设置 JVM 参数加上 -XX:AutoBoxCacheMax=1000
Integer c = 128;
Integer d = 128;
c == d ? false

Integer e = 127;
Integer f = new Integer(127);
e == f ? false

Integer g = new Integer(127);
Integer h = new Integer(127);
g == h ? false

// 我们把装箱的 Integer 和基本类型 int 比较，前者会先拆箱再比较，比较的肯定是数值而不是引用，因此返回 true。
Integer i = 128;
int j = 128;
i == j ? true
```

值得注意，枚举值我们经常使用Integer类型，这时候要注意是否会超过缓存127，

String例子

```java
String a = "1";
String b = "1";
a == b ? true

String c = new String("2");
String d = new String("2");
c == d ? false

String e = new String("3").intern();
String f = new String("3").intern();
e == f ? true

String g = new String("4");
String h = new String("4");
g.equals(h) ? true
```

首先要了解 Java 的**字符串常量池机制**。首先要明确的是其设计初衷是节省内存。当代码中出现双引号形式创建字符串对象时，JVM 会先对这个字符串进行检查，如果字符串常量池中存在相同内容的字符串对象的引用，则将这个引用返回；否则，创建新的字符串对象，然后将这个引用放入字符串常量池，并返回该引用。这种机制，就是字符串驻留或池化。

虽然使用 new 声明的字符串调用 intern 方法，也可以让字符串进行驻留，但在业务代码中滥用 intern，可能会产生性能问题

在启动程序时设置 JVM 参数 -XX:+PrintStringTableStatistic，程序退出时可以打印出字符串常量表的统计信息

可以看到，1000 万次 intern 操作耗时居然超过了 44 秒。

其实，原因在于字符串常量池是一个固定容量的 Map。如果容量太小（Number of buckets=60013）、字符串太多（1000 万个字符串），那么每一个桶中的字符串数量会非常多，所以搜索起来就很慢。输出结果中的 Average bucket size=167，代表了 Map 中桶的平均长度是 167。解决方式是，设置 JVM 参数 -XX:StringTableSize，指定更多的桶。设置 -XX:StringTableSize=10000000 后，重启应用：

没事别轻易用 intern，如果要用一定要注意控制驻留的字符串的数量，并留意常量表的各项指标。

### 实现equals不简单

```java

Object
public boolean equals(Object obj) {
    return (this == obj);
}

String
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

重写equals

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    PointRight that = (PointRight) o;
    return x == that.x && y == that.y;
}    
```

hashCode 和 equals 要配对实现

散列表需要使用 hashCode 来定位元素放到哪个桶。如果自定义对象没有实现自定义的 hashCode 方法，就会使用 Object 超类的默认实现，得到的两个 hashCode 是不同的，导致无法满足需求。

```java
class PointRight {
    private final int x;
    private final int y;
    private final String desc;
    ...
    @Override
    public boolean equals(Object o) {
        ...
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}
```

注意 compareTo 和 equals 的逻辑一致性，inarySearch 方法内部调用了元素的 compareTo 方法进行比较

对于自定义的类型，如果要实现 Comparable，请记得 equals、hashCode、compareTo 三者逻辑一致。

### Lombok坑

Lombok 的 @EqualsAndHashCode 注解实现 equals 和 hashCode 的时候，默认使用类型所有非 static、非 transient 的字段，且不考虑父类。如果希望改变这种默认行为，可以使用 @EqualsAndHashCode.Exclude 排除一些字段，并设置 callSuper = true 来让子类的 equals 和 hashCode 调用父类的相应方法

### 补充

建议使用[阿里的代码规则插件](https://github.com/alibaba/p3c)

equals比较的对象除了所谓的相等外，还有一个非常重要的因素，就是该对象的类加载器也必须是同一个，不然equals返回的肯定是false

instanceof进行类型检查规则是:你是该类或者是该类的子类； getClass获得类型信息采用来进行检查是否相等的操作是严格的判断。不会存在继承方面的考虑

HashSet本质上就是HashMap的key组成的不重复的元素集合，contains方法其实就是根据hashcode和equals去判断相等的 

TreeSet本质TreeMap的key组成的，数据结构是红黑树，是自带排序功能的，可以在放入元素的时候指定comparator（比较器），或者是放入的元素要实现Comparable接口后元素自己实现compareTo方法，contains方法是根据比较器或者compareTo去判断相等

## 数值计算

### 危险的浮点类型

Java采用[IEEE_754标准](https://en.wikipedia.org/wiki/IEEE_754) 实现浮点类型，对于计算机而言，0.1 无法精确表达，这是浮点数计算造成精度损失的根源。

使用 BigDecimal 表示和计算浮点数，且务必使用字符串的构造方法来初始化 BigDecimal：

```java
new BigDecimal("0.1").add(new BigDecimal("0.2"));
new BigDecimal("1.0").subtract(new BigDecimal("0.8"));
new BigDecimal("4.015").multiply(new BigDecimal("100"));
new BigDecimal("123.3").divide(new BigDecimal("100"));
```

### 浮点类型字符串格式化

这就是由精度问题和舍入方式共同导致的，double 和 float 的 3.35 其实相当于 3.350xxx 和 3.349xxx：

```
3.350000000000000088817841970012523233890533447265625
3.349999904632568359375
```

```java
double num1 = 3.35;
float num2 = 3.35f;
System.out.println(String.format("%.1f", num1));//四舍五入 3.4
System.out.println(String.format("%.1f", num2)); // 3.3

double num1 = 3.35;  
float num2 = 3.35f;  
DecimalFormat format = new DecimalFormat("#.##");  
format.setRoundingMode(RoundingMode.DOWN);  
System.out.println(format.format(num1)); // 3.35
format.setRoundingMode(RoundingMode.DOWN);  
System.out.println(format.format(num2)); // 3.34

BigDecimal num1 = new BigDecimal("3.35");
BigDecimal num2 = num1.setScale(1, BigDecimal.ROUND_DOWN);
System.out.println(num2); // 3.3
BigDecimal num3 = num1.setScale(1, BigDecimal.ROUND_HALF_UP);
System.out.println(num3); // 3.4
```

所以，浮点数的字符串格式化也要通过 BigDecimal

### equals数值判等不一定对

```java
new BigDecimal("1.0").equals(new BigDecimal("1")) // false
new BigDecimal("1.0").compareTo(new BigDecimal("1")) == 0 // true
```

BigDecimal 的 equals 方法的注释中说明了原因，equals 比较的是 BigDecimal 的 value 和 scale，1.0 的 scale 是 1，1 的 scale 是 0，所以结果一定是 false

```java
Set<BigDecimal> hashSet1 = new HashSet<>();  
hashSet1.add(new BigDecimal("1.0"));  
hashSet1.contains(new BigDecimal("1"));// false
  
Set<BigDecimal> hashSet2 = new HashSet<>();  
hashSet2.add(new BigDecimal("1.0").stripTrailingZeros());  hashSet2.contains(new BigDecimal("1.000").stripTrailingZeros());// true
  
Set<BigDecimal> treeSet = new TreeSet<>();  
treeSet.add(new BigDecimal("1.0"));  
treeSet.contains(new BigDecimal("1")); //true
```

TreeSet 不使用 hashCode 方法，也不使用 equals 比较元素，而是使用 compareTo 方法

### 数值溢出

```java
long l = Long.MAX_VALUE;  
System.out.println(l + 1);  
System.out.println(l + 1 == Long.MIN_VALUE); // true
```

显然这是发生了溢出，而且是默默地溢出，并没有任何异常

方法一，Math 类的 addExact、subtractExact 等 xxExact 方法进行数值运算，这些方法可以在数值溢出时主动抛出异常

```java
try {
    long l = Long.MAX_VALUE;
    System.out.println(Math.addExact(l, 1));
} catch (Exception ex) {
    ex.printStackTrace();
}
```

方法二，使用大数类 BigInteger。BigDecimal 是处理浮点数的专家，而 BigInteger 则是对大数进行科学计算的专家。

```java
BigInteger i = new BigInteger(String.valueOf(Long.MAX_VALUE));
System.out.println(i.add(BigInteger.ONE).toString());
try {
    long l = i.add(BigInteger.ONE).longValueExact();
} catch (Exception ex) {
    ex.printStackTrace();
}
```

### 补充

MySQL中，整数和浮点数的定义都是有多种类型，整数根据实际范围定义，浮点数语言指定整体长度和小数长度。浮点数类型包括单精度浮点数（float型）和双精度浮点数（double型）。定点数类型就是decimal型。定点数以字符串形式存储，因此，其精度比浮点数要高，而且浮点数会出现误差，这是浮点数一直存在的缺陷。如果要对数据的精度要求比较高，还是选择定点数decimal比较安全。

## Collection集合操作

### asList

第一个坑是，不能直接使用 Arrays.asList 来转换基本类型数组

```java
int[] arr = {1, 2, 3};  
List list = Arrays.asList(arr); // wrong

List list1 = Arrays.stream(arr1).boxed().collect(Collectors.toList()); // right

Integer[] arr2 = {1, 2, 3};
List list2 = Arrays.asList(arr2); // right
```

第二个坑，Arrays.asList 返回的 List 不支持增删操作

Arrays.asList 返回的 List 并不是我们期望的 java.util.ArrayList，而是 Arrays 的内部类 ArrayList。ArrayList 内部类继承自 AbstractList 类，并没有覆写父类的 add 方法，而父类中 add 方法的实现，就是抛出 UnsupportedOperationException

第三个坑，对原始数组的修改会影响到我们获得的那个 List

```java
String[] arr = {"1", "2", "3"};
List list = Arrays.asList(arr) // wrong
List list1 = new ArrayList(Arrays.asList(arr)); // right
arr[1] = "4";
```

### subList

第一个坑，可能导致OOM，和 Arrays.asList 的问题类似，List.subList 返回的子 List 不是一个普通的 ArrayList。这个子 List 可以认为是原始 List 的视图，会和原始 List 相互影响

```java

private static List<List<Integer>> data = new ArrayList<>();

private static void oom() {
    for (int i = 0; i < 1000; i++) {
        List<Integer> rawList = IntStream.rangeClosed(1, 100000).boxed().collect(Collectors.toList());
        data.add(rawList.subList(0, 1));
    }
}
```

出现 OOM 的原因是，循环中的 1000 个具有 10 万个元素的 List 始终得不到回收，因为它始终被 subList 方法返回的 List 强引用

互操作跟迭代问题

```java
List<Integer> list = IntStream.rangeClosed(1, 10).boxed().collect(Collectors.toList());
List<Integer> subList = list.subList(1, 4);
System.out.println(subList);
subList.remove(1);
System.out.println(list);
list.add(0);
try {
    subList.forEach(System.out::println);
} catch (Exception ex) {
    ex.printStackTrace();
}
```

第一，ArrayList 维护了一个叫作 modCount 的字段，表示集合结构性修改的次数。所谓结构性修改，指的是影响 List 大小的修改，所以 add 操作必然会改变 modCount 的值

第二，subList 方法可以看到，获得的 List 其实是内部类 SubList，并不是普通的 ArrayList，在初始化的时候传入了 this。

第三，这个 SubList 中的 parent 字段就是原始的 List。SubList 初始化的时候，并没有把原始 List 中的元素复制到独立的变量中保存。我们可以认为 SubList 是原始 List 的视图，并不是独立的 List。双方对元素的修改会相互影响，而且 SubList 强引用了原始的 List，所以大量保存这样的 SubList 会导致 OOM。

第四，遍历 SubList 的时候会先获得迭代器，比较原始 ArrayList modCount 的值和 SubList 当前 modCount 的值。获得了 SubList 后，我们为原始 List 新增了一个元素修改了其 modCount，所以判等失败抛出 ConcurrentModificationException 异常。

既然 SubList 相当于原始 List 的视图，那么避免相互影响的修复方式有两种

```java
//方式一：
List<Integer> subList = new ArrayList<>(list.subList(1, 4));

//方式二：
List<Integer> subList = list.stream().skip(1).limit(3).collect(Collectors.toList());
```

### 选择适合的数据结构

1. 平衡时间跟空间
2. 过于迷信教科书的大 O 时间复杂度。

## 补充

调用类型是 Integer 的 ArrayList 的 remove 方法删除元素，传入一个 Integer 包装类的数字和传入一个 int 基本类型的数字，结果一样吗

传 int 基本类型的 remove 方法是按索引值移除，返回移除的值；传 Integer 包装类的 remove 方法是按值移除，返回列表移除项目之前是否包含这个值（是否移除成功

循环遍历 List，调用 remove 方法删除元素，往往会遇到 ConcurrentModificationException，原因是什么，修复方式又是什么呢

原因是，remove 的时候会改变 modCount，通过迭代器遍历就会触发 ConcurrentModificationException

第一种，通过 ArrayList 的迭代器 remove。迭代器的 remove 方法会维护一个 expectedModCount，使其与 ArrayList 的 modCount 保持一致

第二种，直接使用 removeIf 方法，其内部使用了迭代器的 remove 方法

## 空值处理

### Java Null Exception

参数值是 Integer 等包装类型，使用时因为自动拆箱出现了空指针异常；

字符串比较出现空指针异常；

诸如 ConcurrentHashMap 这样的容器不支持 Key 和 Value 为 null，强行 put null 的 Key 或 Value 会出现空指针异常；

A 对象包含了 B，在通过 A 对象的字段获得 B 之后，没有对字段判空就级联调用 B 的方法出现空指针异常；

方法或远程服务返回的 List 不是空而是 null，没有进行判空就直接调用 List 的方法出现空指针异常。

建议使用阿里云的[Arthas](https://github.com/alibaba/arthas)分析

对于 Integer 的判空，可以使用 Optional.ofNullable 来构造一个 Optional，然后使用 orElse(0) 把 null 替换为默认值再进行 +1 操作。

对于 String 和字面量的比较，可以把字面量放在前面，比如"OK".equals(s)，这样即使 s 是 null 也不会出现空指针异常；

而对于两个可能为 null 的字符串变量的 equals 比较，可以使用 Objects.equals，它会做判空处理。对于 ConcurrentHashMap，既然其 Key 和 Value 都不支持 null，修复方式就是不要把 null 存进去。

HashMap 的 Key 和 Value 可以存入 null，而 ConcurrentHashMap 看似是 HashMap 的线程安全版本，却不支持 null 值的 Key 和 Value，这是容易产生误区的一个地方。

对于类似 fooService.getBarService().bar().equals(“OK”) 的级联调用，需要判空的地方有很多，包括 fooService、getBarService() 方法的返回值，以及 bar 方法返回的字符串。如果使用 if-else 来判空的话可能需要好几行代码，但使用 Optional 的话一行代码就够了。

对于 rightMethod 返回的 List，由于不能确认其是否为 null，所以在调用 size 方法获得列表大小之前，同样可以使用 Optional.ofNullable 包装一下返回值，然后通过.orElse(Collections.emptyList()) 实现在 List 为 null 的时候获得一个空的 List，最后再调用 size 方法。

### Mysql Null

MySQL 中 sum 函数没统计到任何记录时，会返回 null 而不是 0，可以使用 IFNULL 函数把 null 转换为 0；

MySQL 中 count 字段不统计 null 值，COUNT(*) 才是统计所有记录数量的正确方式。

MySQL 中使用诸如 =、<、> 这样的算数比较操作符比较 NULL 的结果总是 NULL，这种比较就显得没有任何意义，需要使用 IS NULL、IS NOT NULL 或 ISNULL() 函数来比较。

在MySQL的使用中，对于索引列，建议都设置为not null，因为如果有null的话，MySQL需要单独专门处理null值，会额外耗费性能

## 异常处理

第一，注意捕获和处理异常的最佳实践。首先，不应该用 AOP 对所有方法进行统一异常处理，异常要么不捕获不处理，要么根据不同的业务逻辑、不同的异常类型进行精细化、针对性处理；

其次，处理异常应该杜绝生吞，并确保异常栈信息得到保留；最后，如果需要重新抛出异常的话，请使用具有意义的异常类型和异常消息。

第二，务必小心 finally 代码块中资源回收逻辑，确保 finally 代码块不出现异常，内部把异常处理完毕，避免 finally 中的异常覆盖 try 中的异常；或者考虑使用 addSuppressed 方法把 finally 中的异常附加到 try 中的异常上，确保主异常信息不丢失。

此外，使用实现了 AutoCloseable 接口的资源，务必使用 try-with-resources 模式来使用资源，确保资源可以正确释放，也同时确保异常可以正确处理。

第三，虽然在统一的地方定义收口所有的业务异常是一个不错的实践，但务必确保异常是每次 new 出来的，而不能使用一个预先定义的 static 字段存放异常，否则可能会引起栈信息的错乱。

第四，确保正确处理了线程池中任务的异常，如果任务通过 execute 提交，那么出现异常会导致线程退出，大量的异常会导致线程重复创建引起性能问题，我们应该尽可能确保任务不出异常，同时设置默认的未捕获异常处理程序来兜底；如果任务通过 submit 提交意味着我们关心任务的执行结果，应该通过拿到的 Future 调用其 get 方法来获得任务运行结果和可能出现的异常，否则异常可能就被生吞了。

### 补充

关于在 finally 代码块中抛出异常的坑，如果在 finally 代码块中返回值，你觉得程序会以 try 或 catch 中返回值为准，还是以 finally 中的返回值为准呢

以 finally 中的返回值为准。从语义上来说，finally 是做方法收尾资源释放处理的，我们不建议在 finally 中有 return，这样逻辑会很混乱。这是因为，实现上 finally 中的代码块会被复制多份，分别放到 try 和 catch 调用 return 和 throw 异常之前，所以 finally 中如果有返回值，会覆盖 try 中的返回值。

对于手动抛出的异常，不建议直接使用 Exception 或 RuntimeException，通常建议复用 JDK 中的一些标准异常，比如IllegalArgumentException、IllegalStateException、UnsupportedOperationException。你能说说它们的适用场景，并列出更多常见的可重用标准异常吗？

我们先分别看看 IllegalArgumentException、IllegalStateException、UnsupportedOperationException 这三种异常的适用场景。

IllegalArgumentException：参数不合法异常，适用于传入的参数不符合方法要求的场景。

IllegalStateException：状态不合法异常，适用于状态机的状态的无效转换，当前逻辑的执行状态不适合进行相应操作等场景。

UnsupportedOperationException：操作不支持异常，适用于某个操作在实现或环境下不支持的场景。

还可以重用的异常有 IndexOutOfBoundsException、NullPointerException、ConcurrentModificationException 等。

## 日志处理

![](../media/java-common-mistakes/Pasted%20image%2020221117173553.png)

SLF4J 实现了三种功能：

一是提供了统一的日志门面 API，即图中紫色部分，实现了中立的日志记录 API。

二是桥接功能，即图中蓝色部分，用来把各种日志框架的 API（图中绿色部分）桥接到 SLF4J API。这样一来，即便你的程序中使用了各种日志 API 记录日志，最终都可以桥接到 SLF4J 门面 API。

三是适配功能，即图中红色部分，可以实现 SLF4J API 和实际日志框架（图中灰色部分）的绑定。SLF4J 只是日志标准，我们还是需要一个实际的日志框架。日志框架本身没有实现 SLF4J API，所以需要有一个前置转换。

Logback 就是按照 SLF4J API 标准实现的，因此不需要绑定模块做转换。需要理清楚的是，虽然我们可以使用 log4j-over-slf4j 来实现 Log4j 桥接到 SLF4J，也可以使用 slf4j-log4j12 实现 SLF4J 适配到 Log4j，也把它们画到了一列，但是它不能同时使用它们，否则就会产生死循环。jcl 和 jul 也是同样的道理。

推荐使用Lockback

Spring Boot 是目前最流行的 Java 框架，它的日志框架也用的是 Logback。那，为什么我们没有手动引入 Logback 的包，就可以直接使用 Logback 了呢？查看 Spring Boot 的 Maven 依赖树，可以发现 spring-boot-starter 模块依赖了 spring-boot-starter-logging 模块，而 spring-boot-starter-logging 模块又帮我们自动引入了 logback-classic（包含了 SLF4J 和 Logback 日志框架）和 SLF4J 的一些适配器。其中，log4j-to-slf4j 用于实现 Log4j2 API 到 SLF4J 的桥接，jul-to-slf4j 则是实现 java.util.logging API 到 SLF4J 的桥接：

![](../media/java-common-mistakes/Pasted%20image%2020221117173844.png)

自行查阅[boot-features-logback-extensions](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-logback-extensions)

## 文件操作

参考 [Oracle I/O](https://docs.oracle.com/javase/tutorial/essential/io/)

### 读写字符编码一致

查看[JDK 文档](https://docs.oracle.com/javase/8/docs/api/java/io/FileReader.html)可以发现，FileReader 是以当前机器的默认字符集来读取文件的，如果希望指定字符集的话，需要直接使用 InputStreamReader 和 FileInputStream。

也可以使用 JDK1.7 推出的 Files 类的 readAllLines 方法，可以很方便地用一行代码完成文件内容读取，这种可能导致OOM

打开 readAllLines 方法的源码可以看到，readAllLines 读取文件所有内容后，放到一个 List 中返回，如果内存无法容纳这个 List，就会 OOM

### 使用 Files 类静态方法进行文件操作注意释放文件句柄

其实，在JDK 文档中有提到，注意使用 try-with-resources 方式来配合，确保流的 close 方法可以调用释放资源。

```java
LongAdder longAdder = new LongAdder();
IntStream.rangeClosed(1, 1000000).forEach(i -> {
    try (Stream<String> lines = Files.lines(Paths.get("demo.txt"))) {
        lines.forEach(line -> longAdder.increment());
    } catch (IOException e) {
        e.printStackTrace();
    }
});
log.info("total : {}", longAdder.longValue());
```

查看 lines 方法源码可以发现，Stream 的 close 注册了一个回调，来关闭 BufferedReader 进行资源释放：

### 设置合适的Buffer

在进行文件 IO 处理的时候，使用合适的缓冲区可以明显提高性能

对于类似的文件复制操作，如果希望有更高性能，可以使用 FileChannel 的 transfreTo 方法进行流的复制。在一些操作系统（比如高版本的 Linux 和 UNIX）上可以实现 DMA（直接内存访问），也就是数据从磁盘经过总线直接发送到目标文件，无需经过内存和 CPU 进行数据中转：

```java
private static void fileChannelOperation() throws IOException {
    FileChannel in = FileChannel.open(Paths.get("src.txt"), StandardOpenOption.READ);
    FileChannel out = FileChannel.open(Paths.get("dest.txt"), CREATE, WRITE);
    in.transferTo(0, in.size(), out);
}
```

自行查阅[zero-copy](https://developer.ibm.com/articles/j-zerocopy/)

## 序列化

第一，要确保序列化和反序列化算法的一致性。因为，不同序列化算法输出必定不同，要正确处理序列化后的数据就要使用相同的反序列化算法。

第二，Jackson 有大量的序列化和反序列化特性，可以用来微调序列化和反序列化的细节。需要注意的是，如果自定义 ObjectMapper 的 Bean，小心不要和 Spring Boot 自动配置的 Bean 冲突。

第三，在调试序列化反序列化问题时，我们一定要捋清楚三点：是哪个组件在做序列化反序列化、整个过程有几次序列化反序列化，以及目前到底是序列化还是反序列化。

第四，对于反序列化默认情况下，框架调用的是无参构造方法，如果要调用自定义的有参构造方法，那么需要告知框架如何调用。更合理的方式是，对于需要序列化的 POJO 考虑尽量不要自定义构造方法。

第五，枚举不建议定义在 DTO 中跨服务传输，因为会有版本问题，并且涉及序列化反序列化时会很复杂，容易出错。因此，我只建议在程序内部使用枚举。

最后还有一点需要注意，如果需要跨平台使用序列化的数据，那么除了两端使用的算法要一致外，还可能会遇到不同语言对数据类型的兼容问题。这，也是经常踩坑的一个地方。如果你有相关需求，可以多做实验、多测试。

## 使用Java8的日期时间类

![](../media/java-common-mistakes/Pasted%20image%2020221117182808.png)

## OOM

### 同一对象多分拷贝

在进行容量评估时，我们不能认为一份数据在程序内存中也是一份。100M 的数据加载到程序内存中，变为 Java 的数据结构就已经占用了 200M 堆内存；这些数据经过 JDBC、MyBatis 等框架其实是加载了 2 份，然后领域模型、DTO 再进行转换可能又加载了 2 次；最终，占用的内存达到了 200M*4=800M。

所以涉及到数据库查询的时候，要避免无参数的全表扫描，因为我们无法预计表数据到底多多大。

### WeakHashMap 不等于不会 OOM

WeakHashMap 的特点是 Key 在哈希表内部是弱引用的，当没有强引用指向这个 Key 之后，Entry 会被 GC，即使我们无限往 WeakHashMap 加入数据，只要 Key 不再使用，也就不会 OOM。

说到了强引用和弱引用，我先和你回顾下 Java 中引用类型和垃圾回收的关系：
1. 垃圾回收器不会回收有强引用的对象；
2. 在内存充足时，垃圾回收器不会回收具有软引用的对象；
3. 垃圾回收器只要扫描到了具有弱引用的对象就会回收，WeakHashMap 就是利用了这个特点。

WeakHashMap 的 Key 虽然是弱引用，但是其 Value 却持有 Key 中对象的强引用，Value 被 Entry 引用，Entry 被 WeakHashMap 引用，最终导致 Key 无法回收。解决方案就是让 Value 变为弱引用，使用 WeakReference 来包装 UserProfile 即可

```java
private Map<User, WeakReference<UserProfile>> cache2 = new WeakHashMap<>();

User user = new User(userName + i);
        //这次，我们使用弱引用来包装UserProfile
        cache2.put(user, new WeakReference(new UserProfile(user, "location" + i)));
```

当然，还有一种办法就是，让 Value 也就是 UserProfile 不再引用 Key，而是重新 new 出一个新的 User 对象赋值给 UserProfile：

```java
User user = new User(userName + i); 
cache.put(user, new UserProfile(new User(user.getName()), "location" + i));
```

### Tomcat参数配置导致

在[Tomcat 文档](https://tomcat.apache.org/tomcat-8.0-doc/config/http.html)中有提到，这个 Socket 的读缓冲，也就是 readBuffer 默认是 8192 字节。显然，问题出在了 headerBufferSize 上：

```java
inputBuffer = new Http11InputBuffer(request, protocol.getMaxHttpHeaderSize(),
        protocol.getRejectIllegalHeaderName(), httpParser);
```

发行配置文件有如下配置，

```properties
server.max-http-header-size=10000000
```

发现，这位同学出现`java.lang.IllegalArgumentException: Request header is too large`异常，然后网上搜索了下解决方案，

所以一定要根据实际需求来修改参数配置，可以考虑预留 2 到 5 倍的量。容量类的参数背后往往代表了资源，设置超大的参数就有可能占用不必要的资源，在并发量大的时候因为资源大量分配导致 OOM

### 补充

我建议你为生产系统的程序配置 JVM 参数启用详细的 GC 日志，方便观察垃圾收集器的行为，并开启 HeapDumpOnOutOfMemoryError，以便在出现 OOM 时能自动 Dump 留下第一问题现场。对于 JDK8，你可以这么设置 

```java
XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=. -XX:+PrintGCDateStamps -XX:+PrintGCDetails -Xloggc:gc.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M
```

## 反射，注解，泛型遇到OOP

### 反射调用不是根据参数决定

反射的功能包括，在运行时动态获取类和类成员定义，以及动态读取属性调用方法。也就是说，针对类动态调用方法，不管类中字段和方法怎么变动，我们都可以用相同的规则来读取信息和执行方法

反射的起点是 Class 类，Class 类提供了各种方法帮我们查询它的信息。你可以通过这个[文档](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html)，了解每一个方法的作用。

反射调用由方法签名决定，跟传入参数的类型无关，

```java

@Slf4j
public class ReflectionIssueApplication {
  private void age(int age) {
      log.info("int age = {}", age);
  }

  private void age(Integer age) {
      log.info("Integer age = {}", age);
  }
}

getClass().getDeclaredMethod("age", Integer.TYPE).invoke(this, Integer.valueOf("36")); // int age

getClass().getDeclaredMethod("age", Integer.class).invoke(this, Integer.valueOf("36")); // Integer age
```

### 泛型类型擦除多出桥接方法

Java 的泛型类型在编译后擦除为 Object。虽然子类指定了父类泛型 T 类型是 String，但编译后 T 会被擦除成为 Object，所以父类 setValue 方法的入参是 Object，value 也是 Object。如果子类 Child2 的 setValue 方法要覆盖父类的 setValue 方法，那入参也必须是 Object。所以，编译器会为我们生成一个所谓的 bridge 桥接方法，你可以使用 javap 命令来反编译编译后的 Child2 类的 class 字节码：

```
javap -c /Users/zhuye/Documents/common-mistakes/target/classes/org/geekbang/time/commonmistakes/advancedfeatures/demo3/Child2.class
Compiled from "GenericAndInheritanceApplication.java"
class org.geekbang.time.commonmistakes.advancedfeatures.demo3.Child2 extends org.geekbang.time.commonmistakes.advancedfeatures.demo3.Parent<java.lang.String> {
  org.geekbang.time.commonmistakes.advancedfeatures.demo3.Child2();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method org/geekbang/time/commonmistakes/advancedfeatures/demo3/Parent."<init>":()V
       4: return


  public void setValue(java.lang.String);
    Code:
       0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #3                  // String Child2.setValue called
       5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: aload_0
       9: aload_1
      10: invokespecial #5                  // Method org/geekbang/time/commonmistakes/advancedfeatures/demo3/Parent.setValue:(Ljava/lang/Object;)V
      13: return


  public void setValue(java.lang.Object);
    Code:
       0: aload_0
       1: aload_1
       2: checkcast     #6                  // class java/lang/String
       5: invokevirtual #7                  // Method setValue:(Ljava/lang/String;)V
       8: return
}
```

通过 getDeclaredMethods 方法获取到所有方法后，必须同时根据方法名 setValue 和非 isBridge 两个条件过滤，才能实现唯一过滤；使用 Stream 时，如果希望只匹配 0 或 1 项的话，可以考虑配合 ifPresent 来使用 findFirst 方法。

```java
Arrays.stream(child2.getClass().getDeclaredMethods())
        .filter(method -> method.getName().equals("setValue") && !method.isBridge())
        .findFirst().ifPresent(method -> {
    try {
        method.invoke(chi2, "test");
    } catch (Exception e) {
        e.printStackTrace();
    }
});
```

getMethods 和 getDeclaredMethods 是有区别的，前者可以查询到父类方法，后者只能查询到当前类。反射进行方法调用要注意过滤桥接方法。

### 注解可以继承吗

如果你再仔细阅读一下[@Inherited](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Inherited.html) 的文档就会发现，@Inherited 只能实现类上的注解继承。要想实现方法上注解的继承，你可以通过反射在继承链上找到方法上的注解。但，这样实现起来很繁琐，而且需要考虑桥接方法。

好在 Spring 提供了 AnnotatedElementUtils 类，来方便我们处理注解的继承问题。这个类的 findMergedAnnotation 工具方法，可以帮助我们找出父类和接口、父类方法和接口方法上的注解，并可以处理桥接方法，实现一键找到继承链的注解：

```java
Child child = new Child();
log.info("ChildClass:{}", getAnnotationValue(AnnotatedElementUtils.findMergedAnnotation(child.getClass(), MyAnnotation.class)));
log.info("ChildMethod:{}", getAnnotationValue(AnnotatedElementUtils.findMergedAnnotation(child.getClass().getMethod("foo"), MyAnnotation.class)));
```

并注意各种 getXXX 方法和 findXXX 方法的区别，详情查看[Spring 的文档](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/annotation/AnnotatedElementUtils.html)。

编译后的代码和原始代码并不完全一致，编译器可能会做一些优化，加上还有诸如 AspectJ 等编译时增强框架，使用反射动态获取类型的元数据可能会和我们编写的源码有差异，这点需要特别注意。你可以在反射中多写断言，遇到非预期的情况直接抛异常，避免通过反射实现的业务逻辑不符合预期。

### 补充

1、泛型类型擦除后会生成一个 bridge 方法，这个方法同时又是 synthetic 方法。除了泛型类型擦除，你知道还有什么情况编译器会生成 synthetic 方法吗？

内部类会用到，类在JVM是最顶级的，即使是内部类，编译以后，都会存在外部类$1这样的class文件；外部类是能完全访问内部的方法的，即使是private，但编译后编程2个文件了，怎么访问的，就是通过synthetic标识位实现的。 在额外分享两篇R大关于逃逸分析的文章，里面涉及到了synthetic。
http://mail.openjdk.java.net/pipermail/hotspot-compiler-dev/2016-September/024535.html
http://mail.openjdk.java.net/pipermail/hotspot-compiler-dev/2016-September/024535.html

2、关于注解继承问题，你觉得 Spring 的常用注解 @Service、@Controller 是否支持继承呢？

Spring 的常用注解 @Service、@Controller，不支持继承。这些注解只支持放到具体的（非接口非抽象）顶层类上（来让它们成为 Bean），如果支持继承会非常不灵活而且容易出错。

## IOC & AOP

Spring Core 中最核心的就是 IoC（控制反转）和 AOP（面向切面编程）。

IoC，其实就是一种设计思想。使用 Spring 来实现 IoC，意味着将你设计好的对象交给 Spring 容器控制，而不是直接在对象内部控制。那，为什么要让容器来管理对象呢？或许你能想到的是，使用 IoC 方便、可以实现解耦。但在我看来，相比于这两个原因，更重要的是 IoC 带来了更多的可能性。

如果以容器为依托来管理所有的框架、业务对象，我们不仅可以无侵入地调整对象的关系，还可以无侵入地随时调整对象的属性，甚至是实现对象的替换。这就使得框架开发者在程序背后实现一些扩展不再是问题，带来的可能性是无限的。比如我们要监控的对象如果是 Bean，实现就会非常简单。所以，这套容器体系，不仅被 Spring Core 和 Spring Boot 大量依赖，还实现了一些外部框架和 Spring 的无缝整合。

AOP，体现了松耦合、高内聚的精髓，在切面集中实现横切关注点（缓存、权限、日志等），然后通过切点配置把代码注入合适的地方。切面、切点、增强、连接点，是 AOP 中非常重要的概念，也是我们这两讲会大量提及的。

为方便理解，我们把 Spring AOP 技术看作为蛋糕做奶油夹层的工序。如果我们希望找到一个合适的地方把奶油注入蛋糕胚子中，那应该如何指导工人完成操作呢？

![](../media/java-common-mistakes/Pasted%20image%2020221118111703.png)

1. 首先，我们要提醒他，只能往蛋糕胚子里面加奶油，而不能上面或下面加奶油。这就是连接点（Join point），对于 Spring AOP 来说，连接点就是方法执行。
2. 然后，我们要告诉他，在什么点切开蛋糕加奶油。比如，可以在蛋糕坯子中间加入一层奶油，在中间切一次；也可以在中间加两层奶油，在 1/3 和 2/3 的地方切两次。这就是切点（Pointcut），Spring AOP 中默认使用 AspectJ 查询表达式，通过在连接点运行查询表达式来匹配切入点。
3. 接下来也是最重要的，我们要告诉他，切开蛋糕后要做什么，也就是加入奶油。这就是增强（Advice），也叫作通知，定义了切入切点后增强的方式，包括前、后、环绕等。Spring AOP 中，把增强定义为拦截器。
4. 最后，我们要告诉他，找到蛋糕胚子中要加奶油的地方并加入奶油。为蛋糕做奶油夹层的操作，对 Spring AOP 来说就是切面（Aspect），也叫作方面。切面 = 切点 + 增强。

### 单例的 Bean 如何注入 Prototype 的 Bean

Spring 创建的 Bean 默认是单例的，但当 Bean 遇到继承的时候，可能会忽略这一点，开发基类的架构师将基类设计为有状态的，但并不知道子类是怎么使用基类的；而开发子类的同学，没多想就直接标记了 @Service，让类成为了 Bean，通过 @Autowired 注解来注入这个服务。但这样设置后，有状态的基类就可能产生内存泄露或线程安全问题。

在为类标记上 @Service 注解把类型交由容器管理前，首先评估一下类是否有状态，然后为 Bean 设置合适的 Scope

```java
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
```

但是还是内存泄漏，因为Controller 标记了 @RestController 注解，而 @RestController 注解 =@Controller 注解 +@ResponseBody 注解，又因为 @Controller 标记了 @Component 元注解，所以 @RestController 注解其实也是一个 Spring Bean

```java
//@RestController注解=@Controller注解+@ResponseBody注解@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {}

//@Controller又标记了@Component元注解
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {}
```

Bean 默认是单例的，所以单例的 Controller 注入的 Service 也是一次性创建的，即使 Service 本身标识了 prototype 的范围也没用。

修复方式是，让 Service 以代理方式注入。这样虽然 Controller 本身是单例的，但每次都能从代理获取 Service。这样一来，prototype 范围的配置才能真正生效：

```java
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
```

当然，如果不希望走代理的话还有一种方式是，每次直接从 ApplicationContext 中获取 Bean：

```java
@Autowired  
List<SayService> sayServiceList;
@Autowired
private ApplicationContext applicationContext;
@GetMapping("test2")
public void test2() {
applicationContext.getBeansOfType(SayService.class).values().forEach(SayService::say);
}
```

如果细心的话，你可以发现另一个潜在的问题。这里 Spring 注入的 SayService 的 List，第一个元素是 SayBye，第二个元素是 SayHello。但，我们更希望的是先执行 Hello 再执行 Bye，所以注入一个 List Bean 时，需要进一步考虑 Bean 的顺序或者说优先级。大多数情况下顺序并不是那么重要，但对于 AOP，顺序可能会引发致命问题

### 监控切面顺序

如果一组相同类型的 Bean 是有顺序的，需要明确使用 @Order 注解来设置顺序

我们知道，切面本身是一个 Bean，Spring 对不同切面增强的执行顺序是由 Bean 优先级决定的，具体规则是：

1. 入操作（Around（连接点执行前）、Before），切面优先级越高，越先执行。一个切面的入操作执行完，才轮到下一切面，所有切面入操作执行完，才开始执行连接点（方法）。
2. 出操作（Around（连接点执行后）、After、AfterReturning、AfterThrowing），切面优先级越低，越先执行。一个切面的出操作执行完，才轮到下一切面，直到返回到调用点。
3. 同一切面的 Around 比 After、Before 先执行。
4. 对于 Bean 可以通过 @Order 注解来设置优先级，查看 @Order 注解和 Ordered 接口源码可以发现，默认情况下 Bean 的优先级为最低优先级，其值是 Integer 的最大值。其实，值越大优先级反而越低，这点比较反直觉

### 补充

1、除了通过 @Autowired 注入 Bean 外，还可以使用 @Inject 或 @Resource 来注入 Bean。你知道这三种方式的区别是什么吗？

@Autowired，是 Spring 的注解，优先按照类型注入。当无法确定具体注入类型的时候，可以通过 @Qualifier 注解指定 Bean 名称。

@Inject：是 JSR330 规范的实现，也是根据类型进行自动装配的，这一点和 @Autowired 类似。如果需要按名称进行装配，则需要配合使用 @Named。@Autowired 和 @Inject 的区别在于，前者可以使用 required=false 允许注入 null，后者允许注入一个 Provider 实现延迟注入。

@Resource：JSR250 规范的实现，如果不指定 name 优先根据名称进行匹配（然后才是类型），如果指定 name 则仅根据名称匹配。

1、当 Bean 产生循环依赖时，比如 BeanA 的构造方法依赖 BeanB 作为成员需要注入，BeanB 也依赖 BeanA，你觉得会出现什么问题呢？又有哪些解决方式呢？

Bean 产生循环依赖，主要包括两种情况：

一种是注入属性或字段涉及循环依赖，Spring 内部通过三个 Map 的方式解决了这个问题，不会出错，可以参考这篇[文章](https://cloud.tencent.com/developer/article/1497692)

另一种是构造方法注入涉及循环依赖，这种解决方法一般为，改为属性或字段注入；使用 @Lazy 延迟注入。比如如下代码：

```java
@Component
public class TestC {
    @Getter
    private TestD testD;

    @Autowired
    public TestC(@Lazy TestD testD) {
        this.testD = testD;
    }
}
```

其实，这种 @Lazy 方式注入的就不是实际的类型了，而是代理类，获取的时候通过代理去拿值（实例化）。所以，它可以解决循环依赖无法实例化的问题

## Spring其他坑点

### 代理问题

Spring Boot 2.x 默认使用 CGLIB 的方式，但通过继承实现代理有个问题是，无法继承 final 的类。因为，ApacheHttpClient 类就是定义为了 final：

```java
public final class ApacheHttpClient implements Client {
```

为解决这个问题，我们把配置参数 proxy-target-class 的值修改为 false，以切换到使用 JDK 动态代理的方式：

```java
spring.aop.proxy-target-class=false
```

### 配置优先级

要想查询 Spring 中所有的配置，我们需要以环境 Environment 接口为入口。接下来，我就与你说说 Spring 通过环境 Environment 抽象出的 Property 和 Profile：

1. 针对 Property，又抽象出各种 PropertySource 类代表配置源。一个环境下可能有多个配置源，每个配置源中有诸多配置项。在查询配置信息时，需要按照配置源优先级进行查询。
2. Profile 定义了场景的概念。通常，我们会定义类似 dev、test、stage 和 prod 等环境作为不同的 Profile，用于按照场景对 Bean 进行逻辑归属。同时，Profile 和配置文件也有关系，每个环境都有独立的配置文件，但我们只会激活某一个环境来生效特定环境的配置文件。

![](../media/java-common-mistakes/Pasted%20image%2020221118114505.png)

接下来，我们重点看看 Property 的查询过程。对于非 Web 应用，Spring 对于 Environment 接口的实现是 StandardEnvironment 类。我们通过 Spring 注入 StandardEnvironment 后循环 getPropertySources 获得的 PropertySource，来查询所有的 PropertySource 中 key 是 user.name 或 management.server.port 的属性值；然后遍历 getPropertySources 方法，获得所有配置源并打印出来：

```java

@Autowired
private StandardEnvironment env;
@PostConstruct
public void init(){
    Arrays.asList("user.name", "management.server.port").forEach(key -> {
         env.getPropertySources().forEach(propertySource -> {
                    if (propertySource.containsProperty(key)) {
                        log.info("{} -> {} 实际取值：{}", propertySource, propertySource.getProperty(key), env.getProperty(key));
                    }
                });
    });

    System.out.println("配置优先级：");
    env.getPropertySources().stream().forEach(System.out::println);
}
```

![](../media/java-common-mistakes/Pasted%20image%2020221118114928.png)

## 代码复用

### 模板设计模式

有多个并行的类实现相似的代码逻辑。我们可以考虑提取相同逻辑在父类中实现，差异逻辑通过抽象方法留给子类实现。使用类似的模板方法把相同的流程和逻辑固定成模板，保留差异的同时尽可能避免代码重复。同时，可以使用 Spring 的 IoC 特性注入相应的子类，来避免实例化子类时的大量 if…else 代码。

工厂模式 + 模板方法模式，不仅消除了重复代码，还避免了修改既有代码的风险

个人观点，不要盲信什么设计模式，代码到一定程度考虑封装，其实很多设计模式，只是名词不懂，实际上遇到的时候也知道如果封装，

### 利用注解 + 反射消除重复代码

使用硬编码的方式重复实现相同的数据处理算法。我们可以考虑把规则转换为自定义注解，作为元数据对类或对字段、方法进行描述，然后通过反射动态读取这些元数据、字段或调用方法，实现规则参数和规则定义的分离。也就是说，把变化的部分也就是规则的参数放入注解，规则的定义统一处理。

### 属性拷贝工具消除重复代码

```java
ComplicatedOrderDTO orderDTO = new ComplicatedOrderDTO();
ComplicatedOrderDO orderDO = new ComplicatedOrderDO();
BeanUtils.copyProperties(orderDTO, orderDO, "id");
return orderDO;
```

不过，BeanUtils不推荐使用，这个工具是在运行时转换的，遇到同名不同类型的字段不会转换，而且没有错误提示，可能会有坑，推荐使用mapstruct，这个是在编译器生成转换代码，对于普通类型会自动转换（如int和String），对于不能自动转换的会有错误提示，且能看到生成的代码

最后，我们应该把代码重复度作为评估一个项目质量的重要指标，如果一个项目几乎没有任何重复代码，那么它内部的抽象一定是非常好的。在做项目重构的时候，你也可以以消除重复为第一目标去考虑实现。

## 生产就绪

第一，提供健康检测接口。传统采用 ping 的方式对应用进行探活检测并不准确。有的时候，应用的关键内部或外部依赖已经离线，导致其根本无法正常工作，但其对外的 Web 端口或管理端口是可以 ping 通的。我们应该提供一个专有的监控检测接口，并尽可能触达一些内部组件。

第二，暴露应用内部信息。应用内部诸如线程池、内存队列等组件，往往在应用内部扮演了重要的角色，如果应用或应用框架可以对外暴露这些重要信息，并加以监控，那么就有可能在诸如 OOM 等重大问题暴露之前发现蛛丝马迹，避免出现更大的问题。

第三，建立应用指标 Metrics 监控。Metrics 可以翻译为度量或者指标，指的是对于一些关键信息以可聚合的、数值的形式做定期统计，并绘制出各种趋势图表。这里的指标监控，包括两个方面：一是，应用内部重要组件的指标监控，比如 JVM 的一些指标、接口的 QPS 等；二是，应用的业务数据的监控，比如电商订单量、游戏在线人数

## 新版本特性

比如函数式编程等，流处理([analyze-java-stream-operations](https://www.jetbrains.com/help/idea/analyze-java-stream-operations.html))等，

函数式接口是一种只有单一抽象方法的接口，使用 @FunctionalInterface 来描述，可以隐式地转换成 Lambda 表达式。使用 Lambda 表达式来实现函数式接口，不需要提供类名和方法定义，通过一行代码提供函数式接口的实例，就可以让函数成为程序中的头等公民，可以像普通数据一样作为参数传递，而不是作为一个固定的类中的固定方法。

## 排查工具

### JDK自带工具

![](../media/java-common-mistakes/Pasted%20image%2020221118171943.png)

### Jvm工具

[VisualVM](https://visualvm.github.io/download.html) 
[Arthas](https://arthas.aliyun.com/)
[Bistoury](https://github.com/qunarcorp/bistoury)
[Fastthread](https://fastthread.io/)
[MAT](https://www.eclipse.org/mat/)

我们再来看看快照。这里的“快照”是指，应用进程在某一时刻的快照。通常情况下，我们会为生产环境的 Java 应用设置 -XX:+HeapDumpOnOutOfMemoryError 和 -XX:HeapDumpPath=…这 2 个 JVM 参数，用于在出现 OOM 时保留堆快照。这个课程中，我们也多次使用 MAT 工具来分析堆快照。

### 压测工具

[ab](https://httpd.apache.org/)
[wrk](https://github.com/wg/wrk)
[jmeter](https://jmeter.apache.org/)

### 监控

[Prometheus](https://prometheus.io/docs/instrumenting/exporters/)
[micrometer](https://micrometer.io/)

### Linux工具

主机层面的问题，可以使用工具排查：
1. CPU 相关问题，可以使用 top、vmstat、pidstat、ps 等工具排查；
2. 内存相关问题，可以使用 free、top、ps、vmstat、cachestat、sar 等工具排查；
3. IO 相关问题，可以使用 lsof、iostat、pidstat、sar、iotop、df、du 等工具排查；
4. 网络相关问题，可以使用 ifconfig、ip、nslookup、dig、ping、tcpdump、iptables 等工具排查。

### 网络协议

[wireshark](https://www.wireshark.org/)

















































