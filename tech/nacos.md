---
title: nacos
createdAt: 2019-08-19
categories: 
- middleware
tags: 
- nacos
---

新公司使用 `nacos` 用于发现管理，配置微服务，所以这里做个文档备注下。

以下部分文字从网上扒下来的，最底下会列出相关链接，感兴趣的可以喵喵。

简单来说就是 `Nacos =  Eureka/Consule + Config `

<!--more-->

### 启动nacos服务中心

**Nacos Server 有两种运行模式：**

- standalone
- cluster

不论哪种方式吧，都需要先去 https://github.com/alibaba/nacos/releases 下载最新的 release 包

```shell
wget https://github.com/alibaba/nacos/releases/download/1.1.0/nacos-server-1.1.0.tar.gz

tar -zxvf nacos-server-1.1.0.tar.gz 
cd nacos/bin
sh startup.sh -m standalone &
# 如果你替换了默认sh脚本，可能会报错，因为这个脚本有bash的特有符号'[['
bash -f ./startup.sh -m standalone &
curl 127.0.0.1:8848/nacos
# 默认账号密码 nacos/nacos
```

### 服务注册与发现

#### 服务提供者  nacos-provider

`bootstrap.yml`

设置 `nacos` 发现服务器地址

```yaml
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
server:
  port: 18080
```

主要依赖，下面消费者类似，就不多做陈述。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

`NacosProviderApplication`

主要添加 `@EnableDiscoveryClient` 注解开启 `Spring Cloud`的服务注册与发现，由于这里引入了`spring-cloud-starter-alibaba-nacos-discovery`模块，所以`Spring Cloud Common`中定义的那些与服务治理相关的接口将使用`Nacos`的实现。这点不论我们使用`Eureka`、`Consul`还是其他`Spring Cloud`整合的注册中心都一样，这也是`Spring Cloud`做了封装的好处所在。

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }
}
```

`EchoController`

简单Http接口，后面服务消费者可以调用

```java
@RestController
@RequestMapping("/echo")
public class EchoController {

    @RequestMapping(value = "/{string}", method = RequestMethod.GET)
    public String echo(@PathVariable String string) {
        return "Hello Nacos Discovery " + string;
    }
}
```

#### 服务消费者 nacos-consumer

`bootstrap.yml`

```yaml
spring:
  application:
    name: nacos-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
server:
  port: 18081
```

`NacosConsumerApplication`

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerApplication.class, args);
    }

    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

##### 消费方式

###### RestTemplate

这里注入 `RestTemplate` 作为服务的消费方式，调用服务提供者的接口

```java
@RestController
@RequestMapping("/echo")
public class EchoController {

    private final RestTemplate restTemplate;

    @Autowired
    LoadBalancerClient loadBalancerClient;

    @Autowired
    public EchoController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @RequestMapping(value = "/{str}", method = RequestMethod.GET)
    public String echo(@PathVariable String str) {

 		
        String url = "http://nacos-provider/echo/" + str;

        // 或者如果有负载可以通过spring cloud common中的负载均衡接口选取服务提供节点实现接口调用
        ServiceInstance serviceInstance = loadBalancerClient.choose("nacos-provider");
        url = serviceInstance.getUri() + "/echo/" + str;

        return restTemplate.getForObject(, String.class);
    }
}

