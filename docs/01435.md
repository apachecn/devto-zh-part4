# C#中如何比较两幅图像？网

> 原文：<https://dev.to/atir_tahir/how-to-compare-two-images-in-c-net-5glo>

GroupDocs。Comparison 是一个文档比较 API，它将内容和样式相关联，检测插入/删除或样式改变的项目的数量，并生成一个摘要。

**如何分组。比较对图像比较起作用？**
比较从左上角开始，逐像素比较整个图像**。如果像素是相同的，它就移动到下一个像素，以此类推，直到找到一个不同点。改变的像素被赋予一个 ID。它被定义为轻擦、删除或改变。它们以特定的颜色突出显示(根据标准设置为蓝色、红色或绿色)。**

 **来源图片
[![Alt Text](img/381b1d3c0ba4a1d99bbe0bb85601cf7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJ7ESYlD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1g3i2kb0nlbjgpjff5hc.jpg)

目标图像与源图像相同，只是它在表格上有一些额外的对象，在文本编辑器中有一些新的代码行。
[![Alt Text](img/9e9b75d7efaefcae2c4b1823eb8a5b1d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wf6wylvh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ej764wcrft0ql2mphao6.jpg)

比较结果
[![Alt Text](img/d086fbac4b01dc596b1468ab2babf22b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l8mRi9rN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nsdk5byp29z02k3k4m4o.jpg)

API 成功地检测到变化，并在输出/结果图像中突出显示它们。在这种情况下，它突出显示插入的项目(例如，眼镜，杯子和代码中的一些变化)。这就是使用 GroupDocs 进行图像比较的方法。的比较。网**