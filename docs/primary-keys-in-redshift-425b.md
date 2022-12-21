# 红移中的主键

> 原文：<https://dev.to/naturalkey/primary-keys-in-redshift-425b>

红移中的主键

红移中的 PKs 是一个不寻常的实现；或者至少对于熟悉传统事务性面向行的数据库的人来说是这样。

但 AWS Redshift 是列式的，针对聚合粒度数据时所需的数据仓库、高容量数据加载和快速扫描工作负载进行了优化。

红移的另外两个主要方面是:大规模并行处理(MPP)和分布式无共享——这意味着每个节点管理自己的内存(与共享内存相反),尽管节点知道彼此和领导节点。

维护全功能主键的开销对于这种体系结构来说是很大的负担。但是主键确实存在；只是他们不强制唯一性。它们旨在供参考，作为一个标志，表明“此/这些列中的数据应该是唯一的”。注意“应该”这个词的用法。这就是棘手的地方。您可以继续将重复的数据放入 PK 列，从而违反了 PK 的原则。红移没问题。

据 Redshift 了解，您正在加载的数据已经被“清理”，或者您有一个包括重复数据消除步骤的 ETL(或 ELT)过程。

那么，除了减少开销之外，这种实现还有什么用处呢？PKs 可以通过 Erwin、ER/Studio 和 PowerDesigner 等 ERD 工具读取，并合并到逆向工程数据库图中。这些实体关系图(erd)是数据库中关系和连接列的映射。

当 SELECT COUNT(DISTINCT)没有返回预期的行数时，我第一次意识到了 Redshifts PK 实现；Redshift 假设所有行在 PK 中都是唯一的，并基于该假设返回理论行数。我期待一个不同的数字。在阅读文件后，事情开始变得清晰。

Docs:
[https://Docs . AWS . Amazon . com/redshift/latest/DG/t _ Defining _ constraints . html](https://docs.aws.amazon.com/redshift/latest/dg/t_Defining_constraints.html)