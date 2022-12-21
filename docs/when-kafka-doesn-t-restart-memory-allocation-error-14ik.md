# 当 Kafka 不重启时(内存分配错误)

> 原文：<https://dev.to/philhardwick/when-kafka-doesn-t-restart-memory-allocation-error-14ik>

这既令人担忧又非常有趣——卡夫卡(Kafka)倒下了，而且不会重启。我经历过因为没有设置操作系统(OS)级别的设置而无法重启 kafka。我当然不习惯深入了解卡夫卡是如何工作的，但是调查让我深入研究 java 是如何与操作系统一起工作的。

## 症状

当 Kafka 启动但很快停止时，出现如下所示的内存分配错误:

```
[] INFO [TransactionCoordinator id=3] Starting up. (kafka.coordinator.transaction.TransactionCoordinator)
Java HotSpot(TM) 64-Bit Server VM warning: Attempt to protect stack guard pages failed.
Java HotSpot(TM) 64-Bit Server VM warning: Attempt to deallocate stack guard pages failed.
[] INFO [TransactionCoordinator id=3] Startup complete. (kafka.coordinator.transaction.TransactionCoordinator)
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00007f4e0ff1b000, 12288, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (mmap) failed to map 12288 bytes for committing reserved memory.
# An error report file with more information is saved as:
# /opt/kafka/bin/hs_err_pid2423.log
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00007f4eed723000, 262144, 0) failed; error='Cannot allocate memory' (errno=12)
[thread 139981501064960 also had an error]
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00007f4fe5cc0000, 65536, 1) failed; error='Cannot allocate memory' (errno=12)
[thread 139980200949504 also had an error] 
```

## 修罗

增加允许操作系统创建的[内存映射](https://sysctl-explorer.net/vm/max_map_count/)的最大数量:

```
sysctl -w vm.max_map_count=<number of memory maps> #default is 65536
#e.g. sysctl -w vm.max_map_count=262144 
```

## 诊断

原来，Kafka 为每个日志文件、每个分区、每个主题分配了一个内存映射。当您无限期地保存 kafka 消息并且拥有大量分区时，这意味着该进程达到了它可以分配的内存映射数量的极限。这在 [kafka 文档](https://kafka.apache.org/documentation/#os)(该部分的第三个要点)中有详细描述。

要找出一个 Java 进程使用了多少内存映射，请使用下面的命令:

```
jps -l
pmap <id of kafka process> | wc -l 
```

这是那种只有在卡夫卡重启时才会被发现的东西。它展示了测试重启(即故障转移和回切场景)的重要性，即使是在生产环境中。为了快速恢复服务，当出现问题时，能够自信地重启是非常重要的。

这也表明需要对操作系统级别的值进行良好的监控，而不仅仅是应用程序级别的值或简单的 CPU 和内存消耗。在这种情况下，当永久保存消息或经历更多负载时，映射内存部分的数量只会增加。知道监控什么是至关重要的，有时只能通过通读所有的文档或找到你可以借鉴的公共事后分析来理解。