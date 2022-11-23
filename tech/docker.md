---
title: docker
slug: docker
author: [jianchengwang]
date: 2020-09-10
excerpt: "Docker是一个开源的**容器引擎**，它可以帮助我们更快地交付应用。Docker可将应用程序和基础设施层隔离，并且能将基础设施当作程序一样进行管理。使用Docker，可更快地打包、测试以及部署应用程序，并可**减少从编写到部署运行代码的周期**。"
draft: false
tags: 
- cloud
---

Docker是一个开源的**容器引擎**，它可以帮助我们更快地交付应用。Docker可将应用程序和基础设施层隔离，并且能将基础设施当作程序一样进行管理。使用Docker，可更快地打包、测试以及部署应用程序，并可**减少从编写到部署运行代码的周期**。

### 准备工作

官网有详细的安装教程，然后配置下加速器即可，我这边使用[阿里云的`docker`镜像加速器](https://cr.console.aliyun.com/cn-qingdao/instances/mirrors)。

### 基本命令

#### 镜像命令

```shell
# 查询镜像
docker search nginx

# 下载镜像
docker pull nginx

# 列出镜像
docker images
docker images java
docker images java:8
docker images --digests
docker images --filter "dangling=true"   # 展示虚悬镜像

# 删除本地镜像
docker rmi hello-world
docker rmi -f $(docker images)

# 保存镜像
docker save busybox > busybox.tar
docker save --output busybox.tar busybox

# 加载镜像
docker load < busybox.tar.gz
docker load --input fedora.tar

# 构建镜像
docker build [OPTIONS] PATH | URL | -


```

#### 容器命令

##### 新建并启动容器[重要]

使用以下`docker run` 命令即可新建并启动一个容器。该命令是我们最常用的命令了，它有很多选项，下面笔者列举一些常用的选项。

① -d选项：表示后台运行

② -P选项：随机端口映射

③ -p选项：指定端口映射，有以下四种格式。

`ip:hostPort:containerPort`

`ip::containerPort`

`hostPort:containerPort`

`containerPort`

④ –network选项：指定网络模式，该选项有以下可选参数：

`–network=bridge`： 默认选项，表示连接到默认的网桥。

`–network=host`：容器使用宿主机的网络。

`–network=container:NAME_or_ID`：告诉Docker让新建的容器使用已有容器的网络配置。

`–network=none`：不配置该容器的网络，用户可自定义网络配置。

示例1：

```shell
docker run java /bin/echo 'Hello World'
```

这样终端会打印`Hello World`的字样，跟在本地直接执行`/bin/echo 'Hello World'` 一样。

示例2：

```shell
docker run -d -p 91:80 nginx
```

这样就能启动一个`Nginx`容器。在本例中，我们为`docker run`添加了两个参数，含义如下：

```shell
-d                           # 后台运行-p 宿主机端口:容器端口         # 开放容器端口到宿主机端口
```

访问[http://Docker宿主机IP:91/](http://xn--dockerip-ch0m892izn0a:91/) 

**TIPS**

需要注意的是，使用docker run命令创建容器时，会先检查本地是否存在指定镜像。如果本地不存在该名称的镜像，Docker就会自动从Docker Hub下载镜像并启动一个Docker容器。

##### 列出容器[重要]

使用`docker ps` 命令即可列出运行中的容器。执行该命令后，可看到类似于如下的表格。

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES784fd3b294d7        nginx               "nginx -g 'daemon off"   20 minutes ago      Up 2 seconds        443/tcp, 0.0.0.0:91->80/tcp   backstabbing_archimedes
```

如需列出所有容器（包括已停止的容器），可使用-a参数。

该表格包含了七列，含义如下：

① CONTAINER_ID：表示容器ID。

② IMAGE：表示镜像名称。

③ COMMAND：表示启动容器时运行的命令。

④ CREATED：表示容器的创建时间。

⑤ STATUS：表示容器运行的状态。Up表示运行中，Exited表示已停止。

⑥ PORTS：表示容器对外的端口号。

⑦ NAMES：表示容器名称。该名称默认由Docker自动生成，也可使用docker run命令的–name选项自行指定。

命令格式：

```shell
docker ps [OPTIONS]
```

参数：

| Name, shorthand | Default | Description                                          |
| :-------------- | :------ | :--------------------------------------------------- |
| `--all, -a`     | `false` | 列出所有容器，包括未运行的容器，默认只展示运行的容器 |
| `--filter, -f`  |         | 根据条件过滤显示内容                                 |
| `--format`      |         | 通过Go语言模板文件展示镜像                           |
| `--last, -n`    | `-1`    | 显示最近创建n个容器（包含所有状态）                  |
| `--latest, -l`  | `false` | 显示最近创建的容器（包含所有状态）                   |
| `--no-trunc`    | `false` | 不截断输出                                           |
| `--quiet, -q`   | `false` | 静默模式，只展示容器的编号                           |
| `--size, -s`    | `false` | 显示总文件大小                                       |

示例：

```
docker ps -n 5docker ps -a -q
```

##### 停止容器[重要]

使用`docker stop` 命令，即可停止容器。

命令格式：

```
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

参数：

| Name, shorthand | Default | Description                        |
| :-------------- | :------ | :--------------------------------- |
| `--time, -t`    | `10`    | 强制杀死容器前等待的时间，单位是秒 |

示例：

```
docker stop 784fd3b294d7
```

其中`784fd3b294d7` 是容器ID，当然也可使用`docker stop 容器名称` 来停止指定容器。

##### 强制停止容器[重要]

可使用`docker kill` 命令停止一个或更多运行着的容器。

命令格式：

```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

参数：

| Name, shorthand | Default | Description        |
| :-------------- | :------ | :----------------- |
| `--signal, -s`  | `KILL`  | 向容器发送一个信号 |

例如：

```
docker kill 784fd3b294d7
```

##### 启动已停止的容器[重要]

使用`docker run` 命令，即可新建并启动一个容器。对于已停止的容器，可使用`docker start` 命令来启动。

命令格式：

```
docker start [OPTIONS] CONTAINER [CONTAINER...]
```

参数：

| Name, shorthand     | Default | Description                   |
| :------------------ | :------ | :---------------------------- |
| `--attach, -a`      | `false` | 连接`STDOUT/STDERR`并转发信号 |
| `--checkpoint`      |         | 从该检查点还原                |
| `--checkpoint-dir`  |         | 使用自定义的检查点存储目录    |
| `--detach-keys`     |         | 覆盖断开容器的关键顺序        |
| `--interactive, -i` | `false` | 连接容器的`STDIN`             |

例如：

```
docker start 784fd3b294d7
```

##### 重启容器[重要]

可使用`docker restart` 命令来重启容器。该命令实际上是先执行了`docker stop` 命令，然后执行了`docker start` 命令。

命令格式：

```
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

参数：

| Name, shorthand | Default | Description                    |
| :-------------- | :------ | :----------------------------- |
| `--time, -t`    | `10`    | 关闭容器前等待的时间，单位是秒 |

##### 进入容器[重要]

某场景下，我们可能需要进入运行中的容器。

① 使用`docker attach` 命令进入容器。

例如：

```
docker attach 784fd3b294d7
```

很多场景下，使用`docker attach` 命令并不方便。当多个窗口同时attach到同一个容器时，所有窗口都会同步显示。同理，如果某个窗口发生阻塞，其他窗口也无法执行操作。

② 使用`nsenter` 进入容器

`nsenter`工具包含在`util-linux 2.23`或更高版本中。为了连接到容器，我们需要找到容器第一个进程的`PID`，可通过以下命令获取：

```
docker inspect --format "{{.State.Pid}}" $CONTAINER_ID
```

获得`PID`后，就可使用`nsenter`命令进入容器了：

```
nsenter --target "$PID" --mount --uts --ipc --net --pid
```

下面给出一个完整的例子：

```
[root@localhost ~]# docker psCONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES784fd3b294d7        nginx               "nginx -g 'daemon off"   55 minutes ago      Up 3 minutes        443/tcp, 0.0.0.0:91->80/tcp   backstabbing_archimedes[root@localhost ~]# docker inspect --format "{{.State.Pid}}" 784fd3b294d795492[root@localhost ~]# nsenter --target 95492 --mount --uts --ipc --net --pidroot@784fd3b294d7:/#
```

读者也可将以上两条命令封装成一个Shell，从而简化进入容器的过程。

③ docker exec

```
docker exec -it 容器id /bin/bash
```

##### 删除容器[重要]

使用`docker rm` 命令即可删除指定容器。

命令格式

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

参数：

| Name, shorthand | Default | Description                               |
| :-------------- | :------ | :---------------------------------------- |
| `--force, -f`   | `false` | 通过`SIGKILL`信号强制删除正在运行中的容器 |
| `--link, -l`    | `false` | 删除容器间的网络连接                      |
| `--volumes, -v` | `false` | 删除与容器关联的卷                        |

例1：删除指定容器。

```
docker rm 784fd3b294d7
```

该命令只能删除已停止的容器，如需删除正在运行的容器，可使用-f参数。

例2：删除所有的容器。

```
docker rm -f $(docker ps -a -q)
```

##### 导出容器

将容器导出成一个压缩包文件。

命令格式：

```
docker export [OPTIONS] CONTAINER
```

参数：

| Name, shorthand | Default | Description                |
| :-------------- | :------ | :------------------------- |
| `--output, -o`  |         | 将内容写到文件而非`STDOUT` |

示例：

```
docker export red_panda > latest.tardocker export --output="latest.tar" red_panda
```

##### 导入容器

使用`docker import` 命令即可从归档文件导入内容并创建镜像。

命令格式：

```
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```

参数：

| Name, shorthand | Default | Description                        |
| :-------------- | :------ | :--------------------------------- |
| `--change, -c`  |         | 将`Dockerfile`指令应用到创建的镜像 |
| `--message, -m` |         | 为导入的镜像设置提交信息           |

示例：

```
docker import nginx2.tar nginx
```

### Dockerfile

#### 基本指令

##### ADD 复制文件

ADD复制文件

格式为：

- `ADD <src>... <dest>`
- `ADD ["<src>",... "<dest>"]`

从`src`目录复制文件到容器的`dest`。其中`src`可以是`Dockerfile`所在目录的相对路径，也可以是一个URL，还可以是一个压缩包。

除了要复制解压文件，一般不建议使用，使用`RUN`跟`COPY`命令就可以很好替代。

```dockerfile
ADD microservice-discovery-eureka-0.0.1-SNAPSHOT.jar app.jar
```

##### ARG 设置构建参数

设置构建参数，类似于`ENV`。和`ENV`不同的是，`ARG`设置的是构建时的环境变量，在容器运行时是不会存在这些变量的。

格式为：

- `ARG <name>[=<default value>]`

```dockerfile
ARG user1=someuser
```

##### CMD 容器启动命令

用于为执行容器提供默认值。每个`Dockerfile`只有一个`CMD`命令，如果指定了多个`CMD`命令，那么只有最后一条会被执行，如果启动容器的时候指定了运行的命令，则会覆盖掉`CMD`指定的命令。

支持三种格式：

`CMD ["executable","param1","param2"]` (推荐使用，会解析成json数组，所以要记得要用**双引号**)

`CMD ["param1","param2"]` (为`ENTRYPOINT`指令提供预设参数)

`CMD command param1 param2` (在shell中执行)

示例：

```dockerfile
CMD echo "This is a test." | wc -
```

##### COPY 复制文件

复制文件，格式为：

- `COPY <src>... <dest>`
- `COPY ["<src>",... "<dest>"]`

复制本地端的`src`到容器的`dest`。`COPY`指令和`ADD`指令类似，`COPY`不支持URL和压缩包。

##### ENTRYPOINT 入口点

格式为：

- `ENTRYPOINT ["executable", "param1", "param2"]`
- `ENTRYPOINT command param1 param2`

`ENTRYPOINT`和`CMD`指令的目的一样，都是指定`Docker`容器启动时执行的命令，可多次设置，但只有最后一个有效。`ENTRYPOINT`不可被重写覆盖。

`ENTRYPOINT`、`CMD`区别：http://blog.csdn.net/newjueqi/article/details/51355510

##### ENV 设置环境变量

用于设置环境变量，格式为：

- `ENV <key> <value>`
- `ENV <key>=<value> ...`

示例：

```dockerfile
ENV JAVA_HOME /path/to/java
```

##### EXPOSE 声明暴露的端口

用于声明在运行时容器提供服务的端口，格式为：

- `EXPOSE <port> [<port>...]`

需要注意的是，这只是一个声明，运行时并不会因为该声明就打开相应端口。该指令的作用主要是帮助镜像使用者理解该镜像服务的守护端口；其次是当运行时使用随机映射时，会自动映射`EXPOSE`的端口。示例：

```dockerfile
# 声明暴露一个端口示例
EXPOSE port1
# 相应的运行容器使用的命令
docker run -p port1 image
# 也可使用-P选项启动
docker run -P image
# 声明暴露多个端口示例
EXPOSE port1 port2 port3
# 相应的运行容器使用的命令
docker run -p port1 -p port2 -p port3 image
# 也可指定需要映射到宿主机器上的端口号  
docker run -p host_port1:port1 -p host_port2:port2 -p host_port3:port3 image
```

##### FROM 指定基础镜像

指定基础镜像，`FROM`指令有点像`Java`里面的`extend`关键字。需要注意的是，`FROM`指令必须指定且需要写在其他指令之前。`FROM`指令后的所有指令都依赖于该指令所指定的镜像。

支持三种格式：

- `FROM <image>`
- `FROM <image>:<tag>`
- `FROM <image>@<digest>`

##### LABEL 为镜像添加元数据

为镜像添加元数据。

格式为：

- `LABEL <key>=<value> <key>=<value> <key>=<value> ...`

示例：

```dockerfile
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \that label-values can span multiple lines."
```

##### MAINTAINER 指定维护者的信息（已过时）

MAINTAINER指令用于指定维护者的信息，用于为`Dockerfile`署名。

格式为：

- `MAINTAINER <name>`

示例：

```dockerfile
MAINTAINER 周立<eacdy0000@126.com>
```

注：该指令已过时，建议使用如下形式：

```dockerfile
LABEL maintainer="SvenDowideit@home.org.au"
```

##### RUN 执行命令

该指令支持两种格式：

- `RUN <command>`
- `RUN ["executable", "param1", "param2"]`

`RUN <command>` 在`shell`终端中运行，在`Linux`中默认是`/bin/sh -c` ，在`Windows`中是 `cmd /s /c`，使用这种格式，就像直接在命令行中输入命令一样。
`RUN ["executable", "param1", "param2"]` 使用exec执行，这种方式类似于函数调用。指定其他终端可以通过该方式操作，例如：`RUN ["/bin/bash", "-c", "echo hello"]` ，该方式必须使用双引号[“]而不能使用单引号[‘]，因为该方式会被转换成一个`JSON` 数组。

##### USER 设置用户

该指令用于设置启动镜像时的用户或者`UID`，写在该指令后的`RUN`、`CMD`以及`ENTRYPOINT`指令都将使用该用户执行命令。

格式为：

- `USER 用户名`

示例：

```
USER daemon
```

##### VOLUME 指定挂载点

该指令使容器中的一个目录具有持久化存储的功能，该目录可被容器本身使用，也可共享给其他容器。当容器中的应用有持久化数据的需求时可以在`Dockerfile`中使用该指令。格式为：

- `VOLUME ["/data"]`

示例：

```
VOLUME /data
```

使用示例：

```
FROM nginxVOLUME /tmp
```

当该`Dockerfile`被构建成镜像后，`/tmp`目录中的数据即使容器关闭也依然存在。如果另一个容器也有持久化的需求，并且想使用以上容器`/tmp`目录中的内容，则可使用如下命令启动容器：

```
docker run -volume-from 容器ID 镜像名称  # 容器ID是di一个容器的ID，镜像是第二个容器所使用的镜像。
```

##### WORKDIR 指定工作目录

格式为：

- `WORKDIR /path/to/workdir`

切换目录指令，类似于`cd`命令，写在该指令后的`RUN`，`CMD`以及`ENTRYPOINT`指令都将该目录作为当前目录，并执行相应的命令。

##### 其他

`Dockerfile`还有一些其他的指令，例如`STOPSINGAL`、`HEALTHCHECK`、`SHELL`等。由于并不是很常用，本书不作赘述。有兴趣的读者可前往https://docs.docker.com/engine/reference/builder/ 扩展阅读。

**CMD/ENTRYPOINT/RUN区别**

参考：https://segmentfault.com/q/1010000000417103

#### 实战



### 相关链接

[Docker官方网站](https://www.docker.com/)

[Docker GitHub](https://github.com/docker/docker)

[Docker 入门到实践](https://yeasy.gitbook.io/docker_practice)