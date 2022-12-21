# SonarQube:解决“不可恢复的指数化失败”和弹性搜索“超过磁盘水印”的错误

> 原文：<https://dev.to/setevoy/sonarqube-solving-unrecoverable-indexation-failures-and-elasticsearch-disk-watermark-exceeded-errors-4ia1>

[![](img/64d0ddf19656d63582700d939381b5e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TBAQvY8v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2019/06/sonarqube-logo-e1560940372101.png) 我们用的是从詹金斯乔布斯开始的 SonarQube。

更多信息请见[sonar cube:Docker](https://rtfm.co.ua/en/sonarqube-running-tests-from-jenkins-pipeline-from-docker/)中 Jenkins Pipeline 的运行测试。

因此，sonar cube 是从 Jenkins 开始的——这里一切正常，但在 sonar cube 内部，我们可以看到错误:

> Java . lang . illegalstateexception:不可恢复的索引失败:org . sonar . server . es . indexing listener $ 1 . onfinish(indexing listener . Java:39)
> 
> org . sonar . server . es . bulk indexer . stop(bulk indexer . Java:122)
> 
> …

[![](img/e683122e55dfb80fabb9267addd91eae.png "SonarQube: Unrecoverable indexation failures и Elasticsearch "Disk watermark exceeded"")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190829_123022.png)

和主机日志:

> 8 月 29 日 12:30:26 Jenkins-production docker-compose[12591]:sonar cube _ 1 | 2019 . 08 . 29 09:30:26 ERROR web[][o . s . s . es . bulk indexer]index[components]，type [auth]，id [AWzcfdpYaLFDDA6l6km3]，message[ClusterBlockException[block by:[FORBIDD
> 
> EN/12/index 只读/允许删除(API)]；]]
> 
> 8 月 29 日 12:30:26 Jenkins-production docker-compose[12591]:sonar cube _ 1 | 2019 . 08 . 29 09:30:26 ERROR web[][o . s . s . e . recoveryindexer]elastic search recovery-太多失败[167/167 文档]，等待下次运行
> 
> 8 月 29 日 12:30:26 Jenkins-production docker-compose[12591]:sonar

查看关于[基于磁盘的分片分配](https://www.elastic.co/guide/en/elasticsearch/reference/current/disk-allocator.html)的文档——需要设置`cluster.routing.allocation.disk.watermark.low`或者通过设置`"cluster.routing.allocation.disk.threshold_enabled" : false`来禁用这些检查。

虽然我们在 Jenkins 主机上的 100 GB 总空间中有 12 GB 空闲，但 ElasticSearch 默认按百分比计算，所以这对他来说是不够的。

因为 Elasticsearch 在里面运行(！)的位置——让我们来看看 ElasticSearch 配置存储在哪里:

```
root@jenkins-production:/opt/jenkins# docker exec -ti jenkins_sonarqube_1 find / -name elasticsearch.yml
find: ‘/proc/tty/driver’: Permission denied
/opt/sonarqube/temp/conf/es/elasticsearch.yml
/opt/sonarqube/elasticsearch/config/elasticsearch.yml 
```

我们到了。

Sonar 是从一个 Docker Compose 文件启动的，其中有一些文件/目录映射:

```
version: '3.5'
networks:
  sonar:
    external:
      name: jenkins
services:
  sonarqube:
    user: 1004:1004
    image: sonarqube:7.9.1-community
    ports:
      - "9000:9000"
    networks:
      - sonar
    environment:
      - sonar.jdbc.url=jdbc:postgresql://db:5432/sonar
    volumes:
      - /data/sonarqube/conf:/opt/sonarqube/conf
      - /data/sonarqube/logs:/opt/sonarqube/logs
      - /data/sonarqube/temp:/opt/sonarqube/temp
      - /data/sonarqube/data:/opt/sonarqube/data
      - /data/sonarqube/extensions:/opt/sonarqube/extensions
      - /data/sonarqube/bundled_plugins:/opt/sonarqube/lib/bundled-plugins
    logging:
      driver: "journald"
... 
```

磁盘管理的可用选项有:

*   t0:elasticsearch 是否会检查磁盘状态
*   t0:default 85%，Elasticsearch 不会将球放在该盘上
*   t0default 90%，Elasticsearch 会尝试将球从这个球转移到其他球
*   t 095%:不允许更新索引，该索引上的球度为 0/0。{ 1 } { 2 } { 3 }:{ 4 } { 5 } 95% { 6 }

在主机上创建新配置—`/data/sonarqube/conf/elasticsearch.yml`:

```
cluster.routing.allocation.disk.watermark.flood_stage: 95%
cluster.routing.allocation.disk.watermark.high: 90% 
```

将其映射到 sonar cube 的容器 sonar cube:

```
...
    volumes:
      - /data/sonarqube/conf:/opt/sonarqube/conf
      - /data/sonarqube/conf/elasticsearch.yml:/opt/sonarqube/elasticsearch/config/elasticsearch.yml
... 
```

要访问 elastic search–需要为 SonarQube 设置`sonar.search.httpPort`，参见[如何监控 ElasticSearch](https://docs.sonarqube.org/display/SONARqube71/ElasticSearch+Activity) 。

启用该连接器 *9100* :

```
...
    environment:
      - sonar.jdbc.url=jdbc:postgresql://db:5432/sonar
      - sonar.search.httpPort=9100
... 
```

重新启动服务:

```
root@jenkins-production:/home/admin# systemctl restart sonarqube 
```

一份日志显示，该选项将很快被弃用:

> 2019 . 08 . 29 10:56:15 INFO app[][o . s . a . es . essettings]elastic search 监听 on/127 . 0 . 0 . 1:9001
> 
> 2019 . 08 . 29 10:56:15 WARN app[][o . s . a . es . essettings]elastic search HTTP 连接器在端口 9100 上启用。不得用于生产
> 
> 2019 . 08 . 29 10:55:10 WARN es[][o . e . d . c . s . settings][http . enabled]设置在 Elasticsearch 中已被弃用，将在未来版本中删除！请参阅下一个主要版本的重大变更文档。

重启后检查参数，确保 Elasticsearch 可以看到它们。

因为 Elasticsearch 只在内部监听本地主机(再次！)sonar cube 的容器–使用`curl`从 sonar cube 本身检查:

```
root@jenkins-production:/home/admin# docker exec -ti jenkins_sonarqube_1 curl localhost:9100/_cluster/settings?include_defaults=true | jq '.[].cluster.routing.allocation.disk'
null
null
{
  "threshold_enabled": "true",
  "watermark": {
  "low": "85%",
  "flood_stage": "95%",
  "high": "90%"
},
  "include_relocations": "true",
  "reroute_interval": "60s"
} 
```

好–应用了新设置，现在可以继续构建了。

每个节点使用的磁盘空间可以通过调用下一个 API 来检查:

```
root@jenkins-production:/home/admin# docker exec -ti jenkins_sonarqube_1 curl 'http://localhost:9100/_cat/allocation?v&pretty'

shards disk.indices disk.used disk.avail disk.total disk.percent host      ip        node

24      613.4kb    86.3gb     11.5gb     97.9gb           88 127.0.0.1 127.0.0.1 sonarqube 
```

弹性搜索的所有可用参数-[https://nickcanzoneri.com/elasticsearch-settings](https://nickcanzoneri.com/elasticsearch-settings)。

完成了。

### 类似的帖子

*   <small>06/18/2019</small>[sonar cube:在 Docker](https://rtfm.co.ua/en/sonarqube-running-tests-from-jenkins-pipeline-from-docker/) 运行来自 Jenkins 管道的测试
*   <small>【2019 年 6 月 18 日】</small> [声纳室:码头工；【T3 管道】](https://rtfm.co.ua/sonarqube-zapusk-v-docker-i-vyzov-iz-jenkins-pipeline/)
*   <small>06/19/2019</small>[sonar cube:“SCM provider 自动检测失败”错误](https://rtfm.co.ua/en/sonarqube-the-scm-provider-autodetection-failed-error/)
*   <small>02/27/2017</small>[Docker:Compose down–network JM admin _ default 有活动端点](https://rtfm.co.ua/docker-compose-down-network-jmadmin_default-has-active-endpoints/)