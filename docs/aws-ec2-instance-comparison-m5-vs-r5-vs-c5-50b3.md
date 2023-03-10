# AWS EC2 实例比较:M5 vs R5 vs C5

> 原文：<https://dev.to/abhishekray07/aws-ec2-instance-comparison-m5-vs-r5-vs-c5-50b3>

*最初发表于[https://www.learnaws.org](https://www.learnaws.org)。*

AWS 有许多不同的实例类型，很难决定哪种实例类型最适合特定的用例。在本文中，我们将比较 AWS 提供的三种最流行的实例类型(M5、R5 和 C5 ),包括它们的规格、性能和成本。

### 比较 M5、R5 和 C5 实例族

#### M5 实例

亚马逊 EC2 M5 实例提供了计算、内存和网络资源的平衡，因此它们是各种工作负载的理想选择。

[![m5-instance-specs](img/ab2c90209f63f7df1acaccf79e1e22fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kG0XrFpv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/icsgepm0igb6k5d6vlid.png)

#### R5 实例

Amazon EC2 R5 实例是内存优化的实例。R5 实例非常适合内存密集型应用，如高性能数据库、分布式 web 级内存缓存、中型内存数据库、实时大数据分析和其他企业应用。

[![r5-instance-specs](img/77211fa8ddd154d06b3d9a91a645ca8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9aUXLAw7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wt1chjktkdtfihvrgkj2.png)

#### C5 实例

Amazon EC2 C5 实例针对计算进行了优化。C5 实例提供了 Amazon EC2 系列中最低的每 vCPU 价格，非常适合运行高级计算密集型工作负载。

[![c5-instance-specs](img/ab4d06eceb0252c358929a0794bd149f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FfLQ6E1F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wl67wxw7gt1p5vs9c3ka.png)

### 表现

我们通过在以下实例上运行一系列测试来比较这些实例系列之间的性能:

*   m5.xlarge
*   r5.xlarge
*   c5.xlarge

| 实例类型 | 心数 | 内存(GB) |
| --- | --- | --- |
| m5.xlarge | four | Sixteen |
| r5.xlarge | four | Thirty-two |
| c5.xlarge | four | eight |

#### 处理器性能

我们使用 [Geekbench](https://www.geekbench.com/) 来比较这些实例类型的性能。Geekbench 是一个处理器基准测试程序。它在处理器上运行一系列测试，并计算处理器完成任务所需的时间。CPU 越快，分数越高。

所有三种实例类型都使用英特尔至强白金 8000 系列(Skylake-SP)处理器。然而，一个关键的区别是 CPU 时钟速度。M5 和 R5 实例系列的核心 Turbo CPU 时钟速度高达 3.1 GHz，而 C5 实例的时钟速度高达 3.4 GHz。

正如所料，c5 实例在 Geekbench 测试中表现最佳。

| 实例类型 | 单核分数 | 多核分数 |
| --- | --- | --- |
| m5.xlarge | Three thousand nine hundred and five | Eight thousand six hundred and ninety-seven |
| r5.xlarge | Four thousand one hundred and twelve | Nine thousand one hundred and thirty-two |
| c5.xlarge | Four thousand two hundred and fifteen | Nine thousand four hundred and fifteen |

#### 记忆性能

我们使用 [Stream](https://github.com/jeffhammond/STREAM) 基准来比较这些实例类型的内存性能。流用于测量持续的内存带宽。

在这个测试中，r5 实例如预期的那样表现最好。R5 实例是内存优化的，与 M5 和 C5 相比拥有最多的内存。M5 实例比 C5 实例有更多的可用内存，并且比 C5 实例有更好的内存性能。

##### m5.xlarge

```
 -------------------------------------------------------------

Function    Best Rate MB/s  Avg time Min time Max time Copy:           27275.6     0.006025     0.005866     0.006193

Scale:          27054.6     0.006070     0.005914     0.006232

Add:            28308.0     0.008854     0.008478     0.009080

Triad:          28298.5     0.008879     0.008481     0.009268

------------------------------------------------------------- 
```

##### r5.xlarge

```
 -------------------------------------------------------------

Function    Best Rate MB/s  Avg time Min time Max time Copy:           28324.3     0.005796     0.005649     0.005914

Scale:          27793.0     0.005834     0.005757     0.005905

Add:            28616.2     0.008505     0.008387     0.008691

Triad:          28704.4     0.008515     0.008361     0.008679

------------------------------------------------------------- 
```

##### c5.xlarge

```
-------------------------------------------------------------

Function    Best Rate MB/s  Avg time Min time Max time Copy:           26445.8     0.006110     0.006050     0.006173

Scale:          26306.9     0.006143     0.006082     0.006235

Add:            27072.4     0.008962     0.008865     0.009093

Triad:          27002.7     0.008980     0.008888     0.009053

------------------------------------------------------------- 
```

#### 磁盘性能

我们还运行了简单的读写基准来测试 EBS 卷在每种实例类型上的性能。

R5 实例在磁盘性能基准测试中表现最佳。

##### m5.xlarge

写

```
dd bs=16k count=102400 oflag=direct if=/dev/zero of=test_data

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 77.5204 s, 21.6 MB/s 
```

阅读

```
 dd bs=16K count=102400 iflag=direct if=test_data of=/dev/null

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 57.9766 s, 28.9 MB/s 
```

##### r5.xlarge

写

```
dd bs=16k count=102400 oflag=direct if=/dev/zero of=test_data

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 58.0189 s, 28.9 MB/s 
```

阅读

```
dd bs=16K count=102400 iflag=direct if=test_data of=/dev/null

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 40.6274 s, 41.3 MB/s 
```

##### c5.xlarge

写

```
dd bs=16k count=102400 oflag=direct if=/dev/zero of=test_data

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 63.4356 s, 26.4 MB/s 
```

阅读

```
dd bs=16K count=102400 iflag=direct if=test_data of=/dev/null

102400+0 records in 102400+0 records out
1677721600 bytes (1.7 GB, 1.6 GiB) copied, 46.5171 s, 36.1 MB/s 
```

#### 定价

我们通过查看每个实例类型的每小时价格**来比较这些实例系列之间的价格。**

C5 实例是三个实例族中最便宜的，而 R5 实例是最贵的。R5 实例几乎比 C5 实例贵 **50%** 。

Linux 随需应变实例的价格/核心:

| 实例类型 | 价格/小时 |
| --- | --- |
| m5.xlarge | $0.192 |
| r5.xlarge | $0.252 |
| c5.xlarge | $0.170 |

* * *

## 结论

新的 C5 实例提供了最好的处理器性能和价格，应该是大多数用例的首选实例类型。

如果用例需要大量内存，那么在 M5 和 R5 之间做出选择。内存中的数据库和缓存等关键应用程序应该使用 R5 实例，因为它们针对这些用例进行了优化。

批处理系统等应用程序可能更适合 M5 实例，因为它们需要额外的内存，但对性能要求不高。

* * *