```

可以看到，在定义`RestTemplate`的时候，增加了`@LoadBalanced`注解，在真正调用的时候，`Spring Cloud`会将请求拦截下来，然后通过负载均衡器选出节点，并替换服务名部分为具体的ip和端口，从而实现基于服务名的负载均衡调用。

###### WebClient

当然如果你使用 `reactive` 的话，可以使用 `WebClient`，跟 `RestTemplate` 差不多

```java
@EnableDiscoveryClient
@SpringBootApplication
public class TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class, args);
    }

    @Slf4j
    @RestController
    static class TestController {

        @Autowired
        private WebClient.Builder webClientBuilder;

        @GetMapping("/echo")
        public Mono<String> test() {
            Mono<String> result = webClientBuilder.build()
                    .get()
                    .uri("http://nacos-provider/echo/helloworld")
                    .retrieve()
                    .bodyToMono(String.class);
            return result;
        }
    }

    @Bean
    @LoadBalanced
    public WebClient.Builder loadBalancedWebClientBuilder() {
        return WebClient.builder();
    }

}
```

###### Feigon

上面介绍的 `RestTemplate` 和 `WebClient`都是` Spring` 自己封装的工具，下面介绍一个 `Netflix OSS` 中的成员，通过它可以更方便的定义和使用服务消费客户端。

增加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

代码示例

```java
@EnableDiscoveryClient
@SpringBootApplication
@EnableFeignClients
public class TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class, args);
    }

    @Slf4j
    @RestController
    static class TestController {

        @Autowired
        Client client;

        @GetMapping("/test")
        public String test() {
            String result = client.hello("didi");
            return "Return : " + result;
        }
    }


    @FeignClient("nacos-provider")
    interface Client {

        @GetMapping("/hello")
        String hello(@RequestParam(name = "name") String name);

    }

}
```

这里主要先通过`@EnableFeignClients`注解开启扫描`Spring Cloud Feign`客户端的功能；然后又创建一个`Feign`的客户端接口定义。使用`@FeignClient`注解来指定这个接口所要调用的服务名称，接口中定义的各个函数使用`Spring MVC`的注解就可以来绑定服务提供方的`REST`接口，比如下面就是绑定`nacos-provider`服务的`/hello`接口的例子。最后，在`Controller`中，注入了`Client`接口的实现，并调用`hello`方法来触发对服务提供方的调用。

### 配置中心

`Nacos`除了实现了服务的注册发现之外，还将配置中心功能整合在了一起。通过`Nacos`的配置管理功能，我们可以将整个架构体系内的所有配置都集中在`Nacos`中存储。

#### nacos-config.properties

到 `http://127.0.0.1:8848/nacos` 创建一个配置，

Group: `DEFAULT_GROUP`

Data ID: `nacos-config.properties`

```properties
useLocalCache:true
```

#### 构建应用

`bootstrap.yml`

设置 `nacos` 发现服务器地址，注意这里的 `spring.application.name` 跟 `nacos config dataid`  前缀对应

```yaml
spring:
  application:
    name: nacos-config
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
#        namespace: 871e0996-7eb8-474b-a446-2a79228e31d3
#        prefix: nacos-config-diy
#        file-extension: yaml
#        group: DEV_GROUP

server:
  port: 18090
```

依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

`ConfigController`

```java
@RestController
@RequestMapping("/config")
@RefreshScope
public class ConfigController {
    @Value("${useLocalCache:false}")
    private boolean useLocalCache;

    @RequestMapping("/get")
    public boolean get() {
        return useLocalCache;
    }
}
```

启动项目，运行

```shell
curl http://127.0.0.1:18090/config/get
true
```

可以发现返回 `true` 

跟我们在`nacos`配置管理界面配置一样。

我们在`nacos`配置管理界面设置`useLocalCache:false`

再请求一次，可以发现值已经变成 `false` 了，

可以动态刷新。

#### 加载规则

Group: `DEFAULT_GROUP`

Data ID: `nacos-config.properties`

拆解一下，主要有三个元素，它们与具体应用的配置内容对应关系如下：

- Data ID中的`nacos-config`：对应客户端的配置`spring.cloud.nacos.config.prefix`，默认值为`${spring.application.name}`，即：服务名
- Data ID中的`properties`：对应客户端的配置`spring.cloud.nacos.config.file-extension`，默认值为`properties`
- Group的值`DEFAULT_GROUP`：对应客户端的配置`spring.cloud.nacos.config.group`，默认值为`DEFAULT_GROUP`

这些都可以进行配置，比如我要加载

 Data ID: `nacos-config-diy.yaml`

