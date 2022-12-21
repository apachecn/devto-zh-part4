# 如何在 ubuntu 18.04 上安装和配置 mongodb

> 原文：<https://dev.to/sagarjethi/how-to-install-and-configure-mongodb-on-ubuntu-18-04-4m6l>

#### 介绍

MongoDB 是开源的 NoSQL(文档)数据库和文档数据库，旨在方便开发。它提供丰富的查询语言、高可用性、水平可伸缩性、对多种存储引擎的支持。
更多查看:[官方简介](https://docs.mongodb.com/manual/introduction/)

#### 安装 MongoDB

下面是安装 MongoDB 社区版的步骤。这一步一步的指导方针，你可以遵循

**第一步。导入 PMS(包管理系统)使用的公钥**

Ubuntu 包管理工具通过验证包是否用 GPG 密钥签名来确保包的一致性和真实性。以下命令将导入 MongoDB 公共 GPG 密钥。

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4 
```

**第二步。为 MongoDB 创建一个列表文件。** 
为 MongoDB
创建一个源码列表文件

```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list 
```

**步骤 3 重新加载本地 Ubuntu 包数据库。**

```
sudo apt-get update 
```

**步骤 4 安装 MongoDB 包**

安装最新版本的 MongoDB。

```
sudo apt-get install -y mongodb-org 
```

安装特定版本的 MongoDB。

```
sudo apt-get install -y mongodb-org=4.0.10 mongodb-org-server=4.0.10 mongodb-org-shell=4.0.10 mongodb-org-mongos=4.0.10 mongodb-org-tools=4.0.10 
```

如果遇到错误，如需故障排除帮助，请检查[故障排除](https://docs.mongodb.com/manual/reference/installation-ubuntu-community-troubleshooting/#install-ubuntu-troubleshooting) 

配置并运行 MongoDB
te
下载 MongoDB 而不是 Ubuntu 提供的非官方 MongoDB 包后，可以执行以下逐步说明。

**第一步:启动 MongoDB。**
跟随命令启动 mongodb

```
sudo service mongod start 
```

**步骤 2 验证 MongoDB 已经成功启动**

```
sudo systemctl status mongod 
```

```
Output
mongod.service - MongoDB Database Server
   Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: 
   Active: active (running) since Fri 2019-07-19 16:50:52 IST; 15min ago
     Docs: https://docs.mongodb.org/manual
 Main PID: 30977 (mongod)
   CGroup: /system.slice/mongod.service
           └─30977 /usr/bin/mongod --config /etc/mongod.conf 
```

您可以在系统启动时启用自动启动 MongoDB。这取决于你

```
sudo systemctl enable mongod 
```

**第三步停止 MongoDB。**
通过跟随命令停止 mongod

```
sudo service mongod stop 
```

**第 4 步重启 MongoDB。**
以下命令重启 mongod:

```
sudo service mongod restart 
```

开始使用 MongoDB。

```
mongo 
```