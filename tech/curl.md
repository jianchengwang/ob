---
title: curl
slug: curl
author: [jianchengwang]
date: 2019-11-20
excerpt: "[curl](https://curl.haxx.se/)是一种命令行工具，作用是发出网络请求，然后获取数据，显示在标准输出`stdout`上面。最近学习 `oauth2` 经常需要用到此命令进行测试，所以这里简单记录下。"
draft: false
tags: 
- linux
---

[curl](https://curl.haxx.se/)是一种命令行工具，作用是发出网络请求，然后获取数据，显示在标准输出`stdout`上面。最近学习 `oauth2` 经常需要用到此命令进行测试，所以这里简单记录下。

### 基本命令

#### 获取网页源码

```shell
curl example.com
curl -o example.html example.com # 保存网页
curl -L example.com # 自动跳转
```

#### 显示头信息

```shell
curl -i www.baidu.com # http response跟网页
curl -I www.baidu.com # 只显示http response
```

#### 显示通信过程

```shell
curl -v www.baidu.com
curl --trace output.txt www.baidu.com
curl --trace-ascii output.txt www.baidu.com
```

#### 表单信息

GET方法直接拼接在网址后面，POST方法必须数据跟网址分开，

用到`-data`参数。

如果你的数据没有经过表单编码，还可以使用`--data-urlencode`编码

```shell
curl example.com/form?data=xxx
curl -X POST --data "data=xxx" example.com/form
curl -X POST --data-urlencode "data=April 1" example.com/form

```

#### HTTP动词

默认的HTTP动词是GET，使用 `-X` 参数可以支持其他动词。

```shell
curl -X DELETE example.com/1
curl -X POST --data "data=xxx" example.com/form
```

#### 文件上传

比如文件上传表单如下

```html
<form method="POST" enctype='multipart/form-data' action="upload.cgi">
    <input type=file name=upload>
    <input type=submit name=press value="OK">
</form>
```

```shell
curl --form upload=@localfilename --form press=OK[URL]
```

#### Referer字段

表示从哪里跳转过来

```shell
curl --referer http://example1.com http://example2.com
```

#### User Agent字段

表示客户端设备信息

```shell
curl --user-aget "[User Agent]" [URL]
```

#### Cookie

```shell
curl --cokkie "name=xxx" example.com
curl -c cookies http://example.com # 保存网址的cookie信息到指定文件cookies中
curl -b cookies http://example.com # 使用cookies这个文件作为cookie信息访问网址
```

#### 头部信息

```shell
curl --header "Content-Type:applicaion/json" http://example.com

curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsiand0cmVzb3VjZWlkIl0sInVzZXJfbmFtZSI6InJvb3QiLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNTY1NzA2NzQ3LCJhdXRob3JpdGllcyI6WyJST0xFX0FETUlOIl0sImp0aSI6IjEyNWQzYTJkLTQ1ODktNDc4YS04ZTRiLTdiN2UwZDM5MGYyZCIsImNsaWVudF9pZCI6Imp3dGNsaWVudGlkIn0.AHudCbS_rctS0YQuBSADPIPnMGm9a-2hguR_I0uQ19o" "localhost:8080/user/"

curl -X GET "http://localhost:8080/api/sys/user?page=0&size=10&id=1161156488191410178&sort=-createTime" -H "accept: */*" -H "Authorization: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJ3amMiLCJleHAiOjE1Njk4MjIyMTUsImlhdCI6MTU2OTczNTgxNX0.ORQOUeTeNORtwT6MyLy8SaOAwOmtUefyDk2korgtJPg" | python -m json.tool
```

#### HTTP认证

有些网域需要HTTP认证，

```shell
curl --user clientId:clientSecret example.com
curl -u name:password example.com
```