Group: `DEV_GROUP`

```yaml
spring:
  application:
    name: nacos-config
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        prefix: nacos-config-diy
        file-extension: yaml
        group: DEV_GROUP

server:
  port: 18090
```

#### 多环境

一般一套代码可能在不同环境上跑，比如开发环境，测试环境，生产环境等，不同环境可能配置文件都不一样。通过 `nacos` 我们可以很方便地对多环境进行配置，一般来说，有如下三种途径。

##### spring.profile.active

最原始的配置规则为：`${spring.cloud.nacos.config.prefix}`-`${spring.profile.active}`.`${spring.cloud.nacos.config.file-extension}`

所以可以通过配置 `spring.profile.active` 来区分不同环境的配置文件

##### Group

因为有 `Group` 概念，所以也可以根据 `Group` 区分

##### Namespace

命名空间也可以很方便的区分不同环境，

官方建议的方式，通过`Namespace`来区分不同的环境，释放了`Group`的自由度，这样可以让`Group`的使用专注于做业务层面的分组管理。同时，`nacos`控制页面上对于`Namespace`也做了分组展示，不需要搜索，就可以隔离开不同的环境配置，非常易用。

在应用的配置文件中增加`Namespace`配置即可，注意这边配置的是`Namespace ID`，比如

```yacas
spring:
  application:
    name: nacos-config
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
		namespace: 871e0996-7eb8-474b-a446-2a79228e31d3

server:
  port: 18090
```

#### 加载多个配置

在`Spring Cloud`应用中通过使用`spring.cloud.nacos.config.ext-config`参数来配置要加载的这两个配置内容

```properties
spring.cloud.nacos.config.ext-config[0].data-id=actuator.properties
spring.cloud.nacos.config.ext-config[0].group=DEFAULT_GROUP
spring.cloud.nacos.config.ext-config[0].refresh=true
spring.cloud.nacos.config.ext-config[1].data-id=log.properties
spring.cloud.nacos.config.ext-config[1].group=DEFAULT_GROUP
spring.cloud.nacos.config.ext-config[1].refresh=true
```

#### 共享配置

通过上面加载多个配置的实现，实际上我们已经可以实现不同应用共享配置了。但是`nacos`中还提供了另外一个便捷的配置方式，比如下面的设置与上面使用的配置内容是等价的：

```properties
spring.cloud.nacos.config.shared-dataids=actuator.properties,log.properties
spring.cloud.nacos.config.refreshable-dataids=actuator.properties,log.properties
```

- `spring.cloud.nacos.config.shared-dataids`参数用来配置多个共享配置的`Data Id`，多个的时候用用逗号分隔
- `spring.cloud.nacos.config.refreshable-dataids`参数用来定义哪些共享配置的`Data Id`在配置变化时，应用中可以动态刷新，多个`Data Id`之间用逗号隔开。如果没有明确配置，默认情况下所有共享配置都不支持动态刷新

#### 配置加载的优先级

当我们加载多个配置的时候，如果存在相同的key时，我们需要深入了解配置加载的优先级关系。

在使用`nacos`配置的时候，主要有以下三类配置：

- A: 通过`spring.cloud.nacos.config.shared-dataids`定义的共享配置
- B: 通过`spring.cloud.nacos.config.ext-config[n]`定义的加载配置
- C: 通过内部规则（`spring.cloud.nacos.config.prefix`、`spring.cloud.nacos.config.file-extension`、`spring.cloud.nacos.config.group`这几个参数）拼接出来的配置

要弄清楚这几个配置加载的顺序，我们从日志中也可以很清晰的看到，我们可以做一个简单的实验：

```properties
spring.cloud.nacos.config.ext-config[0].data-id=actuator.properties
spring.cloud.nacos.config.ext-config[0].group=DEFAULT_GROUP
spring.cloud.nacos.config.ext-config[0].refresh=true

spring.cloud.nacos.config.shared-dataids=log.properties
spring.cloud.nacos.config.refreshable-dataids=log.properties
```

