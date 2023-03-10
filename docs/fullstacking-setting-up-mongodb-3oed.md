# 全堆叠:设置 MongoDB

> 原文：<https://dev.to/heymarkkop/fullstacking-setting-up-mongodb-3oed>

这是[全筹码挑战](https://dev.to/heymarkkop/new-challenge-fullstacking-with-react-native-3l55)的一部分。
让我们开始尝试让所有层自己工作:`React Native`、`NodeJS` + `KoaJS`和`MongoDB`。在本教程中，我们将在 Ubuntu 18.04 上按照这些说明设置`MongoDB`

## MongoDB

导入 MongoDB 公共 GPG 密钥:
`wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -`

创建列表文件:
`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list`

重新加载本地包数据库
`sudo apt update`

安装最新稳定版本
`sudo apt-get install -y mongodb-org`

启动 MongoDB
`sudo service mongod start`

检查日志
`cat /var/log/mongodb.log`

停止和重启命令
`sudo service mongod stop`
`sudo service mongod restart`

运行 [mongo shell](https://docs.mongodb.com/manual/mongo/) 连接到默认端口 27017 上的 MongoDB 实例:
或`mongo --port 27017`

显示您正在使用的数据库
`db` *(返回默认数据库‘测试’)*

在集合
中插入一个对象`db.collection.insertOne({ title: "Stampler" })`

[查询](https://docs.mongodb.com/manual/tutorial/query-documents)/选择它
`db.collection.find({})`或者
`db.collection.find({ title: "Stampler"})`

就是这样。下一步是让 NodeJS 可以在我们的 MongoDB 实例中查询对象。但在此之前，我们将设置`React Native`和`NodeJS` + `KoaJS`。
欢迎在下面评论或在推特上打我[@ heymarkop](https://twitter.com/HeyMarkKop)