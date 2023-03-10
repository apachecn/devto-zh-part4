# 关于在文本中使用标题来重新组织大型数据集的建议

> 原文：<https://dev.to/hannahkfrank/how-to-re-organize-hundreds-of-thousands-of-files-554g>

嗨，开发者社区，

我有超过 100 个以下结构的文件(称为 FASTA -对于那些花时间查看基因数据的人来说非常熟悉):

**原始文件 1** :

> **> Gene1_id1**
> GATCGATCCGA
> ATGCAGTCCAG
> 
> **> Gene2_id1**
> ATGCATGCAGC
> ACTAGGCCACG
> CCGTAGCGGAC
> 
> **>基因 1 _ id 2**
> 【tagtacgcagt】
> tagtacgcga

这大约 100 个文件中的每一个都包含大约 20，000 个这样的基因。问题是我的文件是这样组织的，即`Gene1`id 和`Gene2`id 混在一起。

为了进行分析，我需要将我所有的`Gene1`id 组织在一个地方。理想情况下，我将为每个`GeneX`创建一个文件，如下所示:

**期望的最终`Gene1`文件**:

> **> Gene1_id1**
> GATCGATCCGA
> ATGCAGTCCAG
> 
> **>基因 1 _ id 2**
> 【tagtacgcagt】
> tagtacgcga

序列的长度在基因之间和基因内的个体之间是不同的，所以我需要一个标题行下面和下一个标题行上面的所有行与标题相关联。

我目前的解决方案是获取每个文件，然后根据每行的标题创建一个新文件。所以第一个文件创建了三个新文件:一个用于`>Gene1_id1`，一个用于`>Gene2_id1`，一个用于`Gene1_id2`。从那时起，我计划重新组织以适应我的需要。

上述方法的问题是，它已经创建了大约 800，000 个同名文件，这些文件正在杀死我的计算机。一定有更好的办法。

对如何进行有什么建议吗？谢谢！！！

汉娜