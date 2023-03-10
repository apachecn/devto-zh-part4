# 什么是随机变量？

> 原文：<https://dev.to/mmithrakumar/what-is-a-random-variable-4nhh>

**随机变量**是一个可以随机取不同值的变量。就其本身而言，随机变量只是对可能状态的描述(你可以把这些想象成类似的函数)，它必须与指定这些状态中的每一个的可能性的概率分布相结合。

好吧，如果这没有意义，让我给你一个例子，当我第一次听说随机变量时，我认为这一定像随机数发生器在每次调用时吐出随机值一样工作，这是部分正确的，让我澄清一下。所以，随机数生成器有两个主要组件，一个采样器，它只不过是一个快乐的灵魂一遍又一遍地抛硬币，报告结果。在这个采样器之后，我们有一个随机变量，随机变量的工作是根据我们的规则将这些正面或反面事件转换成数字。

随机变量可以是离散的，也可以是连续的。离散随机变量是具有有限或可数无限个状态的变量。注意，这些状态不一定是整数；它们也可以只是被命名的状态，不被认为有任何数值。例如，性别(男性、女性等)，我们使用指示函数 **I** 将非数字值映射为数字，例如男性=0，女性=1。连续的随机变量与真实值相关联。

```
"""
The Rademacher and Rayleigh are two types of distributions we will use to generate our samples.

Rademacher: is a discrete probability distribution where a random variate X has a 50% chance of being +1 and a
50% chance of being -1.

Rayleigh: is a continuous probability distribution for non-negative valued random variables.

Do not worry about what probability distributions mean, we will be looking at it in the next section, for now,
you can think of Rademacher as the sampler, the happy guy who tosses coins over and over again where
heads represent +1 and tails -1.
And Rayleigh is the guy who works at a gas/petrol station who helps you to fill the tank and notes down how much
you filled your tank (eg. 1.2l, 4.5l) which are continuous values.
"""

import tensorflow_probability as tfp

# Discrete random variable rademacher = tfp.math.random_rademacher([1, 100], dtype=tf.int32)

# Continuous random variable rayleigh = tfp.math.random_rayleigh([1, 100], dtype=tf.float32)

# Plot discrete random variable 1 and -1 plt.title("Rademacher Discrete Random Variables")
plt.hist(rademacher, color=color_b)
plt.show()

# Plot continuous random variable plt.title("Rayleigh Continuous Random Variables")
plt.hist(rayleigh, color=color_o)
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Rademacher Discrete Random Variables](img/64857d86f668583108a7379cf1011b5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2pDObHqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_9_0.png)

[![Rayleigh Continuous Random Variables](img/09325f7e447509c180a72fa0ac2f92bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G8JM_tNa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_9_1.png)

* * *

这是《用 Tensorflow 2.0 进行深度学习》一书《用 Tensorflow 2.0 进行概率和信息论》一章的第二节。

您可以阅读本节和以下主题:

03.00 -概率与信息论
03.01 -为什么是概率？
03.02 -随机变量
03.03 -概率分布
03.04 -边际概率
03.05 -条件概率
03.06 -条件概率的链式法则
03.07 -独立性和条件独立性
03.08 -期望、方差和协方差
03.09 -常见概率分布
03.10 -常用函数的有用性质
03.11 -贝叶斯' 1

在[用 TF 2.0 深度学习:03.00-概率论与信息论](https://www.adhiraiyan.org/deeplearning/03.00-Probability-and-Information-Theory)。你可以在这里获得这篇文章和本章[其余部分的代码。Google Colab 和 Jupyter Binder 中笔记本的链接在](https://github.com/adhiraiyan/DeepLearningWithTF2.0)[笔记本](https://www.adhiraiyan.org/deeplearning/03.00-Probability-and-Information-Theory)的末尾。