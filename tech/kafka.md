---
title: kafka
createdAt: 2020-02-09
categories: 
- middleware
tags:
- kafka
- message queue


---

[Kafka](http://kafka.apache.org/)是一个分布式的基于发布/订阅模式的消息队列（Message Queue），主要应用于大数据实时处理领域。

<!--more-->

## Kafka架构

![jaigou](https://blog.res.jianchengwang.info/posts/kafka/jiagou.png)

1. Producer ：消息生产者，就是向 kafka broker 发消息的客户端；
2. Consumer ：消息消费者，向 kafka broker 取消息的客户端；
3. Consumer Group （CG）：消费者组，由多个 consumer 组成。消费者组内每个消费者负责消费不同分区的数据，一个分区只能由消费者组中的一个消费者消费；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者；
4. Broker ：一台 kafka 服务器就是一个 broker。一个集群由多个 broker 组成。一个 broker可以容纳多个topic；
5. Topic ：可以理解为一个队列，生产者和消费者面向的都是一个 topic；
6. Partition：为了实现扩展性，一个非常大的 topic 可以分布到多个 broker（即服务器）上，一个 topic 可以分为多个 partition，每个 partition 是一个有序的队列；
7. Replica：副本，为保证集群中的某个节点发生故障时，该节点上的 partition 数据不丢失，且 kafka 仍然能够继续工作，kafka提供了副本机制，一个 topic 的每个分区都有若干个副本，一个 leader 和若干个 follower。
8. leader：每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数据的对象都是 leader。
9. follower：每个分区多个副本中的“从”，实时从 leader 中同步数据，保持和 leader 数据的同步。leader 发生故障时，某个 follower 会成为新的follower。

对于每一个topic， Kafka集群都会维持一个分区日志，如下所示：

![partition-log](https://blog.res.jianchengwang.info/posts/kafka/partition_log.png)

每个分区都是有序且顺序不可变的记录集，并且不断地追加到结构化的commit log文件。分区中的每一个记录都会分配一个id号来表示顺序，我们称之为offset，offset用来唯一的标识分区中每一条记录。

Kafka每个分区的数据是严格有序的，但多分区之间不能确保有序。

## Install

这里为了方便，采用docker镜像，

```yaml
version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper   ## 镜像
    ports:
      - "2181:2181"                 ## 对外暴露的端口号
  kafka:
    image: wurstmeister/kafka       ## 镜像
    volumes:
        - /etc/localtime:/etc/localtime ## 挂载位置（kafka镜像和宿主机器之间时间保持一直）
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 0 # 一个 kafka节点 就是一个 broker。一个集群由多个 broker 组成。一个 broker可以容纳多个 topic
      KAFKA_DELETE_TOPIC_ENABLE: 1 # 允许删除topic
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181 # 配置zookeeper管理kafka的路径
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://${EN_IP}:9092 # 把kafka的地址端口注册给zookeeper，若远程访问要改成外网IP,千万注意是外网IP
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092 # 配置kafka的监听端口 

      KAFKA_LOG_RETENTION_HOURS: 120
      KAFKA_MESSAGE_MAX_BYTES: 10000000
      KAFKA_REPLICA_FETCH_MAX_BYTES: 10000000
      KAFKA_GROUP_MAX_SESSION_TIMEOUT_MS: 60000
      KAFKA_NUM_PARTITIONS: 3
      KAFKA_DELETE_RETENTION_MS: 1000
  kafka-manager:
    image: sheepkiller/kafka-manager                ## 镜像：开源的web管理kafka集群的界面
    environment:
        ZK_HOSTS: ${HOST_IP}                   ## 修改:宿主机IP
    ports:
      - "9001:9000"                                 ## 暴露端口                                                                                                                                                                                                                                                                                      
```

## Command