根据上面的配置，应用分别会去加载三类不同的配置文件，

启动应用，我们发现后面加载的配置会覆盖之前加载的配置，所以优先级关系是：`A < B < C`

### 集群部署

#### 数据改为mysql

`nacos` 默认单机运行模式，是用内嵌数据库`derby`，如想搞集群方式，那么就要将数据库改成`mysql`存储了，

配置`nacos`的`mysql`存储只需要下面三步：

**第一步**：安装数据库，版本要求：5.6.5+

**第二步**：初始化`mysql`数据库，数据库初始化文件：`nacos-mysql.sql`，该文件可以在`nacos`程序包下的`conf`目录下获得。

**第三步**：修改`conf/application.properties`文件，增加支持`mysql`数据源配置，添加（目前只支持`mysql`）数据源的地址、用户名和密码。配置样例如下：

```properties
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=
```

关于`nacos`数据的持久化实现，与其他的中间件相比，在实现上并没有采用分布式算法来解决一致性问题，而是采用了比较常规的集中化存储来实现。由于采用单一数据源的方式，直接解决了分布式一致性问题，所以从学习成本的角度上来说，`nacos`的实现原理会更容易被理解和接受。但是，从部署的负责度和硬件投入成本上来说，与`etcd`、`consul`、`zookeeper`这些通过算法方式解决一致性问题的中间件相比，就显得不足了。

同时，在引入`mysql`的存储时，由于多了一个中间件的存在，整个`nacos`系统的整体可用性一定是会所有下降的。所以为了弥补可用性的下降，在生产上`mysql`的高可用部署也是必须的，成本再次提高。不论如何提高，可用性都难以达到100%，所以这种方式，不论如何提升存储的可用性，理论上都会对`nacos`集群的自身可用性造成微小的下降。

以上思考主要从理论上，粗略讨论的，并没有经过详细的成本评估与可用性计算。所以，对于实际应用场景下，可能这些成本的增加和可用性的降低并没有那么多大的影响。同时，`Spring Cloud Alibaba`下使用的各开源组件都有对应的商业产品，在没有足够运维人力的团队下，使用对应的商业产品可能从各方面都会更加划算。

#### 集群配置

在`nacos`的`conf`目录下有一个`cluster.conf.example`，可以直接把`example`扩展名去掉来使用，也可以单独创建一个`cluster.conf`文件，然后打开将后续要部署的`nacos`实例地址配置在这里。

本文以在本地不同端点启动3个`nacos`服务端为例，可以如下配置：

```protobuf
127.0.0.1:8841
127.0.0.1:8842
127.0.0.1:8843
```

注意：这里的例子仅用于本地学习测试使用，实际生产环境必须部署在不同的节点上，才能起到高可用的效果。另外，`nacos`的集群需要3个或3个以上的节点，并且确保这三个节点之间是可以互相访问的。

然后用 `nginx` 或是其他服务器做一个负载均衡的代理入口

```nginx
upstream nacoserver {
    server 127.0.0.1:8841;
    server 127.0.0.1:8842;
    server 127.0.0.1:8843;
}

server {
    listen 8840;
    server_name localhost;
    
    # access_log
    
    # 定义nacos代理
    location /nacos/ {
        # 对应上面配置upstream配置，实现负载均衡
        proxy_pass http://nacoserver/nacos/;
    }
}
```

### Sdk

如果需要自动化部署，或是配置改变的时候有监听事件啥的，如果是`java` 开发，引入依赖即可

```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>${version}</version>
</dependency>
```

这里官方有详细的代码示例，这里不多做介绍。

### 相关链接

[nacos官网](https://nacos.io/zh-cn/)

[Alibaba之Nacos](https://windmt.com/2018/11/09/intro-to-spring-cloud-alibaba-nacos/)

[Springcloud 入门到精通/nacos系列博客](http://blog.didispace.com/spring-cloud-learning/)