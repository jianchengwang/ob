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

![[Pasted image 20221117114603.png]]

页目录通过槽把记录分成不同的小组，每个小组有若干条记录。如图所示，记录中最前面的小方块中的数字，代表的是当前分组的记录条数，最小和最大的槽指向 2 个特殊的伪记录。有了槽之后，我们按照主键搜索页中记录时，就可以采用二分法快速搜索，无需从最小记录开始遍历整个页中的记录链表。

举一个例子，如果要搜索主键（PK）=15 的记录：先二分得出槽中间位是 (0+6)/2=3，看到其指向的记录是 12＜15，所以需要从 #3 槽后继续搜索记录；再使用二分搜索出 #3 槽和 #6 槽的中间位是 (3+6)/2=4.5 取整 4，#4 槽对应的记录是 16＞15，所以记录一定在 #4 槽中；再从 #3 槽指向的 12 号记录开始向下搜索 3 次，定位到 15 号记录。理解了 InnoDB 存储数据的原理后，我们就可以继续学习 MySQL 索引相关的原理和坑了。

### 聚簇索引和二级索引

InnoDB 引入了 B+ 树，如下所示

![[Pasted image 20221117114948.png]]

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

![[Pasted image 20221117173553.png]]

SLF4J 实现了三种功能：

一是提供了统一的日志门面 API，即图中紫色部分，实现了中立的日志记录 API。

二是桥接功能，即图中蓝色部分，用来把各种日志框架的 API（图中绿色部分）桥接到 SLF4J API。这样一来，即便你的程序中使用了各种日志 API 记录日志，最终都可以桥接到 SLF4J 门面 API。

三是适配功能，即图中红色部分，可以实现 SLF4J API 和实际日志框架（图中灰色部分）的绑定。SLF4J 只是日志标准，我们还是需要一个实际的日志框架。日志框架本身没有实现 SLF4J API，所以需要有一个前置转换。

Logback 就是按照 SLF4J API 标准实现的，因此不需要绑定模块做转换。需要理清楚的是，虽然我们可以使用 log4j-over-slf4j 来实现 Log4j 桥接到 SLF4J，也可以使用 slf4j-log4j12 实现 SLF4J 适配到 Log4j，也把它们画到了一列，但是它不能同时使用它们，否则就会产生死循环。jcl 和 jul 也是同样的道理。

推荐使用Lockback

Spring Boot 是目前最流行的 Java 框架，它的日志框架也用的是 Logback。那，为什么我们没有手动引入 Logback 的包，就可以直接使用 Logback 了呢？查看 Spring Boot 的 Maven 依赖树，可以发现 spring-boot-starter 模块依赖了 spring-boot-starter-logging 模块，而 spring-boot-starter-logging 模块又帮我们自动引入了 logback-classic（包含了 SLF4J 和 Logback 日志框架）和 SLF4J 的一些适配器。其中，log4j-to-slf4j 用于实现 Log4j2 API 到 SLF4J 的桥接，jul-to-slf4j 则是实现 java.util.logging API 到 SLF4J 的桥接：

![[Pasted image 20221117173844.png]]

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

### 使用Java8的日期时间类

![[Pasted image 20221117182808.png]]










































