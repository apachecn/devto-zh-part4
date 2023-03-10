# 容错系统的体系结构

> 原文：<https://dev.to/maneeshchaturvedi/architecture-of-fault-tolerant-systems-23m7>

***程序测试可以用来显示 bug 的存在，但绝不能显示它们的不存在。——埃德格·迪克斯特拉**T3】*

# 容错系统

容错系统是一种即使出现故障也能运行的系统。对于容错系统来说，重要的是规范，它定义了系统无故障运行的含义。规范定义了预期的行为，比如 99.99%的可用性。

故障、错误和失败通常被认为是同义词，但它们之间存在差异。故障是一个潜在的缺陷，是一个系统，当它被激活时会导致错误。错误是错误的表现。

当系统不再符合其定义的规范时，就会发生故障。当系统不应该崩溃时，或者系统不响应请求时，或者系统计算出不正确的结果时，都是失败的例子。

容错发生在执行时。这意味着容错系统已经被设计成即使在系统执行期间出现错误，系统也将正确运行而不会出现故障。

# 建筑积木

## 缓解单位

在构建容错应用程序时，确定并在体系结构上定义缓解单元的正确粒度至关重要。缓解单位确定了错误遏制和恢复的基本单位。选择正确粒度的缓解单元对于构建容错系统至关重要。

如果缓解单位太大，会导致整个系统不可用。例如，考虑作为同构集群部署的整体或微服务。如果每个节点都被视为一个缓解单元，那么在出现错误时，不仅整个节点不可用，整个群集也会变得不可用。这可能是因为相同的一组条件会导致相同的错误在集群中出现。

更好的方法是评估更精细的粒度级别。在某些情况下，这些可能是应用程序中的层。一个重要的设计标准是拥有定义良好的接口，这些层通过这些接口进行交互。假设前端、中间层和数据访问层被标识为缓解单元，则错误遏制和恢复可以在层级别处理，尤其是在外围接口。

Web 服务可以将缓解单元作为服务端点。在发布者-消费者体系结构中，缓解单元可以在生产者和消费者级别。请求响应体系结构可以将缓解单元作为请求和响应处理程序。在正确的粒度级别识别缓解单元将允许更好的系统可用性，并使应用其他错误恢复模式变得更容易。我们将在这篇文章的后面讨论一些错误恢复模式。

## 裁员

容错系统假设存在不可靠的组件，这些组件随时可能发生故障。然而，即使存在这些故障，系统作为一个整体也应该继续运行。这假设系统中有一定程度的冗余。典型的分布式系统由 n+m 拓扑结构组成。这意味着有 n 个异构组件，每个组件有 m 个副本/节点。

## 幂等性

在考虑与容错系统的交互时，我们希望将其行为视为黑盒。从外部，请求被发送到系统进行处理。这些请求可以是 RPC 或 Web 风格的请求。为了稳健起见，这些请求可以由源重试。在传统的方式中，一旦请求被发出并且定时器已经到期，请求被重试。从容错系统的角度来看，这些请求应该是等幂的，否则退休偶尔会导致重复工作。作为一种设计选择，将容错系统分解成每个都是幂等的子系统是有意义的。通过捕获子系统之间的状态并确保状态在故障期间保持不变，确保系统作为一个整体具有容错能力。

在分布式系统的世界中，幂等性意味着一个操作可以被重复调用而不会改变结果。幂等性要求系统在响应失败的情况下不重复工作。

假设一个场景，一个客户向系统发送一个请求，要求从他的账户中扣除 100 美元。假设初始帐户余额为$1000，在成功的情况下，系统将发送一个结果为$900 的响应。

然而，可能有两种故障情况会导致系统错误地运行。
场景 1 -系统借记账户，但是响应超时。
场景 2——由于网络分区或崩溃，发送请求的节点在对请求采取行动之前失败。

### 工作单元的唯一标识符

在场景 1 中，如果响应超时，客户端将重试请求。在这种情况下，系统将错误地借记账户两次，导致账户余额为 800 美元，尽管客户期望的是一次借记。这将不符合等幂的要求。理想情况下，我们希望*恰好有一次*语义。也就是说，如果有重复的消息，系统应该只执行一次工作。处理这个场景的方法之一是使用*Uniquefiers——工作单元的唯一标识符*。

系统收到的每个请求都与一个唯一标识符相关联。当系统收到一个请求，它有一个以前没有见过的唯一标识符，它把它当作一个新的请求。重试会有一个系统以前见过的唯一标识符，在这种情况下，系统会将以前计算的响应发送给客户端。这些唯一符可以保存在某种近缓存中，供系统查找，以确定它是以前见过的唯一符还是新的唯一符。

