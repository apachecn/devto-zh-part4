# 使用 Python 进行统计建模:指南&顶级库

> 原文：<https://dev.to/kite/statistical-modeling-with-python-how-to-top-libraries-lin>

## 使用 Python 进行统计建模:操作指南&顶级库

这篇文章涵盖了 Python 的一些基本的统计建模框架和方法，可以帮助我们进行统计建模和概率计算。

*   简介:为什么 Python 适用于数据科学
*   为什么这些框架是必要的
*   从 NumPy 开始
*   Matplotlib 和 Seaborn 用于可视化
*   使用 Seaborn 和 Matplotlib
*   推理统计科学
*   高级建模的统计模型
*   sci kit-为统计学习而学习
*   结论

## 为什么这些框架是必要的

虽然 Python 在数据争论、可视化、通用机器学习、深度学习和相关的线性代数(张量和矩阵运算)以及 web 集成方面最受欢迎，但其统计建模能力远没有宣传得那么好。很大一部分数据科学家仍然使用其他特殊的统计语言，如 R、MATLAB 或 SAS over Python 来进行建模和分析。

虽然这些替代方案都为统计分析提供了各自独特的功能和能力，但对于一名有前途的数据科学家来说，了解更多有关可用于描述性和推断性统计的各种 Python 框架和方法的知识是非常有用的。

学习这些框架的最大动机是统计推断和概率建模代表了数据科学家日常工作的面包和黄油。然而，只有通过使用这种基于 Python 的工具，才能使用单一编程语言构建强大的端到端数据科学管道(从数据获取到最终业务决策生成的完整流程)。

如果对不同的任务使用不同的统计语言，你可能会面临一些问题。例如:

*   使用 SQL 命令和 Python 库(如 BeautifulSoup 和 SQLalchemy)进行任何 web 抓取和数据库访问
*   使用 Pandas 清理和准备您的数据表，然后切换到 R 或 SPSS 来执行统计测试和计算置信区间
*   使用 ggplot2 创建可视化，然后使用独立的 LaTeX 编辑器打印最终的分析报告
*   在多个编程框架之间切换会使这个过程变得繁琐并且容易出错。

如果您可以在一个核心 Python 平台内进行统计建模、分析和可视化，会怎么样？让我们看看有什么框架和方法可以完成这样的任务。

## 从 NumPy 开始

NumPy 是 Python 中数值计算的事实上的标准，用作构建数据科学和机器学习应用程序(如 TensorFlow 或 Scikit-learn)的更高级库的基础。对于数值处理，NumPy 比原生 Python 代码要快得多，因为它的方法实现是矢量化的，而且它的许多核心例程都是用 C 语言编写的(基于 CPython 框架)。

尽管大多数与 NumPy 相关的讨论都集中在它的线性代数例程上，但它提供了一组不错的统计建模函数，用于执行基本的描述性统计，并基于各种离散和连续分布生成随机变量。

例如，让我们从一个简单的 Python 列表创建一个 NumPy 数组，并计算基本的描述性统计数据，如平均值、中值、标准差、分位数等。

本文的代码可以在 [Kite 的 Github 库](https://github.com/kiteco/kite-python-blog-post-code/tree/master/statistical-modeling)找到。

```
import numpy as np

# Define a python list a_list = [2, 4, -1, 5.5, 3.5, -2, 5, 4, 6.5, 7.5]

# Convert the list into numpy array an_array = np.array(a_list)

# Compute and print various statistics print('Mean:', an_array.mean())
print('Median:', np.median(an_array))
print('Range (Max - min):', np.ptp(an_array))
print('Standard deviation:', an_array.std())
print('80th percentile:', np.percentile(an_array, 80))
print('0.2-quantile:', np.quantile(an_array, 0.2)) 
```

结果如下:

```
Mean: 3.5
Median: 4.0
Range (Max - min): 9.5
Standard deviation: 2.9068883707497264
80th percentile: 5.699999999999999
0.2-quantile: 1.4000000000000001 
```

要阅读更多关于 Numpy、Matplotlib、Seaborn 和 Statsmodels 的内容，请查看蒂尔莎·萨卡尔的文章全文。

Tirtha Sarkar 是半导体技术专家、数据科学作者，也是 pydbgen、MLR 和 doepy 软件包的作者。他拥有电子工程博士学位和数据分析硕士学位。