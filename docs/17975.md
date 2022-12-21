# 使用 SSL 的自签名证书将 MongoDB 连接到 Ruby

> 原文：<https://dev.to/scalegrid/connecting-mongodb-to-ruby-with-self-signed-certificates-for-ssl-1b0g>

[![Connecting MongoDB to Ruby with Self-Signed Certificates for SSL](img/9a1e480f5d8bd11f1bafe3eb97974dbb.png)](https://scalegrid.io/blog/connecting-mongodb-to-ruby-with-self-signed-certificates-for-ssl/)

鉴于我们关于在 Node.js 中将 [MongoDB SSL 与自签名证书相连接的帖子非常受欢迎，我们决定写一篇关于](https://scalegrid.io/blog/mongodb-ssl-with-self-signed-certificates-in-node-js/ "MongoDB SSL with Self-Signed Certificates in Node.js")[将 MongoDB 与 Ruby](https://scalegrid.io/blog/connecting-mongodb-to-ruby-with-self-signed-certificates-for-ssl/ "Connecting MongoDB to Ruby with Self-Signed Certificates for SSL") 相连接的教程。在这篇博客中，我们将向您展示如何使用 [Ruby MongoDB 驱动程序](https://docs.mongodb.com/ruby-driver/current/)和流行的[对象文档映射器(ODM) mongoid](https://docs.mongodb.com/mongoid/current/) 连接到一个配置了 SSL 自签名证书的 MongoDB 服务器。

ScaleGrid 当前在为新集群创建节点时使用 SSL 的自签名证书。此外，我们还为您提供了购买您自己的 SSL 证书并在 [MongoDB 服务器](https://scalegrid.io/mongodb.html "MongoDB hosting")上配置它们的选项，您可以发送电子邮件到 [support@scalegrid.io](mailto:support@scalegrid.io) 以了解有关该服务的更多信息。

## 使用 Ruby MongoDB 驱动程序连接到副本集

对于这个例子，我们将使用最新的稳定的 Ruby MongoDB 驱动程序版本 2.8。2.5.x 版本的驱动程序有一个[已知错误](https://scalegrid.io/blog/mongodb-ruby-driver-2-5-x-case-sensitivity-issues-with-hostnames-on-replica-sets/)，该错误阻止它们与 ScaleGrid 部署一起工作。下面两个例子中使用的 Ruby 版本都是 2.6.3。

驱动程序可用的连接选项记录在[这里](https://docs.mongodb.com/ruby-driver/master/tutorials/ruby-driver-create-client/#ruby-options)，我们需要的选项有:

*   :ssl
*   :ssl_verify
*   :ssl_ca_cert.

首先，从 ScaleGrid 控制台上的集群详细信息页面中找到并复制您的 [MongoDB 连接字符串](https://help.scalegrid.io/docs/mongodb-connecting-to-your-application):

[![MongoDB Connection String for Ruby Applications](img/f431a714ecc998f51cccaeb9c54a2c4e.png)](https://scalegrid.io/blog/wp-content/uploads/2018/12/ConnString.png)

也可以从群集详细信息页面下载 CA 证书文件。下载证书文件并将其存储在应用程序可用的位置: [![MongoDB SSL Certificate and Command Line Syntax for Ruby Applications](img/1b4524b9460a44a62abd69902454fdf6.png)](https://scalegrid.io/blog/wp-content/uploads/2018/12/SSLCert.jpg) 下面的代码片段展示了如何从 Ruby 连接到 MongoDB 副本集:

```
require 'mongo'

Mongo::Logger.logger.level = ::Logger::DEBUG

MONGODB_CA_CERT = "/path/to/ca_cert.pem"
MONGODB_CONN_URL = "mongodb://testuser:@SG-example-17026.servers.mongodirector.com:27017,SG-example-17027.servers.mongodirector.com:27017,SG-example-17028.servers.mongodirector.com:27017/test?replicaSet=RS-example-0&ssl=true"

options = { ssl:true, ssl_verify: true, :ssl_ca_cert => MONGODB_CA_CERT }

client = Mongo::Client.new(MONGODB_CONN_URL, options)

db = client.database
collections = db.collection_names
puts "db #{db.name} has collections #{collections}"
client.close 
```

为了保持示例简单，我们在代码片段中直接指定了连接字符串和证书文件路径——您通常要么将它们放在一个 [yaml](https://docs.mongodb.com/mongoid/current/tutorials/mongoid-installation/#configuration) 文件中，要么将它们指定为环境变量。此外，该示例将日志级别设置为`DEBUG` ，以便可以调试任何连接性问题。一旦解决了连接问题，就应该将其更改为不太详细的级别。

## 使用 Mongoid 连接

我们将在例子中使用的 [mongoid 版本](https://docs.mongodb.com/mongoid/current/tutorials/mongoid-installation/)是最新的稳定版本——7 . 0 . 2。我们将使用一个 yaml 文件来为 mongoid 提供配置，这样一个配置文件的详细信息记录在[这里](https://docs.mongodb.com/mongoid/current/tutorials/mongoid-installation/#anatomy-of-a-mongoid-config)。我们需要连接到副本集的特定于 SSL 的配置选项有:

*   安全套接层
*   ssl _ 验证
*   ssl_ca_cert

我们的 yml 文件:

```
development:
  # Configure available database clients. (required)
  clients:
    # Define the default client. (required)
    default:
      # A uri may be defined for a client:
      # uri: 'mongodb://user:password@myhost1.mydomain.com:27017/my_db'
      # Please see driver documentation for details. Alternatively, you can define the following:
      #
      # Define the name of the default database that Mongoid can connect to.
      # (required).
      database: test
      # Provide the hosts the default client can connect to. Must be an array
      # of host:port pairs. (required)
      hosts:
        - SG-example-17026.servers.mongodirector.com:27017
        - SG-example-17027.servers.mongodirector.com:27017
        - SG-example-17028.servers.mongodirector.com:47100
      options:
        # The name of the user for authentication.
        user: 'testuser'

        # The password of the user for authentication.
        password: 'pwd'

        # The user's database roles.
        roles:
          - 'readWrite'

        # Change the default authentication mechanism. Valid options are: :scram,
        # :mongodb_cr, :mongodb_x509, and :plain. (default on 3.0 is :scram, default
        # on 2.4 and 2.6 is :plain)
        auth_mech: :scram

        # The database or source to authenticate the user against. (default: admin)
        auth_source: test

        # Force the driver to connect in a specific way instead of auto-
        # discovering. Can be one of: :direct, :replica_set, :sharded. Set to :direct
        # when connecting to hidden members of a replica set.
        connect: :replica_set
        ...
        ...
        # The name of the replica set to connect to. Servers provided as seeds that do
        # not belong to this replica set will be ignored.
        replica_set: RS-example-0

        # Whether to connect to the servers via ssl. (default: false)
        ssl: true

        # Whether or not to do peer certification validation. (default: true)
        ssl_verify: true

        # The file containing a set of concatenated certification authority certifications
        # used to validate certs passed from the other end of the connection.
        ssl_ca_cert: /path/to/ca_cert.pem

  # Configure Mongoid specific options. (optional)
  options:
    # Set the Mongoid and Ruby driver log levels. (default: :info)
    log_level: :debug

```

连接示例:

```
gem 'mongoid', '7.0.2'
require 'mongoid'

Mongoid.load!("/path/to/mongoid.yml", :development)

# Not using any of the ODM features - just fetch the underlying mongo client and attempt to connect

client = Mongoid::Clients.default
db = client.database
collections = db.collection_names
puts "db #{db.name} has collections #{collections}"
Mongoid::Clients.disconnect

```

同样，在生产 Ruby on Rails 应用程序中， [yaml 文件路径](https://docs.mongodb.com/mongoid/current/tutorials/mongoid-installation/#rails-applications)将从环境变量中选取。

## 测试故障转移行为

像其他 MongoDB 驱动程序一样，Ruby MongoDB 驱动程序也被设计为在内部识别由于故障转移等事件引起的拓扑变化。但是，最好在故障转移期间测试和验证驱动程序的行为，以避免生产中出现意外。

像我之前在 [MongoDB PyMongo](https://scalegrid.io/blog/pymongo-tutorial-testing-mongodb-failover-in-your-python-app/ "PyMongo Tutorial: Testing MongoDB Failover in Your Python App") 上的帖子一样，我们可以编写一个永久的编写器测试程序来观察驱动程序的故障转移行为。

引发故障转移的最简单方法是运行 [rs.stepDown()](https://docs.mongodb.com/manual/reference/method/rs.stepDown/) 命令:

```
RS-example-0:PRIMARY> rs.stepDown()
2019-04-18T19:44:42.257+0530 E QUERY [thread1] Error: error doing query: failed: network error while attempting to run command 'replSetStepDown' on host 'SG-example-1.servers.mongodirector.com:27017' :
DB.prototype.runCommand@src/mongo/shell/db.js:168:1
DB.prototype.adminCommand@src/mongo/shell/db.js:185:1
rs.stepDown@src/mongo/shell/utils.js:1305:12
@(shell):1:1
2019-04-18T19:44:42.261+0530 I NETWORK [thread1] trying reconnect to SG-example-1.servers.mongodirector.com:27017 (X.X.X.X) failed
2019-04-18T19:44:43.267+0530 I NETWORK [thread1] reconnect SG-example-1.servers.mongodirector.com:27017 (X.X.X.X) ok
RS-example-0:SECONDARY>

```

以下是我们测试代码的相关部分:

```
require 'mongo'
...

logger = Logger.new(STDOUT)
logger.level = Logger::INFO

MONGODB_CA_CERT = "/path/to/ca_cert.pem"
MONGODB_CONN_URL = "mongodb://testuser:@SG-example-17026.servers.mongodirector.com:27017,SG-example-17027.servers.mongodirector.com:27017,SG-example-17028.servers.mongodirector.com:27017/test?replicaSet=RS-example-0&ssl=true"
options = { ssl:true, ssl_verify: true, :ssl_ca_cert => MONGODB_CA_CERT }

begin
    logger.info("Attempting to connect...")
    client = Mongo::Client.new(MONGODB_CONN_URL, options)
    i = 0
    loop do
        db = client.database
        collection = db[:test]
        begin
            doc = {"idx": i, "date": DateTime.now, "text": SecureRandom.base64(3) }
            result = collection.insert_one(doc)
            logger.info("Record inserted - id: #{result.inserted_id}")
            i += 1
            sleep(3)
        rescue Mongo::Error => e
            logger.error("Mong Error seen: #{e.message}")
            logger.error(e.backtrace)
            logger.info("Retrying...")
        end
    end
    logger.info("Done")
rescue => err
    logger.error("Exception seen: #{err.message}")
    logger.error(err.backtrace)
ensure
    client.close unless client.nil?
end 
```

这将不断地向测试数据库的测试集合中写入如下条目:

```
RS-test-0:PRIMARY> db.test.find()
{ "_id" : ObjectId("5cf50ff1896cd172a4f7c6ee"), "idx" : 0, "date" : ISODate("2019-06-03T12:17:53.008Z"), "text" : "HTvd" }
{ "_id" : ObjectId("5cf50ff6896cd172a4f7c6ef"), "idx" : 1, "date" : ISODate("2019-06-03T12:17:58.697Z"), "text" : "/e5Z" }
{ "_id" : ObjectId("5cf50ff9896cd172a4f7c6f0"), "idx" : 2, "date" : ISODate("2019-06-03T12:18:01.940Z"), "text" : "quuw" }
{ "_id" : ObjectId("5cf50ffd896cd172a4f7c6f1"), "idx" : 3, "date" : ISODate("2019-06-03T12:18:05.194Z"), "text" : "gTyY" }
{ "_id" : ObjectId("5cf51000896cd172a4f7c6f2"), "idx" : 4, "date" : ISODate("2019-06-03T12:18:08.442Z"), "text" : "VDXX" }
{ "_id" : ObjectId("5cf51003896cd172a4f7c6f3"), "idx" : 5, "date" : ISODate("2019-06-03T12:18:11.691Z"), "text" : "UY87" }
...

```

让我们看看故障切换期间的行为:

```
I, [2019-06-03T17:53:25.079829 #9464]  INFO -- : Attempting to connect...
I, [2019-06-03T17:53:30.577099 #9464]  INFO -- : Record inserted - id: 5cf5113f896cd124f8f31062
I, [2019-06-03T17:53:33.816528 #9464]  INFO -- : Record inserted - id: 5cf51145896cd124f8f31063
I, [2019-06-03T17:53:37.047043 #9464]  INFO -- : Record inserted - id: 5cf51148896cd124f8f31064
I, [2019-06-03T17:53:40.281537 #9464]  INFO -- : Record inserted - id: 5cf5114c896cd124f8f31065
I, [2019-06-03T17:53:43.520010 #9464]  INFO -- : Record inserted - id: 5cf5114f896cd124f8f31066
I, [2019-06-03T17:53:46.747080 #9464]  INFO -- : Record inserted - id: 5cf51152896cd124f8f31067
I, [2019-06-03T17:53:49.978077 #9464]  INFO -- : Record inserted - id: 5cf51155896cd124f8f31068 <<< Failover initiated here
E, [2019-06-03T17:53:52.980434 #9464] ERROR -- : Mong Error seen: EOFError: end of file reached (for x.x.x.x:27017 (sg-example-17026.servers.mongodirector.com:27017, TLS))
E, [2019-06-03T17:53:52.980533 #9464] ERROR -- : ["C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/mongo-2.8.0/lib/mongo/socket.rb:300:in `rescue in handle_errors'", "C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/mongo-2.8.0/lib/mongo/socket.rb:294:in `handle_errors'", "C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/mongo-2.8.0/lib/mongo/socket.rb:126:in `read'", "C:/Ruby26-x64/lib/ruby/gems/2.6.0/gems/mongo-2.8.0/lib/mongo/protocol/message.rb:139:in `deserialize'",...
...
I, [2019-06-03T17:53:52.980688 #9464]  INFO -- : Retrying...
W, [2019-06-03T17:53:52.981575 #9464]  WARN -- : Retrying ismaster on sg-example-17026.servers.mongodirector.com:27017 due to: Mongo::Error::SocketError EOFError: end of file reached (for x.x.x.x:27017 (sg-example-17026.servers.mongodirector.com:27017, TLS))
I, [2019-06-03T17:54:06.293100 #9464]  INFO -- : Record inserted - id: 5cf51163896cd124f8f3106a
I, [2019-06-03T17:54:09.547716 #9464]  INFO -- : Record inserted - id: 5cf51169896cd124f8f3106b
I, [2019-06-03T17:54:12.806636 #9464]  INFO -- : Record inserted - id: 5cf5116c896cd124f8f3106c

```

很明显，如果捕捉到正确的错误并重试读/写，驱动程序将自动检测拓扑变化并重新连接到新的主机。对于写操作，选项: [retry_writes](http://api.mongodb.com/ruby/2.8.0/Mongo/Client.html) 确保驱动程序在通知应用程序错误之前自己重试一次。

还有多个驱动程序超时，可以根据您在设置中看到的确切行为和延迟进行调整。这些都记录在[这里](https://docs.mongodb.com/ruby-driver/current/tutorials/ruby-driver-create-client/#details-on-timeout-options)。

## 解决纷争

如果您在连接到支持 SSL 的 MongoDB 部署时遇到问题，这里有一些调试技巧:

*   首先，验证您实际上可以从运行您的应用程序的服务器连接到 MongoDB 服务器。最简单的方法是在客户机上安装[mongo shell](https://docs.mongodb.com/manual/introduction/)[。在](https://docs.mongodb.com/manual/installation/#mongodb-community-edition) [Linux](https://docs.mongodb.com/manual/administration/install-on-linux/) 上，你不需要安装整个 MongoDB 服务器——你可以选择单独安装外壳。一旦外壳可用，尝试使用我们提供的“命令行语法”来尝试连接到服务器。
*   如果您无法通过 mongo shell 连接，这意味着客户机无法到达 MongoDB 服务器的端口 27017。查看您的安全组、VPC 和 ScaleGrid 防火墙设置，以确保客户端和服务器之间存在连接。
*   如果网络连接是正确的，接下来要检查的是您正在使用的 [Ruby](https://docs.mongodb.com/ecosystem/drivers/driver-compatibility-reference/#reference-compatibility-language-ruby) 、 [mongoid](https://docs.mongodb.com/mongoid/current/tutorials/mongoid-installation/#compatibility) 和 [mongo](https://docs.mongodb.com/ecosystem/drivers/driver-compatibility-reference/#ruby-driver-compatibility) gem 版本与您的 MongoDB 服务器版本兼容。
*   如果您已经确认驱动程序版本是正确的，尝试在 [IRB](https://en.wikipedia.org/wiki/Interactive_Ruby_Shell) 上运行一个示例 Ruby 脚本，类似于我们上面提供的例子。逐步执行可能会指出问题所在。
*   如果测试脚本运行良好，但是您仍然无法连接到 mongoid，尝试运行一个简单的测试脚本，就像我们上面提供的例子一样。
*   如果您仍然无法连接到您的实例，请通过 [support@scalegrid.io](mailto:support@scalegrid.io) 给我们写信，提供上述故障排除步骤的详细结果以及您正在使用的 Ruby、mongoid 和 mongo 驱动程序的确切版本。 [Gemfile.lock](https://bundler.io/v1.3/rationale.html#checking-your-code-into-version-control) 会给你提供确切的版本。

如果您是 ScaleGrid 的新手，并且想尝试一下本教程，请注册一个[30 天免费试用版](https://console.scalegrid.io/ "Free MongoDB hosting trial")来探索这个平台，并测试如何将 MongoDB 连接到您的 Ruby 应用程序。