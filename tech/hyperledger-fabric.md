---
title: hyperledger-fabric
createdAt: 2021-09-22
categories: 
- blockchain
- fabric
tags: 
- blockchain
- fabric

---

[hyperledger-fabric](https://github.com/hyperledger/fabric) 是一个企业级的分布式账本框架，提供了基于java,go,nodejs等通用编程语言的sdk开发智能合约(**smart contract**)，本文主要介绍如何搭建要给超级账本的网络，并且基于go语言开发简单的智能合约。

<!--more-->

## basic concept

hyperledger fabric符合上面说过的区块链的所有特性。我们必须先了解它的一些概念，才能进一步理解其架构设计。由于英文资料居多，所以这些概念我都以英文描述为准：

- chaincode：智能合约，上文已提到。每个chaincode可提供多个不同的调用命令。
- transaction：交易，每条指令都是一次交易。
- world state：对同一个key的多次交易形成的最终value，就是世界状态。
- endorse：背书。金融上的意义为：指持票人为将票据权利转让给他人或者将一定的票据权利授予他人行使，而在票据背面或者粘单上记载有关事项并签章的行为。通常我们引申为对某个事情负责。在我们的共识机制的投票环节里，背书意味着参与投票。
- endorsement policy：背书策略。由智能合约chaincode选择哪些peer节点参与到背书环节来。
- peer：存放区块链数据的结点，同时还有endorse和commit功能。
- channel：私有的子网络，事实上是为了隔离不同的应用，一个channel可含有一批chaincode。
- PKI：Public Key Infrastructure，一种遵循标准的利用公钥加密技术为电子商务的开展提供一套安全基础平台的技术和规范。
- MSP：Membership Service Provider，联盟链成员的证书管理，它定义了哪些RCA以及ICA在链里是可信任的，包括定义了channel上的合作者。
- org：orginazation，管理一系列合作企业的组织。

更多介绍可以参照 https://hyperledger-fabric.readthedocs.io/en/latest/key_concepts.html

## install & test-network

### install

参照 https://hyperledger-fabric.readthedocs.io/en/latest/getting_started.html

```shell
# 安装前置软件，这里不多赘述，自行参考https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html
sudo apt-get install gcc
wget https://golang.org/dl/go1.16.5.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.5.linux-amd64.tar.gz
# 测试目录
mkdir fabric-test
cd fabric-test
# 如果可以使用代理
curl -sSL https://bit.ly/2ysbOFE | bash -s
# 否则自行下载，其实就是一个shell脚本
wget https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh
bash ./bootstrap.sh
# 如果你二进制文件也下载不下来，可以用可以代理的机器先下载，然后把脚本里的downlaod方法注释掉即可，然后把下载的二进制文件解压得到bin,config文件夹，复制到官方的示例项目fabric-simples即可
bash ./bootstrap.sh
wget https://github.com/hyperledger/fabric/releases/download/v2.3.2/hyperledger-fabric-linux-amd64-2.3.3.tar.gz
wget https://github.com/hyperledger/fabric-ca/releases/download/v1.5.0/hyperledger-fabric-ca-linux-amd64-1.5.2.tar.gz
tar -zxvf https://github.com/hyperledger/fabric/releases/download/v2.3.2/hyperledger-fabric-linux-amd64-2.3.3.tar.gz
tar -zxvf https://github.com/hyperledger/fabric-ca/releases/download/v1.5.0/hyperledger-fabric-ca-linux-amd64-1.5.2.tar.gz
mv bin ./fabric-samples
mv config ./fabric-samples
# 配置环境变量
sudo vim /etc/profile
# config go
export GOROOT=/usr/local/go
export GOPATH=/root/gopath
export GOPROXY=https://goproxy.cn,direct
export GO111MODULE=on
# config fabric
export FABRICBIN=/root/workspace/todo-blockchain/fabric-test/fabric-samples/bin
# config path
PATH=$PATH:${GOROOT}/bin:${FABRICBIN}
export PATH
source /etc/profile
```

### test-network

参照 https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html

```shell
cd fabric-samples/test-network
# up network
./network.sh up

# create channel
./network.sh createChannel # 默认创建mychannel
./network.sh createChannel -c channel1

# starting a chaincode on the channel
cd ./asset-transfer-basic/chaincode-go
go mod vendor
cd ../test-network/
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go
```

`-cnn`: 指定通道名称，默认`basic` 表示`mychannel`

`-ccp`: 指定链码路径

`-ccl`: 指定链码语言

```shell
# point to the core.yaml file core.yml
export PATH=${PWD}/../bin:$PATH
export FABRIC_CFG_PATH=$PWD/../config/
# Environment variables for Org1
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051

# initialize the ledger
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"InitLedger","Args":[]}'
# get list of aseets
peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}'
# transferAsset，将资产asset6转移给Christopher
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"TransferAsset","Args":["asset6","Christopher"]}'

# query Org2
# Environment variables for Org2
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org2MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
export CORE_PEER_ADDRESS=localhost:9051
# query asserts，发现资产asset6已经转移给Christopher
peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","asset6"]}'

# down network
cd $PWD # fix sometime win10 wls2 docker-compose cant work
./network.sh down
```

## deploying a production network

```shell
mkdir fabirc-test/twonodes
cd twonodes
```

### config host

```shell
sudo vim /etc/hosts
127.0.0.1	orderer.example.com
127.0.0.1	peer0.org1.example.com
127.0.0.1	peer0.org2.example.com
```

### cryptogen ca files

```shell
cryptogen template > cryto-config.yaml
cryptogen generate --config=crypto-config.yaml
```

```yaml

# ---------------------------------------------------------------------------
# "OrdererOrgs" - Definition of organizations managing orderer nodes
# ---------------------------------------------------------------------------
OrdererOrgs:
  # ---------------------------------------------------------------------------
  # Orderer
  # ---------------------------------------------------------------------------
  - Name: Orderer
    Domain: example.com
    EnableNodeOUs: true

    # ---------------------------------------------------------------------------
    # "Specs" - See PeerOrgs below for complete description
    # ---------------------------------------------------------------------------
    Specs:
      - Hostname: orderer

# ---------------------------------------------------------------------------
# "PeerOrgs" - Definition of organizations managing peer nodes
# ---------------------------------------------------------------------------
PeerOrgs:
  # ---------------------------------------------------------------------------
  # Org1
  # ---------------------------------------------------------------------------
  - Name: Org1
    Domain: org1.example.com
    EnableNodeOUs: true

    # ---------------------------------------------------------------------------
    # "CA"
    # ---------------------------------------------------------------------------
    # Uncomment this section to enable the explicit definition of the CA for this
    # organization.  This entry is a Spec.  See "Specs" section below for details.
    # ---------------------------------------------------------------------------
    # CA:
    #    Hostname: ca # implicitly ca.org1.example.com
    #    Country: US
    #    Province: California
    #    Locality: San Francisco
    #    OrganizationalUnit: Hyperledger Fabric
    #    StreetAddress: address for org # default nil
    #    PostalCode: postalCode for org # default nil

    # ---------------------------------------------------------------------------
    # "Specs"
    # ---------------------------------------------------------------------------
    # Uncomment this section to enable the explicit definition of hosts in your
    # configuration.  Most users will want to use Template, below
    #
    # Specs is an array of Spec entries.  Each Spec entry consists of two fields:
    #   - Hostname:   (Required) The desired hostname, sans the domain.
    #   - CommonName: (Optional) Specifies the template or explicit override for
    #                 the CN.  By default, this is the template:
    #
    #                              "{{.Hostname}}.{{.Domain}}"
    #
    #                 which obtains its values from the Spec.Hostname and
    #                 Org.Domain, respectively.
    #   - SANS:       (Optional) Specifies one or more Subject Alternative Names
    #                 to be set in the resulting x509. Accepts template
    #                 variables {{.Hostname}}, {{.Domain}}, {{.CommonName}}. IP
    #                 addresses provided here will be properly recognized. Other
    #                 values will be taken as DNS names.
    #                 NOTE: Two implicit entries are created for you:
    #                     - {{ .CommonName }}
    #                     - {{ .Hostname }}
    # ---------------------------------------------------------------------------
    # Specs:
    #   - Hostname: foo # implicitly "foo.org1.example.com"
    #     CommonName: foo27.org5.example.com # overrides Hostname-based FQDN set above
    #     SANS:
    #       - "bar.{{.Domain}}"
    #       - "altfoo.{{.Domain}}"
    #       - "{{.Hostname}}.org6.net"
    #       - 172.16.10.31
    #   - Hostname: bar
    #   - Hostname: baz

    # ---------------------------------------------------------------------------
    # "Template"
    # ---------------------------------------------------------------------------
    # Allows for the definition of 1 or more hosts that are created sequentially
    # from a template. By default, this looks like "peer%d" from 0 to Count-1.
    # You may override the number of nodes (Count), the starting index (Start)
    # or the template used to construct the name (Hostname).
    #
    # Note: Template and Specs are not mutually exclusive.  You may define both
    # sections and the aggregate nodes will be created for you.  Take care with
    # name collisions
    # ---------------------------------------------------------------------------
    Template:
      Count: 1
      # Start: 5
      # Hostname: {{.Prefix}}{{.Index}} # default
      # SANS:
      #   - "{{.Hostname}}.alt.{{.Domain}}"

    # ---------------------------------------------------------------------------
    # "Users"
    # ---------------------------------------------------------------------------
    # Count: The number of user accounts _in addition_ to Admin
    # ---------------------------------------------------------------------------
    Users:
      Count: 1

  # ---------------------------------------------------------------------------
  # Org2: See "Org1" for full specification
  # ---------------------------------------------------------------------------
  - Name: Org2
    Domain: org2.example.com
    EnableNodeOUs: true
    Template:
      Count: 1
    Users:
      Count: 1
```



### configtx.yaml

```shell
cp ../fabric-samples/test-network/configtx/configtx.yaml .
# 更改配置文件的证书路径跟profilefs
vim configtx.yaml
```
```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:

    # SampleOrg defines an MSP using the sampleconfig.  It should never be used
    # in production but may be used as a template for other definitions
    - &OrdererOrg
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: OrdererOrg

        # ID to load the MSP definition as
        ID: OrdererMSP

        # MSPDir is the filesystem path which contains the MSP configuration
        MSPDir: ./crypto-config/ordererOrganizations/example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Writers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Admins:
                Type: Signature
                Rule: "OR('OrdererMSP.admin')"

        OrdererEndpoints:
            - orderer.example.com:7050

        

    - &Org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: Org1MSP

        # ID to load the MSP definition as
        ID: Org1MSP

        MSPDir: ./crypto-config/peerOrganizations/org1.example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.peer', 'Org1MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org1MSP.admin')"
            Endorsement:
                Type: Signature
                Rule: "OR('Org1MSP.peer')"    
        
        AnchorPeers:
            - Host: peer0.org1.example.com
              Port: 7051
           

    - &Org2
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: Org2MSP

        # ID to load the MSP definition as
        ID: Org2MSP

        MSPDir: ./crypto-config/peerOrganizations/org2.example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.peer', 'Org2MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org2MSP.admin')"
            Endorsement:
                Type: Signature
                Rule: "OR('Org2MSP.peer')"   

        AnchorPeers:
            - Host: peer0.org2.example.com
              Port: 9051

################################################################################
#
#   SECTION: Capabilities
#
#   - This section defines the capabilities of fabric network. This is a new
#   concept as of v1.1.0 and should not be utilized in mixed networks with
#   v1.0.x peers and orderers.  Capabilities define features which must be
#   present in a fabric binary for that binary to safely participate in the
#   fabric network.  For instance, if a new MSP type is added, newer binaries
#   might recognize and validate the signatures from this type, while older
#   binaries without this support would be unable to validate those
#   transactions.  This could lead to different versions of the fabric binaries
#   having different world states.  Instead, defining a capability for a channel
#   informs those binaries without this capability that they must cease
#   processing transactions until they have been upgraded.  For v1.0.x if any
#   capabilities are defined (including a map with all capabilities turned off)
#   then the v1.0.x peer will deliberately crash.
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.
    # Set the value of the capability to true to require it.
    Channel: &ChannelCapabilities
        # V2_0 capability ensures that orderers and peers behave according
        # to v2.0 channel capabilities. Orderers and peers from
        # prior releases would behave in an incompatible way, and are therefore
        # not able to participate in channels at v2.0 capability.
        # Prior to enabling V2.0 channel capabilities, ensure that all
        # orderers and peers on a channel are at v2.0.0 or later.
        V2_0: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # used with prior release peers.
    # Set the value of the capability to true to require it.
    Orderer: &OrdererCapabilities
        # V2_0 orderer capability ensures that orderers behave according
        # to v2.0 orderer capabilities. Orderers from
        # prior releases would behave in an incompatible way, and are therefore
        # not able to participate in channels at v2.0 orderer capability.
        # Prior to enabling V2.0 orderer capabilities, ensure that all
        # orderers on channel are at v2.0.0 or later.
        V2_0: true

    # Application capabilities apply only to the peer network, and may be safely
    # used with prior release orderers.
    # Set the value of the capability to true to require it.
    Application: &ApplicationCapabilities
        # V2_0 application capability ensures that peers behave according
        # to v2.0 application capabilities. Peers from
        # prior releases would behave in an incompatible way, and are therefore
        # not able to participate in channels at v2.0 application capability.
        # Prior to enabling V2.0 application capabilities, ensure that all
        # peers on channel are at v2.0.0 or later.
        V2_0: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################
Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Application policies, their canonical path is
    #   /Channel/Application/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        LifecycleEndorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"
        Endorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"

    Capabilities:
        <<: *ApplicationCapabilities
################################################################################
#
#   SECTION: Orderer
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for orderer related parameters
#
################################################################################
Orderer: &OrdererDefaults

    # Orderer Type: The orderer implementation to start
    OrdererType: etcdraft
    # Addresses used to be the list of orderer addresses that clients and peers
    # could connect to.  However, this does not allow clients to associate orderer
    # addresses and orderer organizations which can be useful for things such
    # as TLS validation.  The preferred way to specify orderer addresses is now
    # to include the OrdererEndpoints item in your org definition
    Addresses:
        - orderer.example.com:7050

    EtcdRaft:
        Consenters:
        - Host: orderer.example.com
          Port: 7050
          ClientTLSCert: ./crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
          ServerTLSCert: ./crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt

    # Batch Timeout: The amount of time to wait before creating a batch
    BatchTimeout: 2s

    # Batch Size: Controls the number of messages batched into a block
    BatchSize:

        # Max Message Count: The maximum number of messages to permit in a batch
        MaxMessageCount: 10

        # Absolute Max Bytes: The absolute maximum number of bytes allowed for
        # the serialized messages in a batch.
        AbsoluteMaxBytes: 99 MB

        # Preferred Max Bytes: The preferred maximum number of bytes allowed for
        # the serialized messages in a batch. A message larger than the preferred
        # max bytes will result in a batch larger than preferred max bytes.
        PreferredMaxBytes: 512 KB

    # Organizations is the list of orgs which are defined as participants on
    # the orderer side of the network
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Orderer policies, their canonical path is
    #   /Channel/Orderer/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        # BlockValidation specifies what signatures must be included in the block
        # from the orderer for the peer to validate it.
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"

################################################################################
#
#   CHANNEL
#
#   This section defines the values to encode into a config transaction or
#   genesis block for channel related parameters.
#
################################################################################
Channel: &ChannelDefaults
    # Policies defines the set of policies at this level of the config tree
    # For Channel policies, their canonical path is
    #   /Channel/<PolicyName>
    Policies:
        # Who may invoke the 'Deliver' API
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Who may invoke the 'Broadcast' API
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # By default, who may modify elements at this config level
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    # Capabilities describes the channel level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ChannelCapabilities

################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################
Profiles:
    TwoOrgsOrdererGenesis:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *Org1
                    - *Org2

    TwoOrgsChannel:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org1
                - *Org2
            Capabilities:
                <<: *ApplicationCapabilities

```

```shell
# 配置FABRIC_CFG_PATH为当前目录
export FABRIC_CFG_PATH=$PWD
# 创建
mkdir channel-artifacts
# 生成创世块文件
configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block -channelID fabric-channel
# 生成通道文件
configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID mychannel
# 生成Org1的锚节点文件
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID mychannel -asOrg Org1MSP
# 生成Org2的锚节点文件
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID mychannel -asOrg Org2MSP
```

### docker-compose peer

```shell
cp ../fabric-samples/test-network/docker/docker-compose-test-net.yaml ./docker-compose.yaml
vim docker-compose.yaml

```

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '3.7'

volumes:
  orderer.example.com:
  peer0.org1.example.com:
  peer0.org2.example.com:

networks:
  test:
    name: twonodes_test

services:
  orderer.example.com:
    container_name: orderer.example.com
    image: hyperledger/fabric-orderer:latest
    labels:
      service: hyperledger-fabric
    environment:
      - FABRIC_LOGGING_SPEC=INFO
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_LISTENPORT=7050
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      # enabled TLS
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_GENERAL_BOOTSTRAPMETHOD=none
      # - ORDERER_GENERAL_GENESISMETHOD=file
      # - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_CHANNELPARTICIPATION_ENABLED=true
      - ORDERER_ADMIN_TLS_ENABLED=true
      - ORDERER_ADMIN_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_ADMIN_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_ADMIN_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_ADMIN_TLS_CLIENTROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_ADMIN_LISTENADDRESS=0.0.0.0:7053
      - ORDERER_OPERATIONS_LISTENADDRESS=0.0.0.0:17050
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/msp:/var/hyperledger/orderer/msp
        - ./crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/:/var/hyperledger/orderer/tls
        - orderer.example.com:/var/hyperledger/production/orderer
    ports:
      - 7050:7050
      - 7053:7053
      - 17050:17050
    networks:
      - test

  peer0.org1.example.com:
    container_name: peer0.org1.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=twonodes_test
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=false
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variables
      - CORE_PEER_ID=peer0.org1.example.com
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
      - CORE_PEER_LISTENADDRESS=0.0.0.0:7051
      - CORE_PEER_CHAINCODEADDRESS=peer0.org1.example.com:7052
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:7052
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org1.example.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org1.example.com:7051
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:17051
    volumes:
        - /var/run/docker.sock:/host/var/run/docker.sock
        - ./crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp:/etc/hyperledger/fabric/msp
        - ./crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:/etc/hyperledger/fabric/tls
        - peer0.org1.example.com:/var/hyperledger/production
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    ports:
      - 7051:7051
      - 17051:17051
    networks:
      - test

  peer0.org2.example.com:
    container_name: peer0.org2.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=twonodes_test
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=false
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variables
      - CORE_PEER_ID=peer0.org2.example.com
      - CORE_PEER_ADDRESS=peer0.org2.example.com:9051
      - CORE_PEER_LISTENADDRESS=0.0.0.0:9051
      - CORE_PEER_CHAINCODEADDRESS=peer0.org2.example.com:9052
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:9052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org2.example.com:9051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:9051
      - CORE_PEER_LOCALMSPID=Org2MSP
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:19051
    volumes:
        - /var/run/docker.sock:/host/var/run/docker.sock
        - ./crypto-config/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp:/etc/hyperledger/fabric/msp
        - ./crypto-config/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls:/etc/hyperledger/fabric/tls
        - peer0.org2.example.com:/var/hyperledger/production
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    ports:
      - 9051:9051
      - 19051:19051
    networks:
      - test

  cli1:
    container_name: cli1
    image: hyperledger/fabric-tools:latest
    labels:
      service: hyperledger-fabric
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli1
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
        - ./chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
    depends_on:
      - peer0.org1.example.com
      - peer0.org2.example.com
    networks:
      - test
  
  cli2:
    container_name: cli2
    image: hyperledger/fabric-tools:latest
    labels:
      service: hyperledger-fabric
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli2
      - CORE_PEER_LOCALMSPID=Org2MSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
      - CORE_PEER_ADDRESS=peer0.org2.example.com:9051
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
        - ./chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
    depends_on:
      - peer0.org1.example.com
      - peer0.org2.example.com
    networks:
      - test

```

```shell
docker-compose down --remove-orphans
docker volume prune
docker-compose up -d
```

### peer channel

[官方文档-peerchannel](https://hyperledger-fabric.readthedocs.io/en/release-2.3/commands/peerchannel.html)

#### create channel

```shell
docker exec -it cli1 bash
# 创建通道
peer channel create -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/channel.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/msp/tlscacerts/tlsca.example.com-cert.pem
# 退出容器
exit
# 复制到cli2容器
docker cp cli1:/opt/gopath/src/github.com/hyperledger/fabric/peer/mychannel.block ./
docker cp ./mychannel.block cli2:/opt/gopath/src/github.com/hyperledger/fabric/peer
rm -rf mychannel.block
```

#### join channel

```shell
# Org1 join
docker exec -it cli1 bash
peer channel join -b mychannel.block
exit
# Org2 join
docker exec -it cli2 bash
peer channel join -b mychannel.block
exit
```

#### update channel

```shell
# 更新Org1
docker exec -it cli1 bash
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
# 更新Org2
docker exec -it cli2 bash
peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

### chaincode

[官方文档-peerlifecycle](https://hyperledger-fabric.readthedocs.io/en/release-2.3/commands/peerlifecycle.html)

#### get chaincode file

```shell
# 先使用官方例子的sacc.go
mkdir ./chaincode/go/sacc
cp ../fabric-samples/chaincode/sacc/sacc.go ./chaincode/go/sacc
```

#### package

```shell
# Org1打包链码
docker exec -it cli1 bash
cd /opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go/sacc
go env -w GOPROXY=https://goproxy.cn,direct
go mod init
go mod vendor
cd /opt/gopath/src/github.com/hyperledger/fabric/peer
# 打包链码
peer lifecycle chaincode package sacc.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go/sacc/ --label sacc_1
# 退出容器
exit
# 复制到cli2容器
docker cp cli1:/opt/gopath/src/github.com/hyperledger/fabric/peer/sacc.tar.gz ./
docker cp ./sacc.tar.gz cli2:/opt/gopath/src/github.com/hyperledger/fabric/peer
rm -rf sacc.tar.gz
```

#### install

```shell
# Org1 安装链码
docker exec -it cli1 bash
peer lifecycle chaincode install sacc.tar.gz 
# Chaincode code package identifier: sacc_1:d62238578eba215488f27f94b582ef72858c2026805d50e84e3007de4bca4016
# Org2 安装链码
docker exec -it cli2 bash
peer lifecycle chaincode install sacc.tar.gz
# Chaincode code package identifier: sacc_1:d62238578eba215488f27f94b582ef72858c2026805d50e84e3007de4bca4016
```

#### approveformyorg

```shell
# Org1同意
docker exec -it cli1 bash
# 查询Package ID
peer lifecycle chaincode queryinstalled
# 同意该链码
peer lifecycle chaincode approveformyorg --channelID mychannel --name sacc --version 1.0 --init-required --package-id sacc_1:d62238578eba215488f27f94b582ef72858c2026805d50e84e3007de4bca4016 --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
# Org2同意
docker exec -it cli2 bash
# 同意该链码
peer lifecycle chaincode approveformyorg --channelID mychannel --name sacc --version 1.0 --init-required --package-id sacc_1:d62238578eba215488f27f94b582ef72858c2026805d50e84e3007de4bca4016 --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
# 查询是否approve成功
peer lifecycle chaincode checkcommitreadiness --channelID mychannel --name sacc --version 1.0 --init-required --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --output json 
# 可以看到两个组织都同意了
{
        "approvals": {
                "Org1MSP": true,
                "Org2MSP": true
        }
}
```

#### commit

```shell
# 任意一个peer节点commit就行
docker exec -it cli1 bash
# commit操作
peer lifecycle chaincode commit -o orderer.example.com:7050 --channelID mychannel --name sacc --version 1.0 --sequence 1 --init-required --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
```

#### invoke

```shell
docker exec -it cli1 bash
# cli1调用链码
peer chaincode invoke -o orderer.example.com:7050 --isInit --ordererTLSHostnameOverride orderer.example.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n sacc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["a","b"]}'
# cli2查询
docker exec -it cli2 bash
peer chaincode query -C mychannel -n sacc -c '{"Args":["query","a"]}'
# cli2数据覆盖
peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n sacc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["set","a","c"]}'
# 再进行查询，发现结果变为c
peer chaincode query -C mychannel -n sacc -c '{"Args":["query","a"]}'
# 再到cli1进行查询，结果也是变为c
docker exec -it cli1 bash
peer chaincode query -C mychannel -n sacc -c '{"Args":["query","a"]}'
```

## explorer

[blockchain-explorer](https://github.com/hyperledger/blockchain-explorer)

```shell
mkdir explorer
cd explorer
# 下载示例配置
proxychains4 wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/examples/net1/config.json
proxychains4 wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/examples/net1/connection-profile/test-network.json -P connection-profile
proxychains4 wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/main/docker-compose.yaml
# 复制ca证书目录
cp -r ../twonodes/crypto-config ./organizations
# 保留示例配置
mkdir testbe
mv config.json connection-profile/ docker-compose.yaml organizations/ testbe
# 创建一个生产浏览器
cp -r testbe explorer
cd exporer
mv ./connection-profile/test-network.json ./connection-profile/org1-network.json
# 修改下网络名
vim ./connection-profile/org1-network.json
cp ./connection-profile/org1-network.json ./connection-profile/org2-network.json
# 修改下网络名跟组织信息等
vim ./connection-profile/org2-network.json
# 修改config.json添加组织1，组织2网络
vim config.json
# 修改docker-compose.yaml网络配置，挂载目录等
vim docker-compose.yaml
# 运行
docker-compose up -d
```

## chaincode

参照官方文档，这里不多赘述

## fabric-go-sdk

[fabric-sdk-go](https://github.com/hyperledger/fabric-sdk-go)是Hyperledger Fabric官方提供的Go语言开发包， 应用程序可以利用fabric-sdk-go与fabric网络进行交互并访问链码

### 基本目录

pkg目录是fabric-sdk-go的主要实现，doc文档介绍了不同目录所提供的功能，以及给出 了接口调用样例：

- pkg/fabsdk：主package，主要用来生成fabsdk以及fabric-sdk-go中其他pkg使用的option context。
- pkg/client/channel：主要用来调用、查询Fabric链码，或者注册链码事件。
- pkg/client/resmgmt：主要用来Hyperledger fabric网络的管理，比如创建通道、加入通道，安装、实例化和升级链码。
- pkg/client/event:配合channel模块来进行Fabric链码事件的注册和过滤。
- pkg/client/ledger：主要用来实现Fabric账本的查询，查询区块、交易、配置等。
- pkg/client/msp：主要用来管理fabric网络中的成员关系。

### 一般步骤

- 为client编写配置文件config.yaml
- 为client创建fabric sdk实例fabsdk
- 为client创建resource manage client，简称RC，RC用来进行管理操作的client， 比如通道的创建，链码的安装、实例化和升级等
- 为client创建channel client，简称CC，CC用来链码的调用、查询以及链码事件 的注册和取消注册

### fabric-sdk-go配置文件config.yaml概述

client使用sdk与fabric网络交互，需要告诉sdk两类信息：

- 我是谁：即当前client的信息，包含所属组织、密钥和证书文件的路径等， 这是每个client专用的信息。
- 对方是谁：即fabric网络结构的信息，channel、org、orderer和peer等 的怎么组合起当前fabric网络的，这些结构信息应当与configytx.yaml中是一致的。这是通用配置，每个客户端都可以拿来使用。另外，这部分信息并不需要是完整fabric网络信息，如果当前client只和部分节点交互，那配置文件中只需要包含所使用到的网络信息。

![img](https://blog.res.jianchengwang.info/posts/hyperledger-fabric/fabric-sdk-go-config.png)

### 使用go mod管理fabric-sdk-go项目的依赖

fabric-sdk-go目前本身使用go modules管理依赖，从go.mod可知，依赖的一些包指定了具体的版本， 如果你的项目依赖的版本和fabric-sdk-go依赖的版本不同，会产生编译问题。

因此建议项目也使用go moudles管理依赖，然后相同的软件包可以使用相同的版本，可以这样操作：

- go mod init初始化好项目的go.mod文件。
- 编写代码，完成后运行go mod run，会自动下载依赖的项目，但版本可能与 fabric-sdk-go中的依赖版本不同，编译存在问题。
- 把go.mod中的内容复制到项目的go.mod中，然后保存，go mod会自动合并相同的依赖， 运行go mod tidy，会自动添加新的依赖或删除不需要的依赖。

### 创建入口实例

通过config.FromFile解析配置文件，然后通过fabsdk.New创建fabric-sdk-go的入口实例。

```go
import "github.com/hyperledger/fabric-sdk-go/pkg/core/config"
import "github.com/hyperledger/fabric-sdk-go/pkg/fabsdk"

sdk, err := fabsdk.New(config.FromFile(c.ConfigPath))
if err != nil {
  log.Panicf("failed to create fabric sdk: %s", err)
}
```

### 资源管理客户端

管理员账号才能进行Hyperledger fabric网络的管理操作，所以创建资源管理客户端一定要 使用管理员账号。

通过fabsdk.WithOrg(“Org1”)和fabsdk.WithUser(“Admin”)指定Org1的Admin账户，使用 sdk.Context创建clientProvider，然后通过resmgmt.New创建fabric-sdk-go资源管理客户端。

```go
import 	"github.com/hyperledger/fabric-sdk-go/pkg/client/resmgmt"

rcp := sdk.Context(fabsdk.WithUser("Admin"), fabsdk.WithOrg("Org1"))
rc, err := resmgmt.New(rcp)
if err != nil {
  log.Panicf("failed to create resource client: %s", err)
}
```

### 创建通道客户端

使用用户账号创建fabric-sdk-go的通道客户端，以便进行fabric链码的调用和查询。 使用sdk.ChannelContext创建channelProvider，需要指定channelID和用户User1， 然后通过channel.New创建通道客户端，这个通道客户端就是调用channelID对应channel上链码的channel client。

```go
import 	"github.com/hyperledger/fabric-sdk-go/pkg/client/channel"

ccp := sdk.ChannelContext(ChannelID, fabsdk.WithUser("User1"))
cc, err := channel.New(ccp)
if err != nil {
  log.Panicf("failed to create channel client: %s", err)
}
```



## 相关链接

[手动搭建超级账本网络](https://space.bilibili.com/373566326)

[hyperledger-fabric-2.3 doc](https://hyperledger-fabric.readthedocs.io/en/release-2.3/)

[sxguan/fabric-network](https://github.com/sxguan/fabric-network)

[sxguan/fabric-go-sdk](https://github.com/sxguan/fabric-go-sdk)

[fabric-sdk-go-tutorial](http://blog.hubwiz.com/2020/03/18/fabric-sdk-go-tutorial/)

[jianchengwang/todo-blockchain/fabric-test](https://github.com/jianchengwang/todo-blockchain/tree/main/fabric-test)

