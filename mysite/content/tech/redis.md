---
title: redis
slug: redis
author: [jianchengwang]
date: 2020-03-10
excerpt: "Redis是一个被数百万开发人员用作数据库、缓存、流引擎和消息代理的开源内存数据存储。"
draft: false
tags: [middleware]
---

Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, and message broker. Redis provides data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions, and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster.

官网的介绍，直接黏贴过来的，本文为个人学习笔记，方便后面查阅，建议感兴趣的直接看官网，[redis.io](https://redis.io/)

## Install

这里为了方便直接使用docker镜像，也可以下载源码，不过用底层用c语言编写的，需要安装gcc等，make一下，

```yaml
version: '3'
services:
  redis:
    restart: always
    image: redis:6.0.9
    container_name: redis
    command: redis-server /usr/local/etc/redis/redis.conf 
    ports:
      - 16379:6379
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/data
      - ./conf/redis.conf:/usr/local/etc/redis/redis.conf
```

```shell
docker-compose up -d
docker-compose exec redis bash
redis-cli -p 6379
AUTH requirepass # 输入密码，如果需要
CONFIG get requirepass # 获取配置信息
SELECT 0 # 默认有16个库
KEYS * # 查看已有的key，支持通配符
KEYS 1?*
FLUSHDB # 删除当前库
FLUSHALL # 删除所有库
```

Redis keys are binary safe，key跟value都有大小限制，不能超过**512M**，

Redis 配置或者指令，大小写不敏感，但是key大小写敏感，为了区分操作，所以下列演示的命令，都采用大写的指令

Redis是单线程的，所有的操作都是顺序执行的，但是由于读写惭怍等待用户输入或者输出都是阻塞的，因此很多IO操作在一般情况下都不能直接返回，而某个IO阻塞会导致整个进行无法对其他客户端提供服务，因此redis使用IO多路复用来解决这个问题，底层默认基于操作系统的**epoll**实现。

**epoll 有诸多优点**：

1. epoll 没有最大并发连接的限制，上限是最大可以打开文件的数目，这个数字一般远大于 2048, 一般来说这个数目和系统内存关系很大 ，具体数目可以 cat /proc/sys/fs/file-max 察看。
2. 效率提升， Epoll 最大的优点就在于它只管你“活跃”的连接 ，而跟连接总数无关，因此 IO 效率不随 FD 数目增加而线性下降，在实际的网络环境中， Epoll 的效率就会远远高于 select 和 poll 。
3. 内存拷贝， Epoll 在这点上使用了“共享内存 ”，这个内存拷贝也省略了。
   - Epoll 使用了 mmap 加速内核与用户空间的消息传递。这点涉及了 epoll 的具体实现。无论是select, poll，还是 epoll，都需要内核把 FD 消息通知给用户空间，如何避免不必要的内存拷贝就很 重要。在这点上，Epoll 是通过内核与用户空间 mmap 同一块内存实现的。

## Data-Types

### Strings

字符串，这个是最常见的类型，也是二进制安全的，下面通过redis-cli操作常见的命令

```shell

# set get
SET mykey somevalue
SET mykey somevalue1
GET mykey # "somevalue1"
SET mykey somevalue EX 10 NX # 过期时间10秒，允许覆盖重复赋值
SET mykey somevalue PX 1000 XX # 过期时间1000毫秒，如果有值就赋值失败
SET mykey somevalue1 # 再次赋值，过期时间会失效

# 原子自增自减，仅使用于integer类型的value
SET cnt 1
INCR cnt # 2
DECR cnt # 1
INCRBY cnt 10 # 11
DECRBY cnt 10 # 1

# getset
GETSET cnt 20 # 1

# multi
MSET a 10 b 20 c 30
MGET a b c

# exists
EXISTS a
DEL a
EXISTS a
TYPE a

# expire
EXPIRE b 10
TTL b # -1没有设置过期时间，-2已过期或者不存在已删除
PERSIST b # 移除过期时间

```

### Lists

列表，使用linked-list实现，所以有增加，删除很快速，内置~~`RPOPLPUSH`~~命令方便操作，现被`LMOVE RIGHT LEFT`替代

```shell
# push, pop
RPUSH a 1 2 3 # 右边插入
LLEN a # 3
LRANGE a 0 -1 # -1表示最后一个元素，"1"->"2"->"3"
RPOP a # 弹出最右边的元素
LRANGE a 0 -1 # "1"->"2"
RPUSH b 7 8 9
RPOPLPUSH b a # b列表右边弹出，a列表左边插入
LRANGE a 0 -1 # "9"->"1"->"2"

# trim
LTRIM a 0 1 # 丢弃范围外的元素
LRANGE a 0 -1 # "9"->"1"

# block push, block bpop
BRPOP tasks 5 # 如果列表为空，阻塞5秒

# move
RPUSH mylist "one"
RPUSH mylist "two"
RPUSH mylist "three"
LMOVE mylist myotherlist RIGHT LEFT
LMOVE mylist myotherlist LEFT RIGHT
LRANGE mylist 0 -1 # "two"
LRANGE myotherlist 0 -1 # "three"->"one"
```

### Hashes

散列，一般存储field-value，类似java里面的对象，里面包含很多field

```shell
HMSET user:1000 username antirez birthyear 1977 verified 1
HGET user:1000 username # antirez
HMGET user:1000 username birthyear no-such-field # 返回数组
HINCRBY user:1000 birthyear 10 # 1988
```

### Sets

无序的去重字符串集合，可以对集合执行许多其他操作，比如测试给定元素是否已经存在，执行多个集合之间的交集、并集或差集，等等

```shell
SADD myset 1 2 3
SISMEMBER myset 3 # 1
SISMEMBER myset 30 # 0
SMEMBERS myset # "1","2","3"

SADD myset1 2 3 4
SINTER myset myset1 # 交集 "2","3"
SUNIONSTORE myset2 myset myset1 # 取并集存储到myset2
SMEMBERS myset2 # "1","2","3","4"
SRANDMEMBER myset2 # 获取随机元素

```

### Sorted sets

排序集，数据结构包含一个skip list跟hash table，是有序的集合，每一个元素有一个store浮点值，根据store值的大小进行排序，规则如下，

- If A and B are two elements with a different score, then A > B if A.score is > B.score.
- If A and B have exactly the same score, then A > B if the A string is lexicographically greater than the B string. A and B strings can't be equal since sorted sets only have unique elements.

仅对已经包含在排序集中的元素调用`ZADD`，就会以O(log(N))的时间复杂度更新其分数(和位置)

```shell
ZADD hackers 1940 "Alan Kay"
ZADD hackers 1957 "Sophie Wilson"
ZADD hackers 1969 "Linus Torvalds"
ZADD hackers 1912 "Alan Turing"
ZRANGE hackers 0 -1 # "Alan Turing"->"Alan Kay"->...
ZREVRANGE hackers 0 -1 WITHSCORES # 反序带分数 "Linus Torvalds"->"1969"->"Sophie Wilson"->...
ZRANGEBYSCORE hackers -inf 1950 # 1950分数以下的集合，"Alan Turing"->"Alan Kay"
ZREMRANGEBYSCORE hackers 1940 1960 # 移除1940 1960之间的集合，"Alan Turing"->"Linus Torvalds"
```

### Bitmaps

位图是支持按 bit 位来存储信息，可以用来实现 **布隆过滤器（BloomFilter）**

位图不是实际的数据类型，而是在String类型上定义的一组面向位的操作。由于字符串是二进制安全blob，其最大长度为512 MB，因此它们适合设置为232个不同的位

位图最大的优点之一是，在存储信息时，它们通常会极大地节省空间。例如，在一个用增量用户id表示不同用户的系统中，只使用512 MB内存就可以记住40亿用户的单个位信息(例如，知道用户是否想要接收通讯)

`SETBIT`命令的第一个参数是位号，第二个参数是要设置位的值，即1或0。如果地址位超出当前字符串长度，该命令会自动扩大字符串。

```shell
SETBIT key 10 1
GETBIT key 10
BITCOUNT key
```

### HyperLogLogs

供不精确的去重计数功能，比较适合用来做大规模数据的去重统计，例如统计 UV；

通常，计算唯一项需要使用与您想要计算的项数量成比例的内存，因为您需要记住您在过去已经看到的元素，以避免计算它们多次。然而，有一组算法可以用内存来交换精度:你用一个标准误差来结束一个估计的度量，在Redis实现的情况下，这个误差小于1%

```shell
PFADD hll a b c d
PFCOUNT hll # 4
```

### Geospatial

可以用来保存地理位置，并作位置距离计算或者根据半径计算位置等。有没有想过用Redis来实现附近的人？或者计算最优地图路径？

### Stream

Stream是Redis 5.0引入的新data type，**append only data structure**，它以更抽象的方式对日志数据结构进行建模。作为目前redis最复杂的数据类型，支持阻塞队列，可以类比lists的`BLPOP`，不过提供更强大的api，更多介绍请戳[streams-intro](https://redis.io/topics/streams-intro)

```shell
XADD mystream * sensor-id 1234 temperature 19.8
XLEN mystream # 1
```

第二个参数是标识流中每个条目的Entry-ID。但是，在本例中，我们传递了*，因为我们希望服务器为我们生成一个新的ID ，

Entry-Ids：<millisecondsTime>-<sequenceNumber>

新的条目的id不能小于上一个条目的id

```shell
XADD somestream 0-1 field value # 0-1
XADD somestream 0-2 foo bar # 0-2
XADD somestream 0-1 foo bar # ERR The ID specified in XADD is equal or smaller than the target stream top item
```

**Querying by range: XRANGE and XREVRANGE**

```shell
XRANGE mystream - + # -表示最小，+表示最大，特殊的id
XRANGE mystream 1610077925990 1610077925991
XRANGE mystream - + COUNT 2 # 查询范围内的两项
XREVRANGE mystream + - COUNT 1 # 倒叙
```

**Listening for new items with XREAD**

```shell
XREAD COUNT 2 STREAMS mystream 0 # 非阻塞
XREAD BLOCK 0 STREAMS mystream $ # 阻塞
```

XREAD除了COUNT和BLOCK之外没有其他选项，所以它是一个非常基本的命令，具有将消费者附加到一个或多个流的特定目的。使用消费组API可以获得更强大的消费流特性，但是通过消费组读取是通过另一个名为XREADGROUP的命令实现的

Consumer groups

在某种程度上，消费者组可以想象为流的一些状态，

```
consumer_group_name: mygroup           |
| consumer_group_stream: somekey         |
| last_delivered_id: 1292309234234-92    |
|                                        |
| consumers:                             |
|    "consumer-1" with pending messages  |
|       1292309234234-4                  |
|       1292309234232-8                  |
|    "consumer-42" with pending messages |
|       ... (and so forth)        
```

- **XGROUP** is used in order to create, destroy and manage consumer groups.
- **XREADGROUP** is used to read from a stream via a consumer group.
- **XACK** is the command that allows a consumer to mark a pending message as correctly processed.

```shell
# 创建消费组
XGROUP CREATE mystream mygroup $ 
# 流数据，测试
XADD mystream * message apple
XADD mystream * message orange
XADD mystream * message strawberry
XADD mystream * message apricot
XADD mystream * message banana
# 创建消费者
XREADGROUP GROUP mygroup Alice COUNT 1 STREAMS mystream > 
# 标识条目已被处理
XACK mystream mygroup 1526569495631-0
# 指定消费者
XCLAIM mystream mygroup Alice 3600000 1526569498055-0
# 查看流信息
XINFO STREAM mystream
```

下面有个ruby的完整示例

```ruby
require 'redis'

if ARGV.length == 0
    puts "Please specify a consumer name"
    exit 1
end

ConsumerName = ARGV[0]
GroupName = "mygroup"
r = Redis.new

def process_message(id,msg)
    puts "[#{ConsumerName}] #{id} = #{msg.inspect}"
end

$lastid = '0-0'

puts "Consumer #{ConsumerName} starting..."
check_backlog = true
while true
    # Pick the ID based on the iteration: the first time we want to
    # read our pending messages, in case we crashed and are recovering.
    # Once we consumed our history, we can start getting new messages.
    if check_backlog
        myid = $lastid
    else
        myid = '>'
    end

    items = r.xreadgroup('GROUP',GroupName,ConsumerName,'BLOCK','2000','COUNT','10','STREAMS',:my_stream_key,myid)

    if items == nil
        puts "Timeout!"
        next
    end

    # If we receive an empty reply, it means we were consuming our history
    # and that the history is now empty. Let's start to consume new messages.
    check_backlog = false if items[0][1].length == 0

    items[0][1].each{|i|
        id,fields = i

        # Process the message
        process_message(id,fields)

        # Acknowledge the message as processed
        r.xack(:my_stream_key,GroupName,id)

        $lastid = id
    }
end
```

## Pipelining

Redis is a TCP server using the client-server model and what is called a *Request/Response* protocol.

因为Redis是单线程的，所以内部维护一个指令队列，客户端指令过来，先存储到队列，然后依次执行。所以，如果每次只是一个指令过来的化，性能无疑是很低的，通过管道操作，比如一次发三次指令，只需要一次RRT，这样就能极大的提高性能。

```ruby
require 'rubygems'
require 'redis'

def bench(descr)
    start = Time.now
    yield
    puts "#{descr} #{Time.now-start} seconds"
end

def without_pipelining
    r = Redis.new
    10000.times {
        r.ping
    }
end

def with_pipelining
    r = Redis.new
    r.pipelined {
        10000.times {
            r.ping
        }
    }
end

bench("without pipelining") {
    without_pipelining
}
bench("with pipelining") {
    with_pipelining
}
```

如果，需要批量的命令执行，也可以写在文件里，通过redis-cli的管道模式执行，

```shell
cat data.txt | redis-cli --pipe
```

##  Lua Scripting

Redis内置lua脚本解释器，通过使用在服务器端执行大量工作的脚本，可以更有效地解决流水线的许多用例。脚本的一个很大的优点是它能够以最小的延迟读取和写入数据，使读、计算、写等操作非常快(流水线在这个场景中没有帮助，因为客户端在调用写命令之前需要读取命令的应答)。

```shell
eval "return redis.call('set','foo','bar')" # 0
```

lua脚本具有**Atomicity**，当lua脚本执行的时候，没有其他的命令或者脚本会被执行，

lua脚本执行后会被缓存，因为脚本一般不会经常修改，你可以通过刷新脚本指令，删除目前为止执行的所有脚本，

```shell
SCRIPT FLUSH # 刷新脚本
```

## Eviction Policies

Setting `maxmemory` to zero results into no memory limits. This is the default behavior for 64 bit systems, while 32 bit systems use an implicit memory limit of 3GB.

当达到指定的内存量时，可以在不同的行为(称为策略)中进行选择。Redis可以只返回命令错误，这可能导致更多的内存被使用，或者它可以驱逐一些旧数据，以便在每次添加新数据时返回到指定的限制。

- **noeviction**: 直接报错
- **allkeys-lru**: 所有键中删除最近较少使用(LRU)
- **volatile-lru**: 过期键中删除最近较少使用(LRU)
- **allkeys-random**: 所有键中随机删除
- **volatile-random**: 过期键中随机删除
- **volatile-ttl**: 清除带有过期集的键，并首先尝试清除具有较短生存时间(TTL)的键
- **volatile-lfu** 过期键中删除最近使用频率最低的键
- **allkeys-lfu** 所有键中除最近使用频率最低的键

Redis使用近似的LRU算法，避免占据太多内存，4.0后面可以使用LFU算法，更多介绍，请戳[Least Frequently Used eviction mode](http://antirez.com/news/109)

## Transaction

[MULTI](https://redis.io/commands/multi) / [EXEC](https://redis.io/commands/exec)配合，`MULTI`将一系列指令放到一个QUEUE小队列里面，然后`EXEC`，这个小队列里的指令会当成一个整体的指令，依次执行，因为是一个整体，所以小队列里的指令顺序不会被打乱，即允许在一个步骤中执行一组命令

```shell
MULTI
SET a 1
SET b 2
GET a
EXEC

MULTI
SET c 3
GET c
DISCARD # 取消MULTI
```

但是，这并不意味着`MUTLI`是一个完整意义上的事务操作，因为`EXEC`执行后，如果**出错是不会回滚**的，当然你可以通过`DISCARD ` 取消操作

另外，我们可以使用[WATCH](https://redis.io/commands/watch)指令监视某个key，如果在执行EXEC命令之前至少修改了一个监视键，整个事务将中止，EXEC将返回一个空应答以通知事务失败，

```shell
WATCH mykey
val = GET mykey
val = val + 1
MULTI
SET mykey $val
EXEC
```

`WATCH`实现机制为check-and-set的乐观锁，主要思想也是monitor 同步，即同一个时刻，只有一个 进程/线程 能进入 monitor 中定义的临界区

因为，脚本也具有原子性，而且脚本里面出现错误比较好处理，所以官方也是建议使用lua脚本来完成复杂的事务操作，后续可能会废弃掉Transaction，所以这里有个了解即可。

## Persistence

Redis 提供了多种不同级别的持久化方式：

- RDB 持久化可以在指定的时间间隔内生成数据集的时间点快照（point-in-time snapshot）。
- AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。 AOF 文件中的命令全部以 Redis 协议的格式来保存，新命令会被追加到文件的末尾。 Redis 还可以在后台对 AOF 文件进行重写（rewrite），使得 AOF 文件的体积不会超出保存数据集状态所需的实际大小。
- Redis 还可以同时使用 AOF 持久化和 RDB 持久化。 在这种情况下， 当 Redis 重启时， 它会优先使用 AOF 文件来还原数据集， 因为 AOF 文件保存的数据集通常比 RDB 文件所保存的数据集更完整。
- 你甚至可以关闭持久化功能，让数据只在服务器运行时存在。

### RDB

快照模式，

```
# 时间策略
save 900 1
save 300 10
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/work/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes
```

当 Redis 需要保存 `dump.rdb` 文件时， 服务器执行以下操作：

1. Redis 调用 `fork()` ，同时拥有父进程和子进程。
2. 子进程将数据集写入到一个临时 RDB 文件中。
3. 当子进程完成对新 RDB 文件的写入时，Redis 用新 RDB 文件替换原来的 RDB 文件，并删除旧的 RDB 文件。

这种工作方式使得 Redis 可以从写时复制（copy-on-write）机制中获益。

也可以输入命令，手动保存

```shell
bgsave
```

### AOF

append-only file 只进行追加操作，

```
# 是否开启aof
appendonly yes

# 文件名称
appendfilename "appendonly.aof"

# 同步方式
appendfsync everysec

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof时如果有错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

每当 Redis 执行一个改变数据集的命令时（比如 [SET key value [EX seconds\] [PX milliseconds] [NX|XX]](http://redisdoc.com/string/set.html#set)）， 这个命令就会被追加到 AOF 文件的末尾。

这个文件格式，就是纯文本，有一定的格式，而且每一次改变数据集都进行写入，所以体积也较大，

可以输入重写命令，减少文件体积，

```shell
BGREWRITEAOF
```

这个其实可以类比java的JIT，运行时没调用的类，或者代码就不用编译成字节码，减少体积

## Replication

Redis支持主从复制，也可以说是读写分离，master允许读写，salve只允许读，配置多台机子的化，一般可以通过include复用配置文件，

```
inclue /data/redis/conf/redis.conf
port 6380
pidfile /var/run/redis_6380.pid
slaveof 172.17.0.6 6379
```

```shell
info replication # 输出主从复制相关信息
slaveof  <ip>  <port> # 成为某个master的slave，可以配置在redis.conf文件里，
```

**复制原理**

1. 每次从机联通后，给主机发送`sync`指令；
2. 主机进行存盘操作，发送rdb文件给从机；
3. 从机收到rdb文件，进行全盘加载；
4. 之后主机每次进行写操作，都会立刻发指令给从机，从机执行相同指令；

如果主机宕机了，从机可以输入命令

```shell
slaveof  no one  
```

反客为主，变为主机，其他从机的主从关系会相应调整，如果这时候之前的主机恢复了，也将作为新主机的从机，

## Sentinel

反客为主的自动版，每次去输入`slave of one`太麻烦，所以就有个哨兵机制，检测到主机挂掉后，可以自动选举出新主机，

新建配置文件**sentinel.conf**

```
sentinel  monitor  mymaster 172.17.0.6  6379  1
```

```shell
redis-sentinel  /myredis/sentinel.conf 
```

**故障恢复原理**

1. 从下线的主机的所有从机中选取一个成为新主机，选择条件依次为，优先级靠前(slave-priority 100)，偏移量最大(数据量最全)，runid(每个redis实例启动后会生成一个40为的runid)最小，
2. 挑选新的主机后，sentinel向原主机的从机发送`slaveof 新主机`的命令，复制新的master，
3. 当已下线的主机上线后，sentinel会向其发送`slaveof`从其成为新主机的从机

## Cluster

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。会通过分区操作

修改配置文件redis.conf

```
cluster-enabled yes # 打开集群模式
cluster-config-file  nodes-6379.conf # 设定节点配置文件名
cluster-node-timeout 15000 # 设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换。
```

启动，集群是通过ruby实现的，脚本在src目录下，

```shell
cd /opt/redis/src
./redis-trib.rb create --replicas 1 172.17.0.6:6379 172.17.0.6:6380 172.17.0.6:6381 172.17.0.6:6389 172.17.0.6:6390 172.17.0.6:6391
```

一个集群至少要有三个主节点，选项 --replicas 1 表示我们希望为集群中的每个主节点创建一个从节点，

注意，这边ip地址要用真实ip，不要用127.0.0.1，并且保证所有实例下都没有数据，

查看集群节点信息

```shell
cluster nodes
```

### Slots

一个 Redis 集群包含 16384(16k) 个插槽（hash slot）， 数据库中的每个键都属于这 16384 个插槽的其中一个， 集群使用公式 `CRC16(key) & 16383` 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和

集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

- 节点 A 负责处理 0 号至 5500 号插槽
- 节点 B 负责处理 5501 号至 11000 号插槽
- 节点 C 负责处理 11001 号至 16383 号插槽

集群中，插入值，要添加`-c`支持自动重定向，

```shell
redis-cli  -c –p 6379 
```

不在一个slot下的键值，是不能使用`mget,mset`等多键操作

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去

查询集群中的key

```shell
CLUSTER KEYSLOT <key> # 计算key在哪个slot
CLUSTER COUNTKEYSINSLOT <slot> # 返回指定slot包含的键值对数量
CLUSTER GETKEYSINSLOT <slot> <count> # 返回 count 个 slot 槽中的键
```

如果主节点下线，从节点自动上位，如果之前主节点再次上线，会成为新主节点的从节点，

集群不支持lua脚本，也不支持多键事务，

### Docker

模板文件

```
# redis端口
port ${PORT}
# 关闭保护模式
protected-mode no
# 开启集群
cluster-enabled yes
# 集群节点配置
cluster-config-file nodes.conf
# 超时
cluster-node-timeout 5000
# 集群节点IP host模式为宿主机IP
cluster-announce-ip 192.168.124.5
# 集群节点端口 7001 - 7006
cluster-announce-port ${PORT}
cluster-announce-bus-port 1${PORT}
# 开启 appendonly 备份模式
appendonly yes
# 每秒钟备份
appendfsync everysec
# 对aof文件进行压缩时，是否执行同步操作
no-appendfsync-on-rewrite no
# 当目前aof文件大小超过上一次重写时的aof文件大小的100%时会再次进行重写
auto-aof-rewrite-percentage 100
# 重写前AOF文件的大小最小值 默认 64mb
auto-aof-rewrite-min-size 64mb
```

生成配置文件脚本

```sh
for port in `seq 7001 7006`; do \
  mkdir -p ./redis-cluster/${port}/conf \
  && PORT=${port} envsubst < ./redis-cluster.tmpl > ./redis-cluster/${port}/conf/redis.conf \
  && mkdir -p ./redis-cluster/${port}/data; \
done
```

配置docker-compose

```yaml
version: '3.7'

services:
  redis7001:
    image: 'redis'
    container_name: redis7001
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7001/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7001/data:/data
    ports:
      - "7001:7001"
      - "17001:17001"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai


  redis7002:
    image: 'redis'
    container_name: redis7002
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7002/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7002/data:/data
    ports:
      - "7002:7002"
      - "17002:17002"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai


  redis7003:
    image: 'redis'
    container_name: redis7003
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7003/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7003/data:/data
    ports:
      - "7003:7003"
      - "17003:17003"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai


  redis7004:
    image: 'redis'
    container_name: redis7004
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7004/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7004/data:/data
    ports:
      - "7004:7004"
      - "17004:17004"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai


  redis7005:
    image: 'redis'
    container_name: redis7005
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7005/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7005/data:/data
    ports:
      - "7005:7005"
      - "17005:17005"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai


  redis7006:
    image: 'redis'
    container_name: redis7006
    command:
      ["redis-server", "/usr/local/etc/redis/redis.conf"]
    volumes:
      - ./redis-cluster/7006/conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./redis-cluster/7006/data:/data
    ports:
      - "7006:7006"
      - "17006:17006"
    environment:
      # 设置时区为上海，否则时间会有问题
      - TZ=Asia/Shanghai

```

启动集群

```shell
docker-compose up -d
docker exec -it redis7001 redis-cli -p 7001 --cluster create 172.17.0.6:7001 172.17.0.6:7002 172.17.0.6:7003 172.17.0.6:7004 172.17.0.6:7005 172.17.0.6:7006 --cluster-replicas 1
```

### Springboot

maven pom.xml引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

application.yml配置文件

```yaml
spring:
  redis:
    timeout: 6000
    password: 123456
    cluster:
      max-redirects: 3 # 获取失败 最大重定向次数 
      nodes:
        - 172.17.0.6:7001
        - 172.17.0.6:7002
        - 172.17.0.6:7003
        - 172.17.0.6:7004
        - 172.17.0.6:7005
        - 172.17.0.6:7006
    lettuce:
      pool:
        max-active: 1000 #连接池最大连接数（使用负值表示没有限制）
        max-idle: 10 # 连接池中的最大空闲连接
        min-idle: 5 # 连接池中的最小空闲连接
        max-wait: -1 # 连接池最大阻塞等待时间（使用负值表示没有限制）
  cache:
    jcache:
      config: classpath:ehcache.xml
```

代码配置

```java
@Configuration
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisCacheTemplate(LettuceConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```

基本测试

```java
@SpringBootTest
public class RedisTest {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    @Test
    public void test() {
        redisTemplate.opsForValue().set("name", "admin");
        String name = redisTemplate.opsForValue().get("name");
        System.out.println(name); //输出admin
    }
}
```

## Jedis

java语言的redis客户端，

方法参数基本跟redis-cli一致，这里不多赘述，

下面是springboot的jedis配置使用

```xml
 <!--引入redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <!-- 排除lettuce包，使用jedis代替-->
    <exclusions>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

```yaml
server:
  port: 7777
spring:
  profiles:
    active: local
  redis:
    host: 127.0.0.1
    port: 6379
    database: 6
    password: 12345678
    timeout: 3000
    jedis:
      pool:
        max-active: 50
        min-idle: 5
        max-wait: 3000
        max-idle: 20
```

```java
@Configuration
public class JedisConfig {
    @Value("${spring.redis.host}")
    private String host;

    @Value("${spring.redis.port}")
    private int port;

    @Value("${spring.redis.timeout}")
    private int timeout;

    @Value("${spring.redis.jedis.pool.max-idle}")
    private int maxIdle;

    @Value("${spring.redis.jedis.pool.max-wait}")
    private long maxWaitMillis;

    @Value("${spring.redis.password}")
    private String password;

    @Value("${spring.redis.database}")
    private int database;

    @Bean
    public JedisPool jedisPool() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxIdle(maxIdle);
        jedisPoolConfig.setMaxWaitMillis(maxWaitMillis);
        return new JedisPool(jedisPoolConfig, host, port, timeout, password, database);
    }
}
```

```java
@Service
public class Rds {
    private static final Logger log = LoggerFactory.getLogger(Rds.class);

    @Autowired
    JedisPool jedisPool;

    public String set(String key, String value) {
        Jedis jedis = null;
        String result = null;
        try {
            jedis = jedisPool.getResource();
            result = jedis.set(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            log.error("redis连接池异常"+e.getMessage());
            return result;
        } finally {
            release(jedis);
        }
        return result;
    }

    /**
     * 释放Jedis
     */
    protected void release(final Jedis jedis) {
        if (jedis != null) {
            jedis.close();
        }
    }

}
```

## Distlock

Distributed locks，分布式事务锁，也可以基于Redlock算法实现，参考[distlock](https://redis.io/topics/distlock)

java语言的，一般使用[redisson](https://github.com/redisson/redisson)

```xml
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.14.1</version>
</dependency>  
```

```java

public static void main(String[] args) {
 
    Config config = new Config();
    config.useSingleServer().setAddress("redis://127.0.0.1:6379");
    config.useSingleServer().setPassword("redis1234");
    
    final RedissonClient client = Redisson.create(config);  
    RLock lock = client.getLock("lock1");
    
    try{
        lock.lock();
    }finally{
        lock.unlock();
    }
}
```

```java
public RLock getLock(String name) {
    return new RedissonLock(connectionManager.getCommandExecutor(), name);
}
```

```java
public RedissonLock(CommandAsyncExecutor commandExecutor, String name) {
    super(commandExecutor, name);
    //命令执行器
    this.commandExecutor = commandExecutor;
    //UUID字符串
    this.id = commandExecutor.getConnectionManager().getId();
    //内部锁过期时间
    this.internalLockLeaseTime = commandExecutor.
                getConnectionManager().getCfg().getLockWatchdogTimeout();
    this.entryName = id + ":" + name;
}
```

```java
public void lockInterruptibly(long leaseTime, TimeUnit unit) throws InterruptedException {
    
    //当前线程ID
    long threadId = Thread.currentThread().getId();
    //尝试获取锁
    Long ttl = tryAcquire(leaseTime, unit, threadId);
    // 如果ttl为空，则证明获取锁成功
    if (ttl == null) {
        return;
    }
    //如果获取锁失败，则订阅到对应这个锁的channel
    RFuture<RedissonLockEntry> future = subscribe(threadId);
    commandExecutor.syncSubscription(future);
 
    try {
        while (true) {
            //再次尝试获取锁
            ttl = tryAcquire(leaseTime, unit, threadId);
            //ttl为空，说明成功获取锁，返回
            if (ttl == null) {
                break;
            }
            //ttl大于0 则等待ttl时间后继续尝试获取
            if (ttl >= 0) {
                getEntry(threadId).getLatch().tryAcquire(ttl, TimeUnit.MILLISECONDS);
            } else {
                getEntry(threadId).getLatch().acquire();
            }
        }
    } finally {
        //取消对channel的订阅
        unsubscribe(future, threadId);
    }
    //get(lockAsync(leaseTime, unit));
}
```

## Interview

参考[redis面试题及答案整理]( https://juejin.cn/post/6844904110840348680)

## 相关链接

[redis.io](https://redis.io)

[try.redis.io](https://try.redis.io/)



