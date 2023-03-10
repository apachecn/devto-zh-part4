# 在 PostgreSQL 中管理高可用性——第三部分:Patroni

> 原文：<https://dev.to/scalegrid/managing-high-availability-in-postgresql-part-iii-patroni-3ifb>

[![Managing High Availability in PostgreSQL – Part III: Patroni - ScaleGrid Blog](img/8ae34bdcf49d9cf2dfaf1b8b8233b7c1.png)](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-3/)

在我们之前的博客文章中，我们讨论了 Cluster Labs 的 PostgreSQL 自动故障转移(PAF)和 2ndQuadrant 的 Replication Manager (repmgr)的功能和作用。在本系列的最后一篇文章中，我们将回顾最后一个解决方案 Zalando 的 Patroni，并在最后比较这三个解决方案，以便您可以确定哪个高可用性框架最适合您的 [PostgreSQL 托管](https://scalegrid.io/postgresql.html)部署。

*   [在 PostgreSQL 中管理高可用性–第一部分:PostgreSQL 自动故障转移](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-1/)
*   [在 PostgreSQL 中管理高可用性—第二部分:复制管理器](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-2/)
*   [在 PostgreSQL 中管理高可用性——第三部分:Patroni](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-3/)

## PostgreSQL 的 Patroni

[Patroni](https://patroni.readthedocs.io/en/latest/) 起源于 Governor 的一个分叉，一个来自 Compose 的项目。这是一个用 Python 编写的开源工具套件，用于管理 PostgreSQL 集群的高可用性。Patroni 没有构建自己的一致性协议，而是巧妙地利用了由[分布式配置存储库](https://patroni.readthedocs.io/en/latest/dynamic_configuration.html) (DCS)提供的一致性模型。它还支持其他 DCS 解决方案，如 Zookeeper、etcd、Consul 和 Kubernetes。

Patroni 确保 PostgreSQL HA 集群的端到端设置，包括流复制。它支持创建备用节点的各种方式，并且像一个可以根据您的需要定制的模板一样工作。

这个功能丰富的工具通过 REST APIs 以及一个名为 [patronictl](https://github.com/zalando/patroni/blob/master/patronictl.py) 的命令行实用程序来公开其功能。它通过使用其健康检查 API 来处理负载平衡，从而支持与 HAProxy 的集成。

Patroni 还在回调的帮助下支持事件通知，回调是由某些动作触发的脚本。它通过提供暂停/恢复功能，使用户能够执行任何维护操作。看门狗支持特性使得框架更加健壮。

## 它是如何工作的

最初，需要安装 PostgreSQL 和 Patroni 二进制文件。完成后，您还需要设置 HA DCS 配置。引导集群的所有必要配置都需要在 yaml 配置文件中指定，Patroni 将使用这个文件进行初始化。在第一个节点上，Patroni 初始化数据库，从 DCS 获得 leader 锁，并确保该节点作为主节点运行。

下一步是添加备用节点，Patroni 为此提供了多种选择。默认情况下，Patroni 使用 [pg_basebackup](https://www.postgresql.org/docs/10/app-pgbasebackup.html) 来创建备用节点，并且还支持 WAL-E、pg back space、Barman 等自定义方法来创建备用节点。Patroni 使添加备用节点变得非常简单，并处理所有的引导任务和流复制的设置。

一旦您的集群设置完成，Patroni 将主动监控集群并确保其处于健康状态。主节点每隔 ttl 秒(默认:30 秒)更新一次 leader 锁。当主节点更新领导锁失败时，Patroni 触发选举，将获得领导锁的节点将被选为新的主节点。

### 它如何处理大脑分裂的情况？

在分布式系统中，共识在确定一致性方面起着重要的作用，Patroni 使用 DCS 来获得共识。只有持有领导者锁的节点可以是主节点，并且领导者锁是通过 DCS 获得的。如果主节点没有持有 leader 锁，那么它将被 Patroni 立即降级，作为备用节点运行。这样，在任何时间点，系统中只能有一个主服务器在运行。

### 有什么设置要求吗？

*   Patroni 需要 [python 2.7](https://www.python.org/downloads/) 及以上。
*   必须安装 DCS 及其特定的 python 模块。出于测试目的，可以将 DC 安装在运行 PostgreSQL 的相同节点上。但是，在生产中，DCS 必须安装在单独的节点上。
*   The yaml configuration file must be present using these high level configuration settings:

    | **Global/ Universal**
    This includes configuration such as host name (name), which needs to be unique to the cluster, the name (scope) of the cluster and the path (namespace) used to store the configuration in DCS.**Log**
    Log settings specific to the parent user, including level, format, number of files, file size, etc.**Boot configuration**
    This is the global configuration of the cluster that will be written to DCS. These configuration parameters can be changed by Patroni APIs or directly in DCS. The configuration includes alternate creation methods, initdb parameters, and scripts after initialization. It also contains timeout configuration and parameters that determine the use of PostgreSQL features, such as replication slot, synchronization mode, etc. After the new cluster is initialized, this segment will be written into the/< namespace >/< scope >/config of the given configuration store.**PostgreSQL**
    This part contains PostgreSQL-specific parameters, such as authentication, directory path of data, binary and configuration, and ip address for monitoring.**REST API**
    This section includes the configuration related to REST API that is specific to the parent user, such as listening address, authentication, SSL, etc.**Consular**
    is set to the consular DCS.**ETCD**
    Settings for Etcd DCS**Exhibitor**
    DC settings for exhibitors**Kubernetes**
    Setting of Kubernetes DCS**Zookeeper**
    Settings for ZooKeeper DCS**Watchdog**
    Settings for watchdogs |

## 未来的老板

*   Patroni 支持集群的端到端设置。
*   支持 REST APIs 和 HAproxy 集成。
*   通过由特定动作触发的回调脚本支持事件通知。
*   利用 DCS 达成共识。

## Patroni Cons

*   在恢复配置中，Patroni 不会检测带有未知或不存在节点的备用数据库的错误配置。即使备用节点在没有连接到主节点/级联备用节点的情况下运行，该节点也会显示为从节点。
*   用户需要处理 DCS 软件的设置、管理和升级。
*   需要为组件通信打开多个端口:
    *   Patroni 的 REST API 端口
    *   DCS 最少 2 个端口

## 高可用性测试场景

我们使用 Patroni 对 PostgreSQL HA 管理进行了一些测试。所有这些测试都是在应用程序运行并将数据插入 PostgreSQL 数据库时执行的。该应用程序是使用 PostgreSQL Java [JDBC 驱动程序](https://jdbc.postgresql.org/)编写的，利用了连接故障转移功能。

### 备用服务器测试

| Sl。不 | 测试场景 | 观察 |
| one | 终止 PostgreSQL 进程 | Patroni 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 |
| Two | 停止 PostgreSQL 进程 | Patroni 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 |
| three | 重新启动服务器 | Patroni 需要在重新启动后启动，除非配置为不在重新启动时启动。一旦 Patroni 启动，它就会启动 PostgreSQL 进程并设置备用配置。

*   Writer application is not interrupted.

 |
| four | 停止佩特罗尼进程 | 

*   It did not stop the PostgreSQL process.
*   *Patronic CTL list* does not show this server.
*   Writer application is not interrupted.

因此，本质上，您需要监控 Patroni 进程的健康状况——否则会导致问题。 |

### 主/主服务器测试

| **Sl。否** | **测试场景** | **观察** |
| one | 终止 PostgreSQL 进程 | Patroni 将 PostgreSQL 进程恢复到运行状态。在该节点上运行的 Patroni 拥有主锁，因此选举没有被触发。

*   Writer application is down.

 |
| Two | 停止 PostgreSQL 进程，并在运行状况检查到期后立即将其恢复 | Patroni 将 PostgreSQL 进程恢复到运行状态。在该节点上运行的 Patroni 拥有主锁，因此选举没有被触发。

*   Writer application is down.

 |
| three | 重新启动服务器 | 发生了故障转移，其中一个备用服务器在获得锁后被选为新的主服务器。当 Patroni 在旧主机上启动时，它会将旧主机重新启动并执行 pg_rewind，然后开始跟随新主机。writer 应用程序出现停机。T3】 |
| four | 停止/终止父进程 | 

*   One of the standby servers obtained the DCS lock and became the primary server by promoting itself.
*   The old master is still running, resulting in a multi-master scenario. The application is still writing letters to the old owner.
*   Once Patroni starts on the old host, it will rewind the old host ( **use _ pg _ rewind** is set to true) to the new host timeline and lsn, and start following the new host.

从上面可以看出，监控主服务器上 Patroni 进程的健康状况是非常重要的。否则会导致多主场景和潜在的数据丢失。 |

### 网络隔离测试

| **Sl。否** | **测试场景** | **观察** |
| one | 网络-将主服务器与其他服务器隔离 | 主节点的 DCS 通信被阻止。

*   PostgreSQL is downgraded on the primary server.
*   A new owner was elected in most partitions.
*   The writer application is down.

 |
| Two | 网络-将备用服务器与其他服务器隔离开来 | 备用节点的 DCS 通信被阻止。

*   PostgreSQL service is running, but this node is not considered for election.
*   Writer application is not interrupted.

 |

## 最好的 PostgreSQL HA 框架是什么？

Patroni 对于 PostgreSQL 数据库管理员(DBA)来说是一个有价值的工具，因为它执行 PostgreSQL 集群的端到端设置和监控。选择跟单信用证和备用信用证创建的灵活性对最终用户来说是一个优势，因为他们可以选择自己喜欢的方法。

REST APIs、HaProxy 集成、看门狗支持、回调及其功能丰富的管理使 Patroni 成为 PostgreSQL HA 管理的最佳解决方案。

## PostgreSQL HA 框架测试:PAF vs. repmgr vs. Patroni

下面是一个综合表格，详细列出了我们在所有三个框架上执行的所有测试的结果——PostgreSQL 自动故障转移(PAF)、Replication Manager (repmgr)和 Patroni。

### 备用服务器测试

| 测试场景 | PostgreSQL 自动故障转移(PAF) | 复制管理器(repmgr) | 佩特罗尼 |
| 终止 PostgreSQL 进程 | Pacemaker 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 | 备用服务器被标记为失败。需要手动干预才能再次启动 PostgreSQL 进程。

*   Writer application is not interrupted.

 | Patroni 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 |
| 停止 PostgreSQL 进程 | Pacemaker 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 | 备用服务器被标记为失败。需要手动干预才能再次启动 PostgreSQL 进程。

*   Writer application is not interrupted.

 | Patroni 将 PostgreSQL 进程恢复到运行状态。

*   Writer application is not interrupted.

 |
| 重新启动服务器 | 备用服务器最初被标记为脱机。一旦服务器在重启后启动，PostgreSQL 就由 Pacemaker 启动，并且服务器被标记为在线。如果启用了防护，则节点不会自动添加到群集。

*   Writer application is not interrupted.

 | 备用服务器被标记为失败。服务器重启后启动，PostgreSQL 被手动启动，服务器被标记为正在运行。

*   Writer application is not interrupted.

 | Patroni 需要在重新启动后启动，除非配置为不在重新启动时启动。一旦 Patroni 启动，它就会启动 PostgreSQL 进程并设置备用配置。

*   Writer application is not interrupted.

 |
| 停止框架代理进程 | 代理:起搏器

*   PostgreSQL process is stopped and marked as offline.
*   Writer application is not interrupted.

 | 代理:repmgrd

*   The standby server will not be an automatic failover situation.
*   PostgreSQL service was found to be running.
*   Writer application is not interrupted.

 | 代理:patroni

*   It did not stop the PostgreSQL process.
*   *Patronic CTL list* does not show this server.
*   Writer application is not interrupted.

 |

### 主/主服务器测试

| 测试场景 | PostgreSQL 自动故障转移(PAF) | 复制管理器(repmgr) | 佩特罗尼 |
| 终止 PostgreSQL 进程 | Pacemaker 将 PostgreSQL 进程恢复到运行状态。初选在阈值时间内恢复，因此没有触发选举。

*   Writer application is down.

 | repmgrd 在固定时间间隔内对所有备用服务器上的主服务器连接启动了运行状况检查。当所有重试失败时，在所有备用服务器上触发选举。选举的结果是，最近获得 LSN 奖的候补队员得到了提升。选举失败的备用服务器将等待来自新的主节点的通知，并且一旦它们接收到通知就跟随它。需要手动干预才能再次启动 postgreSQL 进程。

*   Writer application is down.

 | Patroni 将 PostgreSQL 进程恢复到运行状态。在该节点上运行的 Patroni 拥有主锁，因此没有触发选举。

*   Writer application is down.

 |
| 停止 PostgreSQL 进程，并在运行状况检查到期后立即将其恢复 | Pacemaker 将 PostgreSQL 进程恢复到运行状态。初选在阈值时间内恢复，因此没有触发选举。

*   Writer application is down.

 | repmgrd 在固定的时间间隔内对所有备用服务器上的主服务器连接启动了运行状况检查。当所有重试失败时，在所有备用节点上触发选举。然而，新选出的主服务器没有通知现有的备用服务器，因为旧的主服务器回来了。集群处于不确定状态，需要手动干预。

*   Writer application is down.

 | Patroni 将 PostgreSQL 进程恢复到运行状态。在该节点上运行的 Patroni 拥有主锁，因此没有触发选举。

*   Writer application is down.

 |
| 重新启动服务器 | 在主机不可用的阈值时间后，起搏器触发了选举。最符合条件的备用服务器被提升为新的主服务器。一旦旧主服务器在重新启动后启动，它将作为备用服务器添加回集群。如果启用了防护，则节点不会自动添加到群集。

*   Writer application is down.

 | 当所有备用服务器上的主连接运行状况检查失败时，repmgrd 开始选举。符合条件的备用数据库已升级。当此服务器返回时，它没有加入群集，并被标记为失败。运行了 repmgr 节点重新加入命令以将服务器添加回群集。

*   Writer application is down.

 | 发生了故障转移，其中一个备用服务器在获得锁后被选为新的主服务器。当 Patroni 在旧主机上启动时，它会将旧主机重新启动并执行 pg_rewind，然后开始跟随新主机。

*   Writer application is down.

 |
| 停止框架代理进程 | 代理:起搏器

*   PostgreSQL process is stopped and marked as offline.
*   The election was triggered and the new owner was elected.
*   Writer application is down.

 | 代理:repmgrd

*   The primary server will not be in the case of automatic failover.
*   PostgreSQL service was found to be running.
*   Writer application is not interrupted.

 | 代理:patroni

*   One of the standby servers acquired the DCS lock and became the primary server by promoting itself.
*   The old master is still running, resulting in a multi-master scenario. The application is still writing letters to the old owner.
*   Once Patroni starts on the old host, it will rewind the old host ( **use _ pg _ rewind** is set to true) to the new host timeline and lsn, and start following the new host.

 |

### 网络隔离测试

| 测试场景 | PostgreSQL 自动故障转移(PAF) | 复制管理器(repmgr) | 佩特罗尼 |
| 网络隔离主服务器和其他服务器(裂脑场景) | 主服务器上的 Corosync 流量被阻止。

*   PostgreSQL service was shut down, and the main server was marked offline due to the arbitration policy.
*   A new owner was elected in most partitions.
*   The writer application is down.

 | repmgr 配置中的*位置*的所有服务器都具有相同的值:

*   When the health check of the primary connection on all standby servers fails, repmgrd starts the election.
*   The qualified standby node is promoted, but PostgreSQL process is still running on the old primary node.
*   There are two nodes operating as master nodes. After network isolation is corrected, manual intervention is required.

备用服务器对于*位置*具有相同的值，但是主服务器对于 repmgr 配置中的*位置*具有不同的值:

*   When the health check of the primary connection on all standby servers fails, repmgrd starts the election.
*   However, no new primary server has been selected, because the *position* of the standby server is different from that of the primary server.
*   The repmgrd enters the degraded monitoring mode. PostgreSQL runs on all nodes, and there is only one master node in the cluster.

 | 主节点的 DCS 通信被阻止。

*   PostgreSQL is downgraded on the primary server.
*   A new owner was elected in most partitions.
*   The writer application is down.

 |
| 网络-将备用服务器与其他服务器隔离开来 | 备用服务器上的 Corosync 流量被阻止。

*   Because of the arbitration policy, the server was marked as offline and PostgreSQL service was shut down.
*   Writer application is not interrupted.

 | 

*   The repmgrd enters the degraded monitoring mode.
*   PostgreSQL process is still running on the standby node.
*   Manual intervention is required after network isolation is corrected.

 | 备用节点的 DCS 通信被阻止。

*   PostgreSQL service is running, but this node is not considered for election.
*   Writer application is not interrupted.

 |