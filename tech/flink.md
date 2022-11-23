---
title: flink
slug: flink
author: [jianchengwang]
date: 2021-02-26
excerpt: "**Apache Flink** 是一个在无界和有界数据流上进行状态计算的框架和分布式处理引擎.Flink 已经可以在所有常见的集群环境中运行,并以 in-memory 的速度和任意的规模进行计算."
draft: false
tags: 
- middleware
---

**Apache Flink** 是一个在无界和有界数据流上进行状态计算的框架和分布式处理引擎.Flink 已经可以在所有常见的集群环境中运行,并以 in-memory 的速度和任意的规模进行计算.

可以类比 **spring batch** 或者**spark**进行学习,基本流程就是**source->computer/transformation->sink**

本文章的大部分文字都来源于互联网,最底下会附上链接.

### QuickStart

#### 搭建执行环境

这边通过 **docker-compose** 构建,当然也可以直接下载编译好的二进制版本了,[download](https://flink.apache.org/downloads.html)

```yaml
version: "3"
services:
  jobmanager:
    image: flink
    expose:
      - "6123"
    ports:
      - "8081:8081"
    command: jobmanager
    environment:
      - JOB_MANAGER_RPC_ADDRESS=jobmanager
  taskmanager:
    image: flink
    expose:
      - "6121"
      - "6122"
    depends_on:
      - jobmanager
    command: taskmanager
    links:
      - "jobmanager:jobmanager"
    environment:
      - JOB_MANAGER_RPC_ADDRESS=jobmanager
```

#### 创建应用

这里根据创建一个`WordCount`应用

```groovy
buildscript {
    repositories {
        jcenter() // this applies only to the Gradle 'Shadow' plugin
    }
    dependencies {
        classpath 'com.github.jengelman.gradle.plugins:shadow:2.0.4'
    }
}
plugins {
    id 'java'
    id 'application'
    // shadow plugin to produce fat JARs
    id 'com.github.johnrengelman.shadow' version '2.0.4'
}

configurations {
    flinkShadowJar // dependencies which go into the shadowJar

    // always exclude these (also from transitive dependencies) since they are provided by Flink
    flinkShadowJar.exclude group: 'org.apache.flink', module: 'force-shading'
    flinkShadowJar.exclude group: 'com.google.code.findbugs', module: 'jsr305'
    flinkShadowJar.exclude group: 'org.slf4j'
    flinkShadowJar.exclude group: 'org.apache.logging.log4j'
}

ext {
    javaVersion = '1.8'
    flinkVersion = '1.11.2'
    scalaBinaryVersion = '2.12'
    slf4jVersion = '1.7.15'
    log4jVersion = '2.12.1'
}

dependencies {
    compile "org.apache.flink:flink-streaming-java_${scalaBinaryVersion}:${flinkVersion}"
    compile "org.apache.flink:flink-clients_${scalaBinaryVersion}:${flinkVersion}"
    compile "org.apache.flink:flink-connector-kafka_${scalaBinaryVersion}:${fflinkVersion}"
    compile 'org.slf4j:slf4j-simple:1.7.21'
}

// make compileOnly dependencies available for tests:
sourceSets {
    main.compileClasspath += configurations.flinkShadowJar
    main.runtimeClasspath += configurations.flinkShadowJar

    test.compileClasspath += configurations.flinkShadowJar
    test.runtimeClasspath += configurations.flinkShadowJar

    javadoc.classpath += configurations.flinkShadowJar
}

run.classpath = sourceSets.main.runtimeClasspath

jar {
    manifest {
        attributes 'Built-By': System.getProperty('user.name'),
                'Build-Jdk': System.getProperty('java.version')
    }
}

shadowJar {
    configurations = [project.configurations.flinkShadowJar]
}
```

```java
public class WordCount {

    public static void main(String[] args) throws Exception {
        // 获取本地执行环境
        final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        // 设置并行数量
        env.setParallelism(1);
        // 获取数据流
        DataStream<String> stream = env.socketTextStream("localhost", 9999);
        // 转换算子处理数据流并输出结果
        stream.flatMap(new Tokenizer()).keyBy(r -> r.f0).sum(1).print();
        // 执行
        env.execute("Flink Streaming Java API Skeleton");
    }

    public static class Tokenizer implements FlatMapFunction<String, Tuple2<String, Integer>> {
        @Override
        public void flatMap(String value, Collector<Tuple2<String, Integer>> out) throws Exception {
            String[] stringList = value.split("\\s");
            for (String s : stringList) {
                // 使用out.collect方法向下游发送数据
                out.collect(new Tuple2(s, 1));
            }
        }
    }
}
```

如果是在**IDEA**本地运行的话,记得引入依赖`flink-clients`

```shell
 nc -lk 9999
```

如果，已经搭建好了 **Flink WebUI** 运行环境,上传提交编译好的jar包 **JobGraph** 即可,或者通过命令行运行

```shell
flink run -c todo.lib.flink.WordCount WordCount.jar
```

### DataStream API

#### DataSource

##### 内置数据源

###### Elements

从数组或者集合，一般本地调试使用

```java
String[] elementInput = new String[]{"hello Flink", "Second Line"};
DataStream<String> stream = env.fromElements(elementInput);
```

###### File

可以使用 `readTextFile` 方法直接读取文本文件, 这种方式可以用来监控一下 **log** 日志文件, 也可以使用 `readFile` 方法通过指定 `InputFormat` 来读取特定数据类型的文件, `InputFormat`可以是内置类,如 `CsvInputFormat` 或者用户自定义 `InputFormat` 接口类.

在 `readFile()` 方法中有一项参数为 `WatchType`, 共有两种模式 (`PROCESS_CONTINUOUSLY `/ `PROCESS_ONCE`). 在 `PROCESS_CONTINUOUSLY` 模式下, 检测到文件变动就会将文件全部内容加载在 **flink**, 在 `PROCESS_ONCE` 模式下, 只会将文件变动的那部分加载到 **flink**.

```java
// 添加文件源
// 直接读取文本文件
DataStream<String> stream = env.readTextFile(logPath);
// 读取csv
CsvInputFormat csvInput = new RowCsvInputFormat(
    new Path(csvPath),                                        // 文件路径
    new TypeInformation[]{Types.STRING, Types.STRING, Types.STRING},// 字段类型
    "\n",                                             // 行分隔符
    ",");                                            // 字段分隔符
csvInput.setSkipFirstLineAsHeader(true);
// 指定 CsvInputFormat, 监控csv文件(两种模式), 时间间隔是10ms
DataStream<Row> stream = env.readFile(csvInput, csvPath, FileProcessingMode.PROCESS_CONTINUOUSLY, 10);
```

###### Socket

```java
// 添加Socket作为数据输入源
// 4个参数 -> (hostname:Ip地址, port:端口, delimiter:分隔符, maxRetry:最大重试次数)
DataStream<String> stream = env.socketTextStream("localhost", 9999, "\n", 4);
```

##### 外部数据源

外部数据源是重头戏, 一般来说项目中均是使用外部数据源作为数据的源头.

###### 第三方数据源

flink 通过实现 `SourceFunction` 定义了非常丰富的第三方数据连接器.对于第三方数据源, flink的支持分为三种,有**只读型**(Twitter Streaming API / Netty ), **只写型**( Cassandra / Elasticsearch / hadoop FileSystem), 支持**读写**(Kafka / Amazon Kinesis / RabbitMQ)

- Apache Kafka (Source / Sink)
- Apache Cassandra (Sink)
- Amazon Kinesis Streams (Source / Sink)
- Elasticsearch (Sink)
- Hadoop FileSystem (Sink)
- RabbitMQ (Source / Sink)
- Apache NiFI (Source / Sink)
- Twitter Streaming API (Source)
- Apache Bahir 中的连接器:
- Apache ActiveMQ (Source / Sink)
- Apache Flume (Sink)
- Redis (Sink)
- Akka (Sink)
- Netty (Source)

**以Kafka 为例 做演示**

我这边是远程服务器上**docker-compose**启动**kafka**,主要注意下面的**EN_IP**表示外网的IP地址

```yaml
# 一个 kafka节点 就是一个 broker。一个集群由多个 broker 组成。一个 broker可以容纳多个 topic
KAFKA_BROKER_ID: 0
# 配置zookeeper管理kafka的路径
KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181 
# 把kafka的地址端口注册给zookeeper，若远程访问要改成外网IP,千万注意是外网IP
KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://${EN_IP}:9092 
# 配置kafka的监听端口
KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092  
```

```java
Properties properties = new Properties();
properties.setProperty("bootstrap.servers", "EN_IP:9092");
properties.setProperty("group.id", "test");
DataStream<String> dataStream = env
    .addSource(new FlinkKafkaConsumer<>("topic", new SimpleStringSchema(), properties));
dataStream.print();
```

```shell
docker exec -it kafka_container_id bash
cd /opt/kafka/bin
// 生产数据
./kafka-console-producer.sh --broker-list EN_IP:9092 --topic flink-test
// 消费数据
./kafka-console-consumer.sh --bootstrap-server EN_IP:9092 --topic flink-test --from-beginning
```

###### 自定义数据源

用户也可以自己定义连接器, 通过实现 `SourceFunction` 定义单个线程的接入的数据连接器, 也可以通过实现`ParallelSourceFunction` 接口或者继承 `RichParallelSourceFunction` 类定义并发数据源接入器.

```java
 class SourceFromMySQL extends RichSourceFunction<User> {
     PreparedStatement ps;
     private Connection connection;

     @Override
     public void run(SourceContext<User> ctx) throws Exception {
         ResultSet resultSet = ps.executeQuery();
         while (resultSet.next()) {
             User user = new User(
                 resultSet.getInt("id"),
                 resultSet.getString("name").trim());
             ctx.collect(user);
         }
     }
     ........
```



#### Transformation

##### 基本转换算子

基本转换算子会针对流中的每一个单独的事件做处理,也就是说每一个输入数据会产生一个输出数据.单值转换,数据的分割,数据的过滤,都是基本转换操作的典型例子.这个有个概念就行,可以跳过.

###### filter

```java
DataStream<SensorReading> filteredReadings = readings.filter(r -> r.temperature >= 25);
```

###### map

```java
DataStream<String> sensorIds = filteredReadings.map(r -> r.id);
```

###### flatMap

```java
DataStream<String> splitIds = sensorIds
    .flatMap((FlatMapFunction<String, String>)
             (id, out) -> { for (String s: id.split("_")) { out.collect(s);}})
    // provide result type because Java cannot infer return type of lambda function
    // 提供结果的类型，因为Java无法推断匿名函数的返回值类型
    .returns(Types.STRING);
```

###### richFunction

在函数处理数据之前,需要做一些初始化的工作;或者需要在处理数据时可以获得函数执行上下文的一些信息;以及在处理完数据时做一些清理工作

```java
public static class MyFlatMap extends RichFlatMapFunction<Integer, Tuple2<Integer, Integer>> {
  private int subTaskIndex = 0;

  @Override
  public void open(Configuration configuration) {
    int subTaskIndex = getRuntimeContext.getIndexOfThisSubtask;
    // 做一些初始化工作
    // 例如建立一个和HDFS的连接
  }

  @Override
  public void flatMap(Integer in, Collector<Tuple2<Integer, Integer>> out) {
    if (in % 2 == subTaskIndex) {
      out.collect((subTaskIndex, in));
    }
  }

  @Override
  public void close() {
    // 清理工作，断开和HDFS的连接。
  }
}
```

##### 键控流转换算子

很多流处理程序的一个基本要求就是要能对数据进行分组,分组后的数据共享某一个相同的属性.**DataStream API**提供了一个叫做`KeyedStream`的抽象,此抽象会从逻辑上对DataStream进行分区,分区后的数据拥有同样的`Key`值,分区后的流互不相关.

###### keyBy

```java
KeyedStream<SensorReading, String> keyed = readings.keyBy(r -> r.id);
```

###### fold

通过将最后一个文件夹流与当前记录组合来推出 KeyedStream.它会发回数据流.

```java
KeyedStream.fold("1", new FoldFunction<Integer, String>() {
    @Override
    public String fold(String accumulator, Integer value) throws Exception {
        return accumulator + "=" + value;
    }
})
```

###### aggregate

滚动聚合算子由`KeyedStream`调用,并生成一个聚合以后的DataStream.

滚动聚合算子只能用在滚动窗口,不能用在滑动窗口.

滚动聚合操作会对每一个key都保存一个状态。因为状态从来不会被清空，所以我们在使用滚动聚合算子时只能使用在含有有限个key的流上面。

常见的滚动聚合算子: sum,min,max,minBy,maxBy

```java
DataStream<Tuple3<Integer, Integer, Integer>> resultStream = inputStream
    .keyBy(0) // key on first field of the tuple
    .sum(1);   // sum the second field of the tuple in place
```

###### window

允许按时间或其他条件对现有 KeyedStream 进行分组.以下是以 10 秒的时间窗口聚合:

```java
inputStream.keyBy(0).window(Time.seconds(10));
inputStream.keyBy(0).windowAll(Time.seconds(10));
```

###### window join

我们可以通过一些 key 将同一个 window 的两个数据流 join 起来.

以下示例是在 5 秒的窗口中连接两个流,其中第一个流的第一个属性的连接条件等于另一个流的第二个属性

```java
inputStream.join(inputStream1)
           .where(0).equalTo(1)
           .window(Time.seconds(5))     
           .apply (new JoinFunction () {...});
```

###### split

此功能根据条件将流拆分为两个或多个流.当您获得混合流并且您可能希望单独处理每个数据流时,可以使用此方法.

```java
SplitStream<Integer> split = inputStream.split(new OutputSelector<Integer>() {
    @Override
    public Iterable<String> select(Integer value) {
        List<String> output = new ArrayList<String>(); 
        if (value % 2 == 0) {
            output.add("even");
        }
        else {
            output.add("odd");
        }
        return output;
    }
});
```

###### select

此功能允许您从拆分流中选择特定流

```java
SplitStream<Integer> split;
DataStream<Integer> even = split.select("even"); 
DataStream<Integer> odd = split.select("odd"); 
DataStream<Integer> all = split.select("even","odd");
```

###### project

Project 函数允许您从事件流中选择属性子集,并仅将所选元素发送到下一个处理流.

```java
DataStream<Tuple4<Integer, Double, String, String>> in = // [...] 
DataStream<Tuple2<String, String>> out = in.project(3,2);
```

###### reduce

reduce函数可以通过实现接口ReduceFunction来创建一个类.ReduceFunction接口定义了`reduce()`方法,此方法接收两个输入事件,输入一个相同类型的事件.

reduce作为滚动聚合的泛化实现,同样也要针对每一个key保存状态.因为状态从来不会清空,所以我们需要将reduce算子应用在一个有限key的流上.

```java
DataStream<SensorReading> maxTempPerSensor = keyed
    .reduce((r1, r2) -> {
        if (r1.temperature > r2.temperature) {
            return r1;
        } else {
            return r2;
        }
    });
```

##### 多流转换算子

许多应用需要摄入多个流并将流合并处理,还可能需要将一条流分割成多条流然后针对每一条流应用不同的业务逻辑.

###### union

合流的方式为FIFO方式,合并流类型要一致.

```java
DataStream<SensorReading> parisStream = ...
DataStream<SensorReading> tokyoStream = ...
DataStream<SensorReading> rioStream = ...
DataStream<SensorReading> allCities = parisStream
  .union(tokyoStream, rioStream)
```

###### connect,comap,coflatmap

两个流的数据类型可以不同,会对两个流中的数据应用不同的处理方法.

```java
DataStream<Tuple2<Integer, Long>> one = ...
DataStream<Tuple2<Integer, String>> two = ...
// keyBy two connected streams
ConnectedStreams<Tuple2<Int, Long>, Tuple2<Integer, String>> keyedConnect1 = one
  .connect(two)
  .keyBy(0, 0); // key both input streams on first attribute
// alternative: connect two keyed streams
ConnectedStreams<Tuple2<Integer, Long>, Tuple2<Integer, String>> keyedConnect2 = one
  .keyBy(0)
  .connect(two.keyBy(0));
```

##### 分布式转换算子

定义了事件如何分配到不同的任务中去

当我们使用DataStream API来编写程序时,系统将自动的选择数据分区策略,然后根据操作符的语义和设置的并行度将数据路由到正确的地方去.有些时候,我们需要在应用程序的层面控制分区策略,或者自定义分区策略

###### random

随机数据交换由`DataStream.shuffle()`方法实现。shuffle方法将数据随机的分配到下游算子的并行任务中去

###### round-robin

`rebalance()`方法使用Round-Robin负载均衡算法将输入流平均分配到随后的并行运行的任务中去

###### rescale

`rescale()`方法使用的也是round-robin算法,但只会将数据发送到接下来的并行运行的任务中的一部分任务中.本质上,当发送者任务数量和接收者任务数量不一样时,rescale分区策略提供了一种轻量级的负载均衡策略.如果接收者任务的数量是发送者任务的数量的倍数时,rescale操作将会效率更高.

`rebalance()`和`rescale()`的根本区别在于任务之间连接的机制不同.`rebalance()`将会针对所有发送者任务和所有接收者任务之间建立通信通道,而`rescale()`仅仅针对每一个任务和下游算子的一部分子并行任务之间建立通信通道

###### broadcast

`broadcast()`方法将输入流的所有数据复制并发送到下游算子的所有并行任务中去.

###### global

`global()`方法将所有的输入流数据都发送到下游算子的第一个并行任务中去.这个操作需要很谨慎,因为将所有数据发送到同一个task,将会对应用程序造成很大的压力.

###### custom

当Flink提供的分区策略都不适用时,我们可以使用`partitionCustom()`方法来自定义分区策略.这个方法接收一个`Partitioner`对象,这个对象需要实现分区逻辑以及定义针对流的哪一个字段或者key来进行分区.

#### Sink

Flink没有类似于spark中foreach方法,让用户进行迭代的操作.所有对外的输出操作都要利用Sink完成.最后通过类似如下方式完成整个任务最终输出操作.

```java
stream.addSink(new MySink(xxxx));
```

官方提供了一部分的框架的sink.除此以外,需要用户自定义实现sink.

##### 第三方sink

###### kafka

```xml
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-connector-kafka_2.11</artifactId>
  <version>${flink.version}</version>
</dependency>
```

```java
DataStream<String> union = high
  .union(low)
  .map(r -> r.temperature.toString);

union.addSink(
  new FlinkKafkaProducer011<String>(
    "localhost:9092",
    "test",
    new SimpleStringSchema()
  )
);
```

###### redis

```xml
<dependency>
  <groupId>org.apache.bahir</groupId>
  <artifactId>flink-connector-redis_2.11</artifactId>
  <version>1.0</version>
</dependency>
```

```java
public class RedisSink_ {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        DataStream<User> stream = env.addSource(new UserSource());


        FlinkJedisPoolConfig conf = new FlinkJedisPoolConfig.Builder().setHost("localhost").build();

        stream.addSink(new RedisSink<SensorReading>(conf, new MyRedisSink()));

        env.execute();
    }

    public static class MyRedisSink implements RedisMapper<User> {
        @Override
        public String getKeyFromData(User user) {
            return user.getId().toString();
        }

        @Override
        public String getValueFromData(User User) {
            return user.getName();
        }

        @Override
        public RedisCommandDescription getCommandDescription() {
            return new RedisCommandDescription(RedisCommand.HSET, "flink-test");
        }
    }
}
```

```shell
docker exec -it redis_container_id redis-cli
auth 123456
keys keys flink-test
hvals flink-test
```

###### elasticsearch

```xml
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-connector-elasticsearch6_2.11</artifactId>
  <version>${flink.version}</version>
</dependency>

<!-- 可选依赖 -->  
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.9.1</version>

</dependency>
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>7.9.1</version>
</dependency>
```

```java
public class EsSink_ {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        DataStreamSource<User> stream = env.addSource(new UserSource());

        ArrayList<HttpHost> httpHosts = new ArrayList<>();
        httpHosts.add(new HttpHost("localhost", 9200, "http"));
        ElasticsearchSink.Builder<User> sensorReadingBuilder = new ElasticsearchSink.Builder<>(
                httpHosts,
                (ElasticsearchSinkFunction<User>) (user, runtimeContext, requestIndexer) -> {
                    HashMap<String, String> map = new HashMap<>();
                    map.put("data", user.toString());
                    IndexRequest indexRequest = Requests
                            .indexRequest()
                            .index("flink-test") // 索引是flink-test，相当于数据库
                            .type("user") // es6需要加这一句
                            .source(map);

                    requestIndexer.add(indexRequest);
                }
        );
        sensorReadingBuilder.setBulkFlushMaxActions(1);
        stream.addSink(sensorReadingBuilder.build());

        env.execute();
    }
}
```

##### 自定义sink

继承 RichSinkFunction 抽象类,重写 invoke 方法

```java
public static class MyJDBCSink extends RichSinkFunction<User> {
    private Connection conn;
    private PreparedStatement insertStmt;
    private PreparedStatement updateStmt;

    @Override
    public void invoke(User value, Context context) throws Exception {
        updateStmt.setString(1, value.getName());
        updateStmt.setInt(2, value.getId());
        updateStmt.execute();

        if (updateStmt.getUpdateCount() == 0) {
            insertStmt.setInt(1, value.getId());
            insertStmt.setString(2, value.getName());
            insertStmt.execute();
    	}
    }
    ....
```



#### Window

##### 时间 time

事件时间 **Event Time**,即事件实际发生的时间,可以处理乱序事件,一般都用这个;
摄入时间 **Ingestion Time**,事件进入流处理框架的时间;
处理时间 **Processing Time**,事件被处理的时间,执行操作算子的本地时间,与机器无关.统计某些延时非常高的日志.

```java
final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
//设置时间属性为 EventTime
env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);

DataStream<MyEvent> stream = env.addSource(new FlinkKafkaConsumer09<MyEvent>(topic, schema, props));
stream
    .keyBy( (event) -> event.getUser() )
    .timeWindow(Time.hours(1))
    .reduce( (a, b) -> a.add(b) )
    .addSink(...);
```

```java
// 这个方法中的 while 循环部分会从 eventTimeTimersQueue 中依次取出触发时间小于参数 time 的所有定时器，调用 triggerTarget.onEventTime() 方法进行触发。这就是 EventTime 从注册到触发的流程。
InternalTimeServiceImpl.advanceWatermark。
public void advanceWatermark(long time) throws Exception {
   currentWatermark = time;
   InternalTimer<K, N> timer;

   while ((timer = eventTimeTimersQueue.peek()) != null && timer.getTimestamp() <= time) {
      eventTimeTimersQueue.poll();
      keyContext.setCurrentKey(timer.getKey());
      triggerTarget.onEventTime(timer);
   }
}
```

##### 水位线 watermark

###### 概念

**水印的出现是为了解决实时计算中的数据乱序问题，它的本质是 DataStream 中一个带有时间戳的元素**。

如果 Flink 系统中出现了一个 WaterMark T,那么就意味着 EventTime < T 的数据都已经到达,窗口的结束时间和 T 相同的那个窗口被**触发**进行计算了.

也就是说:水印是 Flink 判断迟到数据的标准,同时也是窗口触发的标记.

在程序并行度大于 1 的情况下,会有多个流产生水印和窗口,这时候 Flink 会选取时间戳最小的水印.

###### 使用水印

**a. 在 Source Function 中 直接指定 Timestamps 和 Watermark**

 用户需要复写 SourceFunction 接口中 run( ) 方法实现数据逻辑, 同时调用 SourceContext 的 collectWithTimestamp( ) 方法生成 event time 时间戳, 调用 emitWatermark( ) 方法生成 watermark.

```java
DataStream<String> text = env.addSource(new SourceFunction<String>() {
            @Override
            public void run(SourceContext<String> ctx) throws Exception {
                for (String s : elementInput) {
                    // 切割每一条数据
                    String[] inp = s.split(",");
                    Long timestamp = new Long(inp[1]);
                    // 生成 event time 时间戳
                    ctx.collectWithTimestamp(s, timestamp);
                    // 调用 emitWatermark() 方法生成 watermark, 最大延迟设定为 2
                    ctx.emitWatermark(new Watermark(timestamp - 2));
                }
                // 设定默认 watermark
                ctx.emitWatermark(new Watermark(Long.MAX_VALUE));
            }

            @Override
            public void cancel() {

            }
        });
```

**b. 通过 Flink 自带的 Timestamp Assigner 指定 Timestamp 和 生成 watermark**

**b.1 使用 Ascending Timestamp Assigner 指定 Timestamps 和 Watermark**

```java
 DataStream<Tuple2<String, Long>> dataStream = env.fromCollection(collectionInput);
        dataStream.assignTimestampsAndWatermarks(
                (WatermarkStrategy<Tuple2<String, Long>>) context -> new WatermarkGenerator<Tuple2<String,Long>>(){
                    private long maxTimestamp;
                    private long delay = 3000;
                    @Override
                    public void onEvent(
                            Tuple2<String,Long> event,
                            long eventTimestamp,
                            WatermarkOutput output){
                        maxTimestamp = Math.max(maxTimestamp, event.f1);
                    }
                    @Override
                    public void onPeriodicEmit(WatermarkOutput output){
                        output.emitWatermark(new Watermark(maxTimestamp - delay));
                    }
                });
```

**b.2 内置水印生成策略**

**b.2.1 固定延迟生成水印**

通过静态方法`forBoundedOutOfOrderness`提供,入参接收一个Duration类型的时间间隔，也就是我们可以接受的最大的延迟时间.使用这种延迟策略的时候需要我们对数据的延迟时间有一个大概的预估判断。

```java
WatermarkStrategy.forBoundedOutOfOrderness(Duration maxOutOfOrderness)
```

我们实现一个延迟3秒的固定延迟水印，可以这样做：

```java
DataStream dataStream = ...... ;
dataStream.assignTimestampsAndWatermarks(WatermarkStrategy.forBoundedOutOfOrderness(Duration.ofSeconds(3)));
```

他的底层使用的WatermarkGenerator接口的一个实现类BoundedOutOfOrdernessWatermarks。我们看下源码中的这两个方法，是不是和我们上面自己写的很像.

```java
 @Override
 public void onEvent(T event, long eventTimestamp, WatermarkOutput output) {
  maxTimestamp = Math.max(maxTimestamp, eventTimestamp);
 }

 @Override
 public void onPeriodicEmit(WatermarkOutput output) {
  output.emitWatermark(new Watermark(maxTimestamp - outOfOrdernessMillis - 1));
 }
```

**b.2.2 单调递增生成水印**

通过静态方法`forMonotonousTimestamps`来提供.

```java
WatermarkStrategy.forMonotonousTimestamps()
```

这个也就是相当于上述的延迟策略去掉了延迟时间，以event中的时间戳充当了水印。

在程序中可以这样使用：

```java
DataStream dataStream = ...... ;
dataStream.assignTimestampsAndWatermarks(WatermarkStrategy.forMonotonousTimestamps());
```

它的底层实现是AscendingTimestampsWatermarks，其实它就是BoundedOutOfOrdernessWatermarks类的一个子类，没有了延迟时间，我们来看看具体源码的实现.

```java
@Public
public class AscendingTimestampsWatermarks<T> extends BoundedOutOfOrdernessWatermarks<T> {

 /**
  * Creates a new watermark generator with for ascending timestamps.
  */
 public AscendingTimestampsWatermarks() {
  super(Duration.ofMillis(0));
 }
}
```

###### event时间的获取

上述我们讲了flink自带的两种水印生成策略，但是对于我们使用eventtime语义的时候，我们想从我们的自己的数据中抽取eventtime，这个就需要TimestampAssigner了.

```java
@Public
@FunctionalInterface
public interface TimestampAssigner<T> {

    ............
    
 long extractTimestamp(T element, long recordTimestamp);
}
```

使用的时候我们主要就是从我们自己的元素element中提取我们想要的eventtime。

使用flink自带的水印策略和eventtime抽取类，可以这样用：

```java
DataStream dataStream = ...... ;
dataStream.assignTimestampsAndWatermarks(
    WatermarkStrategy
      .<Tuple2<String,Long>>forBoundedOutOfOrderness(Duration.ofSeconds(5))
      .withTimestampAssigner((event, timestamp)->event.f1));
```

###### 处理空闲数据源

在某些情况下，由于数据产生的比较少，导致一段时间内没有数据产生，进而就没有水印的生成，导致下游依赖水印的一些操作就会出现问题，比如某一个算子的上游有多个算子，这种情况下，水印是取其上游两个算子的较小值，如果上游某一个算子因为缺少数据迟迟没有生成水印，就会出现eventtime倾斜问题，导致下游没法触发计算。

所以filnk通过WatermarkStrategy.withIdleness()方法允许用户在配置的时间内（即超时时间内）没有记录到达时将一个流标记为空闲。这样就意味着下游的数据不需要等待水印的到来。

当下次有水印生成并发射到下游的时候，这个数据流重新变成活跃状态。

通过下面的代码来实现对于空闲数据流的处理

```java
WatermarkStrategy
        .<Tuple2<Long, String>>forBoundedOutOfOrderness(Duration.ofSeconds(20))
        .withIdleness(Duration.ofMinutes(1));
```

##### 窗口简介 window

窗口是流式计算中非常重要的一个概念, 很多常见的功能都是通过各种窗口实现的, 比如每5分钟统计一下刚去1小时的热度. Flink DataStream API 将窗口独立成 Operator. 每个窗口算子包含了以下几个部分:

**Windows Assigner**

指定窗口的类型, 定义如何将数据流分配到一个或者多个窗口

**Windows Trigger**

指定窗口触发的时机, 定义窗口满足什么样的条件触发计算

**Evictor**

用户数据剔除

**Lateness**

标记是否处理迟到的数据, 当迟到数据到达窗口中是否触发计算

**Output Tag**

标记输出标签, 然后再通过 getSideOutput 将窗口中的数据根据标签输出

**Windows Function**

定义窗口上的数据处理的逻辑, 例如对数据进行sum

##### Window Assigner

首先最需要了解的就是 windows Assigner了, 我们想要一个什么样的窗口划分, 主要就是通过他来实现的.

根据 flink 上游的数据集是否为 KeyedStream 类型 来做分别的处理. 如果使用了keyBy( ) 则对应使用window( ) 来处理, 否则可以使用 windowAll( )来使用

Flink 可以支持两种类型的窗口, 分别是基于时间的窗口和基于数量的窗口.基于时间的意思就是按照时间去划分窗口,同理,基于数量的也是根据窗口中的数量来做切分的. 对应的分别就是 timeWindow() 和 countWindow() 来使用, 下面的示例主要使用 timeWindow() 来演示.

对于不同的 Window Assigner, 还可以把窗口划分为4大类, 分别是 滚动窗口(Tumbling Windows) / 滑动窗口(Sliding Window) / 会话窗口(Session Window) 和 全局窗口(Global Window).

###### 滚动窗口 Tumbling Windows

DataStream API 提供基于 EventTime 和 ProcessingTime 的两种类型的 Tumbling window.对应的 Assigner 分别是 TumblingEventTimeWindow 和 ProcessingEventTimeWindow . 举例如下,完整代码见Github.

```
// 使用ProcessTime的滚动时间窗口, 长度为10s
stream.keyBy(x -> x.f1)
    .window(TumblingProcessingTimeWindows.of(Time.seconds(10))).process(...)
// 使用ProcessTime的滚动时间窗口, 长度为10s
stream.keyBy(x ->x.f1).window(TumblingEventTimeWindows.of(Time.seconds(10))).process(...)
```

使用 window(TumblingProcessingTimeWindows.of(Time.seconds(10))) 的方法有点啰嗦, Flink 还提供了timeWindow( ) 的 API 来简化这一行代码.

```
// 直接使用 timeWindow API 便可实现滚动窗口的操作, 参数依旧是窗口的长度
// 窗口类型的时间由 time characteristic 确定, 如果指定为 event time,那么窗口也会自动用这个时间
input.keyBy(x -> x.f1).timeWindow(Time.seconds(10));
```

###### 滑动窗口 Sliding Window

滑动窗口顾名思义就是一个在不断往后滑动的窗口, 比如说 每5分钟 统计一个 最近一小时的时间, 那么就需要用滑动窗口来做处理. 滑动窗口主要是依靠 window size 和 slide time 来确定. 与滚动窗口类似的, flink 也提供了对应不同时间的 Assigner API(SlidingEventTimeWindow / SlidingEventTimeWindow), 语法基本类似, 只是由原本的一个参数(窗口长度) 变为了两个参数(窗口长度和滑动时间), 同样的, 为了简化代码, 依然可以使用timeWindow() 来简化.

```
// 两个参数分别是 窗口长度 和 滑动时间, 窗口时间类型依旧通过time characteristic 确定
input.keyBy(x -> x.f1).timeWindow(Time.seconds(10), Time.seconds(1))
```

###### 会话窗口 Session Window

会话窗口主要是将某段时间内活跃度较高的数据聚合成一个窗口计算. 触发条件是 Session Gap. 在规定的时间内没有数据接入则认为这个窗口结束,然后触发窗口计算. Session Gap 除了固定间隔的方式, 也可以动态抽取.

```
// 创建 Session Window, 间隔为 3s
        DataStream<Tuple3<String, Long, Integer>> aggregated = source
                .keyBy(0)
                .window(EventTimeSessionWindows.withGap(Time.seconds(3L)))
                .sum(2);
```

###### 全局窗口 Global Window

全局窗口将所有key的数据分配到单个窗口中计算结果.

```
// 创建 GlobalWindow
        input.keyBy(1)
                .window(GlobalWindows.create())
                .sum(1);
```

##### Window Function

Window Assigner 的作用是划分窗口的, 而 Window Function 就是对窗口内的数据做处理的一个过程

###### ReduceFunction (增量)

对输入的两个相同类型的元素按照指定的计算方式进行聚合, 通过实现 ReduceFunction 接口就可以在reduce( ) 函数内部进行聚合操作了.

```java
input.keyBy(x -> x.f1).timeWindow(Time.seconds(10), Time.seconds(1))
	.reduce((t1,t2) -> new Tuple2<>(t1.f0 + t2.f0, t1.f1));
```

 ###### AggregateFunction (增量)

AggregateFunction 相对于ReduceFunction更加灵活,但是实现起来也更复杂, AggregateFunction有 4 个需要复写的方法, 其中createAccumulator( ) 定义累加器, add( ) 定义数据的添加逻辑, getResult( ) 定义了根据 accumulator 计算结果的逻辑, merge()方法定义合并 accumulator 的逻辑.

```java
input.keyBy(x -> x.f1)
    .timeWindow(Time.seconds(10), Time.seconds(1))
    // 自定义一个AggregateFunciton, 将相同标号 f1 的数据的 f0字符串字段合并在一起
    // ("hello", 1L) + ("world", 1L) = ("hello world", 1L)
    .aggregate(new MyAggregateFunction());
```

通过自定义的 MyAggregateFunction() 来实现 AggregateFunction 接口

```java
public static class MyAggregateFunction implements AggregateFunction<Tuple2<String, Long>, String, String>{
        @Override
        public String createAccumulator() {
        	// 初始化累加器
            return "";
        }
        @Override
        public String add(Tuple2<String, Long> t, String s) {
            // 输入数据与累加器的合并
            return s + " " +t.f0;
        }
        @Override
        public String getResult(String s) {
            // 得到累加器的结果
            return s.trim();
        }
        @Override
        public String merge(String s, String acc1) {
            // 合并累加器
            return s + " " + acc1;
        }
    }
```

###### FoldFunction (增量)

FoldFunction定义了如何将窗口中的输入元素与外部的元素合并的逻辑

```java
input.keyBy(x -> x.f1)
.timeWindow(Time.seconds(10), Time.seconds(1)).fold("flink", (acc, t) ->t.f0 + acc);
```

FoldFunction在新版本已经被标记@Deprecated了, 建议使用AggregateFunction代替

###### ProcessWindowFunction (全量)

ProcessWindowFunction 相较于其他的 Window Function, 可以实现一些更复杂的计算, 比如基于整个窗口做某些指标计算 或者需要操作窗口中的状态数据和窗口元数据. Flink 提供了 ProcessWindowFunction 这个抽象类, 继承此类就可以实现ProcessWindowFunction, 其中, 必须要实现 process( ) 方法, 这是处理窗口数据的主要方法.还在一下跟窗口数据相关的方法可以有选择的实现.

```java
public static class MyProcessWindowFunction extends ProcessWindowFunction<Tuple3<String, Long, Long>, String, Long, TimeWindow> {
	@Override
    public void process(Long s, Context context, Iterable<Tuple3<String, Long, Long>> 		elements, Collector<String> out) throws Exception {
        // 统计每个窗口内的所有数据的 f0字段加起来共有多少个单词
        // 也就做单个窗口的 wordcount
        Long count = 0L;
        for (Tuple3<String, Long, Long> element : elements) {
            count += element.f0.split(" ").length;
        }
        out.collect("window: " + context.window() + " word count: " + count);
	}
}
```

##### Window Join

Flink 中支持窗口上的多流合并, 需要保证的是输入的 stream 要构建在相同的 Window 上, 并使用相同类型的 Key 作为关联条件.

```java
inputStream1.join(inputStream2)
			// 指定inputStream1的关联key
			.where(0)
			// 指定inputStream2的关联key
			.equalTo(1)
			// 指定 window Assigner
			.window(TumblingEventTimeWindows.of(Time.seconds(10)))
			// 指定窗口计算函数
			.apply(<JoinFunction>)
```

##### 处理迟到的元素

迟到的元素是指当这个元素来到时,这个元素所对应的窗口已经计算完毕了(也就是说水位线已经没过窗口结束时间了).这说明迟到这个特性只针对事件时间.

DataStream API提供了三种策略来处理迟到元素

###### 直接抛弃

抛弃迟到的元素是event time window operator的默认行为.也就是说一个迟到的元素不会创建一个新的窗口.

process function可以通过比较迟到元素的时间戳和当前水位线的大小来很轻易的过滤掉迟到元素.

###### 重定向

迟到的元素也可以使用侧输出(side output)特性被重定向到另外的一条流中去.迟到元素所组成的侧输出流可以继续处理或者sink到持久化设施中去.

###### 更新窗口计算结果

由于存在迟到的元素,所以已经计算出的窗口结果是不准确和不完全的.我们可以使用迟到元素更新已经计算完的窗口结果.

window operator API提供了方法来明确声明我们要等待迟到元素.当使用event-time window,我们可以指定一个时间段叫做`allowed lateness`.window operator如果设置了`allowed lateness`,这个window operator在水位线没过窗口结束时间时也将不会删除窗口和窗口中的状态.窗口会在一段时间内(allowed lateness设置的)保留所有的元素.

当迟到元素在`allowed lateness`时间内到达时,这个迟到元素会被实时处理并发送到触发器(trigger).当水位线没过了窗口结束时间+allowed lateness时间时,窗口会被删除,并且所有后来的迟到的元素都会被丢弃.

#### State

官方文档有详细描述,这里不多赘述.

https://ci.apache.org/projects/flink/flink-docs-release-1.11/zh/dev/stream/state/state.html

#### Table

Flink本身是批流统一的处理框架,所以Table API和SQL,就是批流统一的上层处理API.目前还在完善中,所以后面待完善.

```groovy
compileOnly "org.apache.flink:flink-table-api-java-bridge_${scalaBinaryVersion}:${flinkVersion}"
// 本地运行，线上lib已经包含，不需要引入
compileOnly "org.apache.flink:flink-table-planner-blink_${scalaBinaryVersion}:${flinkVersion}"
// 自定义函数，线上lib已经包含，不需要引入
compileOnly "org.apache.flink:flink-table-common:${flinkVersion}"
```

**未完待续.....**

### 相关链接

[apache flink](https://ci.apache.org/projects/flink/flink-docs-release-1.12/)

[github flink](https://github.com/apache/flink)

[github flink-learning](https://github.com/zhisheng17/flink-learning)

[github flink-simple-tutorial](https://github.com/CheckChe0803/flink-simple-tutorial)

[尚硅谷](https://confucianzuoyuan.github.io/flink-tutorial/)