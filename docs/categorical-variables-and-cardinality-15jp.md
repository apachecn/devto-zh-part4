# 分类变量和基数

> 原文：<https://dev.to/mgalalen/categorical-variables-and-cardinality-15jp>

处理分类变量的一种方法是一次性编码。但是在使用它的时候，有一件重要的事情需要记住。
基数，它表示一列中唯一值的数量。

为基数较低的列创建一次性编码总是更好，因为在大型数据集中，一次性编码会扩大数据集的大小。

高基数列可以从数据集中删除，或者我们可以使用标签编码。

[![Alt Text](img/645d1d213b22f803befdbf592ebbdbab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Zc7-o4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4ej5qvwqv1vu25l6x4n.png)