场景 2 很简单。由于系统从未处理过该请求，客户端可以重试该请求。不管请求发送到哪个节点，系统都会正确运行。

## 交换的

交换运算是可以以任何顺序执行而不影响系统最终结果的运算。在无序请求的情况下，交换操作保证了相同的结果。交换操作是实现 CRDT(无冲突复制数据类型)的常用方法。CRDT 用于确保最终的一致性，而没有昂贵的同步或一致的开销。CRDT 有两种口味

### 基于状态

当副本收到更新时，它首先更新其本地副本。稍后，它会将其完整状态发送给另一个副本。偶尔，每个副本会将其完整状态发送给其他副本。接收复制品将应用合并功能来将其本地状态与它刚刚接收的状态合并。类似地，这个副本偶尔也会将其状态发送给另一个副本，因此每次更新最终都会到达系统中的所有副本。如果合并函数是幂等的、可交换的(因此是结合的)，那么所有的复制品保证收敛到相同的值。

例如，考虑一个简单的合并函数，它采用计数器的最大值。在这种情况下，即使副本之间的局部最大值不同，最终所有副本将收敛到计数器的单个整体最大值。

### 基于操作

在这种方法中，副本不跨状态发送，这可能是巨大的，而只是将它已经执行的操作作为更新广播给所有其他副本。其他副本应该在本地重放更新。因为这些都是广播，所以可能会有多个更新(比如 u1 和 u2)被发送到另外两个副本 r1 和 r2。副本可能以不同的顺序接收这些更新，假设 r1 在 u1 之前接收 u2，r2 在 u2 之前接收 u1。然而，如果这些操作是可交换的，复制品将收敛到相同的状态。

我们已经在电子商务和订单执行系统中使用了基于 CRDT 的操作，其中我们使用图来建模 CRDT，以顶点作为起始和结束状态，边权重捕捉操作的数量。重复操作会导致相同的边。此外，所有副本最终都收敛到同一个有向图。

## 从错误中恢复

错误恢复通过将系统置于不包含错误的状态，即使检测到错误也能继续执行。这意味着在一个已知的位置恢复执行，允许它至少像检测到错误之前一样继续处理。错误恢复包括两个主要部分。第一部分包括消除错误的不良影响。第二部分包括在系统中创建一个可以恢复执行的无错误状态。这两者都必须使用最少的时间来最大化可用性。我们在这里简要讨论一些错误恢复机制。

### 检查站

检查点是一种增量保存的状态，便于将处理快速恢复到保存状态的点。从保存的状态恢复减少了返回到错误时存在的相同状态所需的时间。处理可以从中间状态快速恢复，而不必从头开始重放整个事件序列。恢复时间的减少增加了系统可用于服务的时间量，即其可用性

### 反转

回滚包括将系统恢复到已知的良好状态。如果有可用的检查点，则系统状态可以恢复到回滚开始时的状态。如果没有检查点，那么回滚应该将处理返回到最后一个请求开始之前。请求应该保存到完成，这样如果发生回滚，请求仍然存在，可以再次处理。当系统状态回滚时，一些工作将被执行两次，一次在错误之前，一次在错误之后。必须注意确保这种重复工作的副作用不会引起新的问题。

### 故障切换

在系统具有冗余的情况下，可以使用故障转移，如果活动副本失败，可以在备用副本上继续处理。有各种拓扑，但我们将假设一个 n+m 冗余拓扑。理想情况下，为了继续执行，当错误发生时，非活动单元应该被置于与活动单元相同的状态。发生故障的活动单元使用的数据必须以某种方式保持不变，以便接管的冗余单元可以访问这些数据。故障转移的其他问题可能表现在要求协调器自动将流量路由到新的活动副本，而不是旧的副本。

### 重试次数

重试是一种处理瞬时故障或利用故障转移继续处理请求的机制。然而，重试应该是有限的，因为故障是确定性的；当一个潜在的故障受到同样的刺激时，它会以同样的方式激活。重新处理错误发生前的刺激会导致错误再次发生。如果要处理的事件被传送到具有多个节点的分布式系统，例如 n+m 拓扑冗余配置，那么故障将贯穿整个系统，一个接一个的处理器发生故障，导致灾难性的故障。其他重要的方面是缓冲重试之间的时间，通常使用某种指数机制。

在这篇文章中，我们讨论了容错系统的架构考虑，以及一些错误恢复策略。有很多常用的模式，比如系统监视器、断路器、隔板、基于队列的请求管理，它们都被积极地使用，我在这里没有涉及到。