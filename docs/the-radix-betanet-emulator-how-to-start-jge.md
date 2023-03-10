# Radix Betanet 模拟器-如何开始？

> 原文：<https://dev.to/radixdlt/the-radix-betanet-emulator-how-to-start-jge>

# 简介

这是一个快速入门指南，旨在让非技术用户在您的计算机上运行 Radix DLT Betanet 模拟器。包括以下步骤:

- [安装 Docker](https://docs.radixdlt.com/kb/#installing-docker)
- [创建配置文件](https://docs.radixdlt.com/kb/#creating-a-docker-composeyml-configuration-file)
- [启动 Radix DLT Betanet 仿真器](https://docs.radixdlt.com/kb/#launching-emulator)

> # 模拟器限制
> 
> betanet 模拟器在独立模式下工作。
> -节点之间没有网络或连接。
> -吞吐量有限，因为所有工作负载都集中在一个节点上。
> -网络 API 端点总是返回一个空集。

# 先决条件

## 硬件

您的主机应该至少有:

2 个 CPU 内核
8 GB 内存
20 GB 磁盘

> **注意**:随着分类帐的增长，实际的磁盘大小需求也会随着时间的推移而增长。

# 软件

您可以在任何支持 Docker 和 Docker Compose 的操作系统上运行 Radix DLT Betanet 模拟器，包括:

-Linux
-MAC OS X
-Windows 10

# 安装对接器

你可以在这里为你的系统下载合适的 Docker 引擎(社区版):[https://hub.docker.com/search/?type=edition&提供=社区](https://hub.docker.com/search/?type=edition&offering=community)

关于详细的 Docker 安装指南，请参考 [Docker CE](https://www.docker.com/products/docker-engine) 的[官方安装](https://docs.docker.com/install/)文档。

# 安装 Docker Compose(仅限 Linux)

如果你运行的是 Linux，在你完成 Docker 设置之后，你需要安装 [Docker Compose](https://docs.docker.com/compose/install/) 。参见官方 Docker Composite [安装指南](https://docs.docker.com/compose/install/)获取下载链接和其他信息。

> **提示** : Docker Compose 捆绑了 Mac 和 Windows 版本的 Docker CE。

# 创建配置文件

Radix DLT 软件堆栈由单个 docker 映像组成，`radixdlt/radixdlt-core-emulator:1.0.0-beta.`
您的`betanet-emulator.yml`决定了您将运行的软件组件。具体来说，指定了以下设置:

-下载并启动
的 Docker 映像-持久数据卷-在重启和升级后仍然存在。

从这个 docker 合成文件开始:

```
version: '2.2'
services:
  core:
    image: radixdlt/radixdlt-core-emulator:1.0.0-beta
    init: true
    ports:
      - "8080:8080"
    environment:
      CORE_NETWORK_SEEDS: core
      CORE_SECURE_RANDOM_SOURCE: /dev/urandom
      CORE_UNIVERSE: v2djcmVhdG9yWCIBA3hanCWf3pmR5E+i+wtWWfKleBrDOQduLb/vcFKOSt9oa2Rlc2NyaXB0aW9ueB5UaGUgUmFkaXggZGV2ZWxvcG1lbnQgVW5pdmVyc2VnZ2VuZXNpc4G/aG1ldGFEYXRhv2l0aW1lc3RhbXBtMTU1MTIyNTYwMDAwMP9ucGFydGljbGVHcm91cHODv2lwYXJ0aWNsZXOBv2hwYXJ0aWNsZb9lYnl0ZXNXAVJhZGl4Li4uIGp1c3QgaW1hZ2luZSFsZGVzdGluYXRpb25zgVECVqurOHBYXwTQFdVa32ALx2Rmcm9tWCcEAgN4Wpwln96ZkeRPovsLVlnypXgawzkHbi2/73BSjkrfaIh5wbllbm9uY2UbAAQdARdF7ABqc2VyaWFsaXplcndyYWRpeC5wYXJ0aWNsZXMubWVzc2FnZWJ0b1gnBAIDeFqcJZ/emZHkT6L7C1ZZ8qV4GsM5B24tv+9wUo5K32iIecG5Z3ZlcnNpb24YZP9qc2VyaWFsaXplcnNyYWRpeC5zcHVuX3BhcnRpY2xlZHNwaW4BZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnRyYWRpeC5wYXJ0aWNsZV9ncm91cGd2ZXJzaW9uGGT/v2lwYXJ0aWNsZXODv2hwYXJ0aWNsZb9sZGVzdGluYXRpb25zgVECVqurOHBYXwTQFdVa32ALx2Vub25jZQBjcnJpWDkGL0pIMVA4ZjN6bmJ5ckRqOEY0UldwaXg3aFJrZ3hxSGpkVzJmTm5LcFIzdjZ1Zlhua25vci9YUkRqc2VyaWFsaXplcnNyYWRpeC5wYXJ0aWNsZXMucnJpZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnNyYWRpeC5zcHVuX3BhcnRpY2xlZHNwaW4gZ3ZlcnNpb24YZP+/aHBhcnRpY2xlv2dhZGRyZXNzWCcEAgN4Wpwln96ZkeRPovsLVlnypXgawzkHbi2/73BSjkrfaIh5wblrZGVzY3JpcHRpb25zUmFkaXggTmF0aXZlIFRva2Vuc2xkZXN0aW5hdGlvbnOBUQJWq6s4cFhfBNAV1VrfYAvHa2dyYW51bGFyaXR5WCEFAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFnaWNvblVybHg0aHR0cHM6Ly9hc3NldHMucmFkaXhkbHQuY29tL2ljb25zL2ljb24teHJkLTMyeDMyLnBuZ2RuYW1lZFJhZHNrcGVybWlzc2lvbnO/ZGJ1cm5kbm9uZWRtaW50c3Rva2VuX2NyZWF0aW9uX29ubHn/anNlcmlhbGl6ZXJ4IHJhZGl4LnBhcnRpY2xlcy50b2tlbl9kZWZpbml0aW9uZnN5bWJvbGNYUkRndmVyc2lvbhhk/2pzZXJpYWxpemVyc3JhZGl4LnNwdW5fcGFydGljbGVkc3BpbgFndmVyc2lvbhhk/79ocGFydGljbGW/ZmFtb3VudFghBf//////////////////////////////////////////bGRlc3RpbmF0aW9uc4FRAlarqzhwWF8E0BXVWt9gC8drZ3JhbnVsYXJpdHlYIQUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAWVub25jZRsABB0BF0ZbTmtwZXJtaXNzaW9uc79kYnVybmRub25lZG1pbnRzdG9rZW5fY3JlYXRpb25fb25sef9qc2VyaWFsaXplcngicmFkaXgucGFydGljbGVzLnVuYWxsb2NhdGVkX3Rva2Vuc3gYdG9rZW5EZWZpbml0aW9uUmVmZXJlbmNlWDkGL0pIMVA4ZjN6bmJ5ckRqOEY0UldwaXg3aFJrZ3hxSGpkVzJmTm5LcFIzdjZ1Zlhua25vci9YUkRndmVyc2lvbhhk/2pzZXJpYWxpemVyc3JhZGl4LnNwdW5fcGFydGljbGVkc3BpbgFndmVyc2lvbhhk/2pzZXJpYWxpemVydHJhZGl4LnBhcnRpY2xlX2dyb3VwZ3ZlcnNpb24YZP+/aXBhcnRpY2xlc4O/aHBhcnRpY2xlv2ZhbW91bnRYIQX//////////////////////////////////////////2xkZXN0aW5hdGlvbnOBUQJWq6s4cFhfBNAV1VrfYAvHa2dyYW51bGFyaXR5WCEFAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFlbm9uY2UbAAQdARdGW05rcGVybWlzc2lvbnO/ZGJ1cm5kbm9uZWRtaW50c3Rva2VuX2NyZWF0aW9uX29ubHn/anNlcmlhbGl6ZXJ4InJhZGl4LnBhcnRpY2xlcy51bmFsbG9jYXRlZF90b2tlbnN4GHRva2VuRGVmaW5pdGlvblJlZmVyZW5jZVg5Bi9KSDFQOGYzem5ieXJEajhGNFJXcGl4N2hSa2d4cUhqZFcyZk5uS3BSM3Y2dWZYbmtub3IvWFJEZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnNyYWRpeC5zcHVuX3BhcnRpY2xlZHNwaW4gZ3ZlcnNpb24YZP+/aHBhcnRpY2xlv2ZhbW91bnRYIQX///////////////////////////zE0cNgL3/DF////2xkZXN0aW5hdGlvbnOBUQJWq6s4cFhfBNAV1VrfYAvHa2dyYW51bGFyaXR5WCEFAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFlbm9uY2UbAAQdARdG1htrcGVybWlzc2lvbnO/ZGJ1cm5kbm9uZWRtaW50c3Rva2VuX2NyZWF0aW9uX29ubHn/anNlcmlhbGl6ZXJ4InJhZGl4LnBhcnRpY2xlcy51bmFsbG9jYXRlZF90b2tlbnN4GHRva2VuRGVmaW5pdGlvblJlZmVyZW5jZVg5Bi9KSDFQOGYzem5ieXJEajhGNFJXcGl4N2hSa2d4cUhqZFcyZk5uS3BSM3Y2dWZYbmtub3IvWFJEZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnNyYWRpeC5zcHVuX3BhcnRpY2xlZHNwaW4BZ3ZlcnNpb24YZP+/aHBhcnRpY2xlv2dhZGRyZXNzWCcEAgN4Wpwln96ZkeRPovsLVlnypXgawzkHbi2/73BSjkrfaIh5wblmYW1vdW50WCEFAAAAAAAAAAAAAAAAAAAAAAAAAAADOy48n9CAPOgAAABsZGVzdGluYXRpb25zgVECVqurOHBYXwTQFdVa32ALx2tncmFudWxhcml0eVghBQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABZW5vbmNlGwAEHQEXRsX0a3Blcm1pc3Npb25zv2RidXJuZG5vbmVkbWludHN0b2tlbl9jcmVhdGlvbl9vbmx5/2ZwbGFuY2saAYp/QGpzZXJpYWxpemVyeCRyYWRpeC5wYXJ0aWNsZXMudHJhbnNmZXJyYWJsZV90b2tlbnN4GHRva2VuRGVmaW5pdGlvblJlZmVyZW5jZVg5Bi9KSDFQOGYzem5ieXJEajhGNFJXcGl4N2hSa2d4cUhqZFcyZk5uS3BSM3Y2dWZYbmtub3IvWFJEZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnNyYWRpeC5zcHVuX3BhcnRpY2xlZHNwaW4BZ3ZlcnNpb24YZP9qc2VyaWFsaXplcnRyYWRpeC5wYXJ0aWNsZV9ncm91cGd2ZXJzaW9uGGT/anNlcmlhbGl6ZXJqcmFkaXguYXRvbWpzaWduYXR1cmVzv3ggNTZhYmFiMzg3MDU4NWYwNGQwMTVkNTVhZGY2MDBiYze/YXJYIQGN+yS2lSWiWm+Qp+RE0XKZZlDCmsYEHqVOgwi5sumVH2FzWCEBUZbY2D5H9/zBPnDtU80qy4A1/EAJyrzw/bOCrHlNgoFqc2VyaWFsaXplcnZjcnlwdG8uZWNkc2Ffc2lnbmF0dXJlZ3ZlcnNpb24YZP//bXRlbXBvcmFsUHJvb2a/Y2FpZFghCEJOW9hqiqRdHgAFw1rMG1kA7xFc89evO1arqzhwWF8EanNlcmlhbGl6ZXJ0dGVtcG8udGVtcG9yYWxfcHJvb2ZndmVyc2lvbhhkaHZlcnRpY2Vzgb9lY2xvY2sBamNvbW1pdG1lbnRYIQMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGVvd25lclgiAQO5kZ9TtqJE8PAqMAkR3IZE413Vm/wmXBPBKjvYm8ddX2hwcmV2aW91c1ECAAAAAAAAAAAAAAAAAAAAAGZyY2xvY2sbAAABaSxAOABqc2VyaWFsaXplcnV0ZW1wby50ZW1wb3JhbF92ZXJ0ZXhpc2lnbmF0dXJlv2FyWCEBPkk2kYaH4ixggyRYaSAYGe80qCxej1B5I+Zi+x/jn4Fhc1ghASXlWsUP3WFDhORmhokZUIVw5sy27TBsZ/Rudr1ueAXkanNlcmlhbGl6ZXJ2Y3J5cHRvLmVjZHNhX3NpZ25hdHVyZWd2ZXJzaW9uGGT/anRpbWVzdGFtcHO/Z2RlZmF1bHQbAAABaSxAOAD/Z3ZlcnNpb24YZP//Z3ZlcnNpb24YZP9lbWFnaWM6T2h//WRuYW1lbFJhZGl4IERldm5ldGZwbGFuY2sZ6mBkcG9ydBl1MGpzZXJpYWxpemVybnJhZGl4LnVuaXZlcnNla3NpZ25hdHVyZS5yWCEBnNHHy2ZNs02RhJ2PaGE8TYjJ6a4JZbs5W5ICDG2FZ4prc2lnbmF0dXJlLnNYIQEzwSczZGOPPINsytlykDyITA51ICNl/GVmTSRwoD4Zeml0aW1lc3RhbXAbAAABaSxAOABkdHlwZQJndmVyc2lvbhhk/w==
      JAVA_OPTS: -server -Xms2g -Xmx2g
    volumes:
      - "core_ledger:/opt/radixdlt/RADIXDB"
      - "core_config:/opt/radixdlt/etc"
    logging:
      options:
        max-size: "10m"
        max-file: "30"
    networks:
      - single_node
  faucet:
    image: radixdlt/faucet:1.0.0-beta
    init: true
    environment:
      FAUCET_DELAY: 0
      FAUCET_TOKEN_RRI: /JH1P8f3znbyrDj8F4RWpix7hRkgxqHjdW2fNnKpR3v6ufXnknor/XRD
      RADIX_IDENTITY_UNENCRYPTED_KEY: JI41zTPZ+DW0JMUBlLciuyrSqT0/Gj9/Oz3+J9MhNL4=
      RADIX_BOOTSTRAP_TRUSTED_NODE: http://core:8080
      JAVA_OPTS: -server
    logging:
      options:
        max-size: "10m"
        max-file: "30"
    networks:
      - single_node
volumes:
  core_ledger:
  core_config:
networks:
  single_node: 
```

1.在您的计算机上创建一个目录，用于存储 docker 撰写文件(例如，`radixdlt`)。
2。使用你最喜欢的文本编辑器创建`betanet-emulator.yml.`
3。复制并粘贴上面的内容。

# 启动 Betanet 模拟器

打开终端(Mac/Linux)或命令提示符(Windows)。导航到放置`betanet-emulator.yml`文件的目录。使用
启动 Docker 容器

```
cd ~/radixdlt
docker-compose -p radixdlt -f betanet-emulator.yml up -d 
```

如果成功，它应该拉下来，完成后看起来像这样:

```
Creating network "radixdlt_single_node" with the default driver
Creating volume "radixdlt_core_ledger" with default driver
Creating volume "radixdlt_core_config" with default driver
Pulling core (radixdlt/radixdlt-core-emulator:1.0.0-beta)...
emulator-1.0.0-beta: Pulling from radixdlt/radixdlt-core
c87736221ed0: Pull complete
a0d980c21713: Pull complete
7153696eb942: Pull complete
5a8ad49b35c2: Pull complete
6e8ff60c7fe2: Pull complete
e5bd13ba5d60: Pull complete
dc2a7e34f4b6: Pull complete
9520c49c4dca: Pull complete
Creating radixdlt_core_1 ... done 
```

检查您是否有一个带有
的`radixdlt_core_1` Docker 容器

```
docker ps 
```

您还可以在[http://localhost:8080/API/system](http://localhost:8080/api/system)检查 Betanet 模拟器是否启动并运行

如果运行正确，您应该会得到一系列指标，看起来应该是这样的:

> {"ledger":{"processed":0，" storingPerShard":0，" storedPerShard "::" str:0.0 "，" stored":1，" storing.peak":0，" latency":{"path":0，" persist":0}，" checksum":9155064537244249607，" processing":0，" faults":{"tears":0，" assisted ":0，" stitched":0 "，" failed":0}，" storing":0}，" agent":{"protocol":100，" name ":":str:/

祝贺您，您现在已经成功运行了 Betanet 模拟器！

# 管理 Betanet 仿真器

一旦运行了 Betanet 模拟器，您可以使用以下命令停止或刷新(升级)软件:

## 停止 Betanet 模拟器

`docker-compose -p radixdlt -f betanet-emulator.yml down`

## 刷新 Betanet 模拟器

`docker-compose -p radixdlt -f betanet-emulator.yml pull`

# Kitematic

这是可选的，但是如果您在 Mac 或 Windows 计算机上运行 Betanet 模拟器，您可以下载 [Kitematic](https://kitematic.com/) 来将 UI 添加到 Docker 容器中。如果你想访问漂亮的按钮和实时日志视图，这绝对是给你的！

# 开发库

最后，但同样重要的是，您可以使用我们的 [Java](https://docs.radixdlt.com/radixdlt-java/) 和 [JavaScript](https://docs.radixdlt.com/radixdlt-js/) 客户端库开始您自己的 DApp 开发。

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询