---
title: cosmos-evmos
createdAt: 2021-11-23
categories: 
- blockchain
- cosmos
tags: 
- cosmos
- crosschain
- evmos
draft: true

---

[Evmos](https://evmos.dev/)是一个可扩展的、高吞吐量的权益证明区块链，与以太坊完全兼容和互操作。它使用了运行在 [Tendermint Core ](https://github.com/tendermint/tendermint)共识引擎之上的 [Cosmos SDK](https://github.com/cosmos/cosmos-sdk/)实现。

本文是基于`evmos`实现简单的跨链代币互相转换的`demo`。

<!--more-->

## Quickd start

```shell
# install go
sudo apt-get install gcc make
wget https://dl.google.com/go/go1.17.3.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.3.linux-amd64.tar.gz
# install evmos
wget https://github.com/tharsis/evmos/releases/download/v0.2.0/evmos_0.2.0_Linux_x86_64.tar.gz
rm -rf /usr/local/evmos && tar -C /usr/local/evmos -xzf evmos_0.2.0_Linux_x86_64.tar.gz
# config env
sudo vim /etc/profile
export GOROOT=/usr/local/go
export GOPATH=/root/gopath
export GOPROXY=https://goproxy.cn,direct
export GO111MODULE=on
export EVMOSBIN=/usr/local/evmos/bin
PATH=$PATH:${GOROOT}/bin:${EVMOSBIN}
export PATH
source /etc/profile
```



## 相关链接

[cosmos-sdk](https://cosmos.network/)

[evmos](https://evmos.dev/)

[gravity-bridge](https://github.com/cosmos/gravity-bridge)
