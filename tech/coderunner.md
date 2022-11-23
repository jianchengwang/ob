---
title: coderunner
slug: coderunner
author: [jianchengwang]
date: 2021-05-25
excerpt: "这几天学习go语言，发现[The Go Playground](https://play.golang.org/) 可以在线编辑代码然后运行，其实很多语言都有提供这种在线代码编辑运行功能，比如[Scalafiddle](https://scalafiddle.io/)等等，这种免去了下载语言配置环境等，可以让用户尽情体验编码的乐趣。"
draft: false
tags: 
- code
---

这几天学习go语言，发现[The Go Playground](https://play.golang.org/) 可以在线编辑代码然后运行，其实很多语言都有提供这种在线代码编辑运行功能，比如[Scalafiddle](https://scalafiddle.io/)等等，这种免去了下载语言配置环境等，可以让用户尽情体验编码的乐趣。

之前还有个[github1s](http://github1s.com/)很好玩，可以在线预览Github仓库的项目，这个是基于vscode的web版实现，而Github被微软收购，又即将推出Github Codespaces，以后在线编程将成为一种常态。

又因为docker是go语言写的，通过go可以很容易调用docker客户端执行相应的命令，所以一时兴起就也想自己搞个coderunner，学以致用才是王道，从github仓库找到了[Elaina](https://github.com/wuhan005/Elaina)这个基于 Docker 的远程代码运行器的项目，所以就参照这个实现。

<!--more-->

## Docker Client

**下载依赖**

```shell
go get github.com/docker/docker/client
```
**初始化客户端**

```go
cli, err := client.NewClientWithOpts()
```

或者连接远程的docker

```go
cl, err := client.NewClient("tcp://192.168.64.1:2375", "", nil, nil)
```

**创建容器并启动**

```go
	var networkMode container.NetworkMode
	createContainerResp, err := t.cli.ContainerCreate(t.ctx,
		&container.Config{
			Image: t.RUNNER.Image,
			Tty:   true,
			WorkingDir: RuntimePath,
		},
		&container.HostConfig{
			NetworkMode: networkMode,
			Mounts: []mount.Mount{
				{
					Type:   mount.TypeBind,
					Source: t.SourceVolumePath,
					Target: RuntimePath,
				},
			},
			Resources: container.Resources{
				NanoCPUs: t.RUNNER.MaxCPUs * 1000000000,    // 0.0001 * CPU of cpu
				Memory:   t.RUNNER.MaxMemory * 1024 * 1024, // Minimum memory limit allowed is 6MB.
			},
		}, nil, nil, t.UUID)
	if err != nil {
		return nil, err
	}
	t.ContainerID = createContainerResp.ID

	// Clean containers and folder after executed.
	//defer t.Clean()

	if err := t.cli.ContainerStart(t.ctx, t.ContainerID, types.ContainerStartOptions{}); err != nil {
		return nil, err
	}
```

**执行容器里的命令并返回输出**

```go
idResponse, err := t.cli.ContainerExecCreate(t.ctx, t.ContainerID, types.ExecConfig{
		Env: t.RUNNER.Env,
		Cmd:[]string{"/bin/sh", "-c", cmd},
		Tty:true,
		AttachStderr:true,
		AttachStdout:true,
		AttachStdin:true,
		Detach:true,
	})
	if err != nil {
		return &Output{
			Error: true,
			Body:  err.Error(),
		}, nil
	}

	// 附加到上面创建的/bin/sh进程中
	hr, err := t.cli.ContainerExecAttach(t.ctx, idResponse.ID, types.ExecStartCheck{Detach: false, Tty: true})
	if err != nil {
		return &Output{
			Error: true,
			Body:  err.Error(),
		}, nil
	}
```

更多方法的使用可以查看[Go client for the Docker Engine API](https://link.zhihu.com/?target=https%3A//pkg.go.dev/github.com/docker/docker/client%3Futm_source%3Dgodoc) 

自此我们可以通过go操作docker容器

## 编程语言配置

不同编程语言，基于docker镜像，编译命令，执行命令等等都是不一样的，所以我们这里要对支持的编程语言进行相应初始配置信息

```go
type runner struct {
	Name     string
	Ext      string
	Image    string
	BuildCmd string
	RunCmd   string
	DefaultFileName string
	Env 	 []string
	MaxCPUs  int64
	MaxMemory int64
	Example  string
}

var LangRunners = []runner{
	{
		Name:     "go",
		Ext:      ".go",
		Image:    "golang:1.15-alpine",
		BuildCmd: "rm -rf go.mod && go mod init code-runner && go build -v .",
		RunCmd:   "./code-runner",
		Env: []string{"GOPROXY=https://goproxy.io,direct"},
		DefaultFileName: "code.go",
		MaxCPUs: 2,
		MaxMemory: 100,
		Example: "package main\n\n" +
			"import \"fmt\"\n\n" +
			"func main() {\n\n" +
			"  fmt.Println(\"hello world.\")\n\n" +
			"}",
	},
	{
		Name:     "python",
		Ext:      ".py",
		Image:    "python:3.9.1-alpine",
		BuildCmd: "",
		RunCmd:   "python3 code.py",
		Env: []string{},
		DefaultFileName: "code.py",
		MaxCPUs: 2,
		MaxMemory: 100,
		Example: "print(\"hello world.\")",
	},
	{
		Name:     "java",
		Ext:      ".java",
		Image:    "openjdk:8u232-jdk",
		BuildCmd: "javac code.java",
		RunCmd:   "java code",
		Env: []string{},
		DefaultFileName: "code.java",
		MaxCPUs: 2,
		MaxMemory: 100,
		Example: "class code {\n  public static void main(String[] args) {\n    System.out.println(\"Hello, World!\"); \n  }\n}",
	},
	{
		Name:     "javascript",
		Ext:      ".js",
		Image:    "node:lts-alpine",
		BuildCmd: "npm config set registry https://registry.npm.taobao.org",
		RunCmd:   "node code.js",
		Env: []string{},
		DefaultFileName: "code.js",
		MaxCPUs: 2,
		MaxMemory: 50,
		Example: "console.log(\"hello world.\");",
	},
	{
		Name:     "c",
		Ext:      ".c",
		Image:    "gcc:latest",
		BuildCmd: "gcc -v code.c -o code",
		RunCmd:   "./code",
		Env: []string{},
		DefaultFileName: "code.c",
		MaxCPUs: 2,
		MaxMemory: 50,
		Example: "#include <stdio.h>\nint main()\n{\n  printf(\"Hello, World!\");\n  return 0;\n}",
	},
}

```

这里简单说一下几个字段，

**Image**: 基于哪个镜像构建的，理论上只要镜像里包含指定编程语言的指令即可，你也可以构建自己的镜像，

**Env**： 容器的环境变量，这个是go语言一般要设置goproxy，不然基本下载不了依赖

**BuildCmd**: 构建命令，比如java代码，要先javac编译成class才能执行；或是npm设置淘宝镜像等代码执行前的前置操作；

**RunCmd**: 执行命令，运行代码

**DefaultFileName**： 默认的文件名，因为java的语言，文件名是要跟文件内容里的类名一致的，后期我们会拉取github仓库或者其他地方的源代码，这时候文件名保存就不能是默认的文件名了，这时候有一个默认文件名的字段，可以方便替换

后期，如果你需要扩展语言，直接编写下对应编程语言的配置即可，后期可能会考虑以配置文件的方式存储，目前是硬编码，

## 多人任务模式

可以存在多人操作，为了避免多个人操作同一个容器，所以这边抽象一个任务的结构体，

```go
type Task struct {
	ctx context.Context

	UUID   string
	RUNNER *runner

	cli         *client.Client
	ContainerID string

	SourceVolumePath string // Folder in Host: /home/<your_user>/coderunner/volume/<UUID>/
	fileName         string
}
```

每个任务都有一个唯一的`UUID`，这个会在程序运行的工作空间`APP_CONTAINER_PATH`创建以`UUID`命名的文件夹，并且不同任务都会创建不同的容器，保证不同任务执行互不影响。

因为每个任务的操作频率可能不止一次，所以这边并没有执行运行代码命令后就删除容器，因为容器的创建销毁是需要时间的，为了给用户良好的操作体验，所以这边每个任务只要有操作对应关联的docker容器便会保留一小时的有效时间，当然如果你切换其他编程语言的话，之前的编程语言的容器就会销毁。

## 拉取Github源码

一般源码都放在Github仓库上，而且`github1s`确实用着感觉看源代码很方便，所以这边就简单实现下，拉取Github仓库目录的源码，在左边形成文件列表，代码如下，

```go
user := strings.Split(gitPath, "/")[1]
	rep := strings.Split(gitPath, "/")[2]
	branch := strings.Split(gitPath, "/")[4]
	dir := gitPath[strings.Index(gitPath, branch) + len(branch) + 1:]
	gitUrl := "https://github.com/" + user + "/" + rep + ".git"
	fmt.Println(user, rep, branch, dir, gitUrl)

	var workDir = path.Join(hostGitPath, rep)
	if !utils.Exists(workDir) {
		fmt.Println(workDir, "not existed")
		err = os.MkdirAll(workDir, 0755)
		if err != nil {
			return r.MakeErrJSON(500, err.Error())
		}
		runGitCommand(workDir,"git", "init")
		runGitCommand(workDir,"git", "remote", "add", "origin", gitUrl)
		runGitCommand(workDir,"git", "config", "core.sparsecheckout", "true")
	} else {
		fmt.Println(workDir, "existed")
	}
	runGitCommand(workDir,"/bin/sh", "-c", "echo \"" + dir + "/*\">> .git/info/sparse-checkout")
	runGitCommand(workDir,"git", "pull", "origin", branch)
	runGitCommand(workDir,"git", "checkout")
	targetDir := path.Join(workDir, dir)
	fmt.Println(targetDir)
	fileMap := map[string]interface{}{}
	buildFileMap(targetDir, fileMap, fileExt)
	return r.MakeSuccessJSON(gin.H{
		"fileMap": fileMap,
	})
```

就是使用git命令，通过配置`.git/info/sparse-checkout`来检出特定的目录，避免有的项目太大，下载半天，然后遍历目录，找到所有文件后缀跟当前编程语言，以**文件名:文件内容**这样的map返回给前端，生成文件列表，点击对应文件名，会在右边编辑窗口显示对应的文件内容，

## 终端实现

有的代码，可能要安装依赖，所以这边也实现简单的终端，可以让用户在docker容器里面执行命令，这个就是跟运行代码调用方法一致，只是运行代码的命令是编程语言配置好，固定的，这边是获取前端用户输入的执行命令。

## 上传文件

既然提供了终端，用户可以操作容器里命令，那么上传文件到容器里也是很有必要的，比如用户上传一个jar包，然后在容器里运行`java -jar`执行，毕竟web界面提供的窗口有限，只适用单文件的源码，

## 语言特性

遇到的问题是java语言，文件名是要求跟类名一致的，并且，单文件源码的话不能包含包名，所以这里针对java语言会特殊处理，

保存文件到工作空间的时候直接用源码的文件名，并且去除源码里的包名，

```go
if len(fileName) == 0 || t.RUNNER.Name != "java" || path.Ext(fileName) != t.RUNNER.Ext  {
		fileName = "code" + t.RUNNER.Ext
	} else {
		if strings.HasPrefix(code, "package") {
			code = code[strings.Index(code, "\n"):]
		}
	}
```

编译命令跟运行命令也要进行相对应的调整，

```go
if len(fileName) > 0 && t.RUNNER.Name == "java" && path.Ext(fileName) == t.RUNNER.Ext {
				cmd = strings.ReplaceAll(t.RUNNER.BuildCmd, t.RUNNER.DefaultFileName, fileName) + " && "+ strings.ReplaceAll(t.RUNNER.RunCmd, "code", strings.ReplaceAll(fileName, t.RUNNER.Ext, ""))
			} else {
				cmd = t.RUNNER.BuildCmd + " && " + t.RUNNER.RunCmd
			}
```

## Docker部署

**Dockerfile**

```dockerfile
FROM alpine:latest

RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone

# install git - apt-get replace with apk
RUN apk update && \
    apk upgrade && \
    apk add --no-cache bash git openssh

RUN mkdir /etc/coderunner
WORKDIR /etc/coderunner

ADD coderunner /etc/coderunner

RUN chmod 655 /etc/coderunner/coderunner

ENTRYPOINT ["/etc/coderunner/coderunner"]
EXPOSE 8080
```

这里注意，要安装git软件，不然运行不了，

**go build & docker build**

```shell
set GOARCH=amd64
set GOOS=linux
go build -o coderunner main.go

docker build -t coderunner:v0.0.1 .
docker tag coderunner:v0.0.1 jianchengwang/coderunner
docker login
docker push jianchengwang/coderunner
```

这里根据自己的需求，打包成基于哪种架构的二进制文件，然后生成docker镜像即可，

**docker-compose**

```yaml
version: '3'
services:
  coderunner:
    image: jianchengwang/coderunner:latest
    ports:
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /root/coderunner:/root/coderunner
    environment:
      APP_URL: http://localhost:8080
      APP_PASSWORD: 12345678
      APP_CONTAINER_PATH: /root/coderunner
```

这里要注意将`APP_CONTAINER_PATH`要跟docker目录进行映射，`APP_URL`就是配置允许跨域的域名地址了，

**nginx proxy**

你如果使用nginx进行代理转发的话，要配置下跨域相关，否则可能导致跨域问题，

```nginx
 proxy_set_header    Host            $host;
 proxy_set_header    X-Real-IP       $remote_addr;
 proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
 location / {
    proxy_pass http://172.17.0.6:8902;
    add_header Access-Control-Allow-Origin *;
 }
```

## 后记

附上一张效果图，

![效果图1](https://github.com/jianchengwang/coderunner/raw/main//doc/assets/show1.png)

当然如果你感兴趣的话，也可以在线访问，[请戳](https://coderunner.jianchengwang.info/)

后续，可能会集成markdown，jsbin等，持续完善中...

## 相关链接

[jianchengwang/coderunner](https://github.com/jianchengwang/coderunner)

[Elaina](https://github.com/wuhan005/Elaina)

[Js-Encoder](https://github.com/Longgererer/JS-Encoder)

[github1s](https://github1s.com/)

[codemirror](https://codemirror.net/)

[cdnjs codemirror](https://cdnjs.com/libraries/codemirror)



