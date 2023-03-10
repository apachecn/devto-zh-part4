# Tensorflow 2.0 中的常见概率分布

> 原文：<https://dev.to/mmithrakumar/common-probability-distributions-with-tensorflow-2-0-38m1>

概率分布是一个函数，它描述了你获得随机变量不同可能值的可能性。

下面是一些流行发行版的例子。

#### **3.9.1 伯努利分布**

**伯努利分布**是单个二元随机变量的分布。它由单个参数ϕ∈[0,1 控制，该参数给出了随机变量等于 1 的概率。它具有以下属性:

p(x = 1)=
p(x = 0)=
p(x = x)【ex[x]=
var _ x(x)=】

伯努利分布是**二项式分布**的一个特例，只有一次试验。二项分布是独立且同分布的伯努利随机变量之和。例如，假设你掷一次硬币，正面的概率是 p。代表你掷一次硬币后的奖金的随机变量是伯努利随机变量。那么，在 100 次投掷中，你正面着地的概率是多少，这就是你使用伯努利试验的地方，一般来说，如果有 n 次伯努利试验，那么这些试验的总和就是一个参数为 n 和 p 的二项式分布。下面，我们将看到一个 1000 次试验的例子，并绘制出最终的二项式分布。

```
import tensorflow_probability as tfp
tfd = tfp.distributions

# Create a Bernoulli distribution with a probability .5 and sample size of 1000 

bernoulli_distribution = tfd.Bernoulli(probs=.5)
bernoulli_trials = bernoulli_distribution.sample(1000)

# Plot Bernoulli Distribution sns.distplot(bernoulli_trials, color=color_b)

# Properties of Bernoulli distribution property_1 = bernoulli_distribution.prob(1)
print("P(x = 1) = {}".format(property_1))

property_2 = bernoulli_distribution.prob(0)
print("P(x = 0) = 1 - {} = {}".format(property_1, property_2))

print("Property three is a generalization of property 1 and 2")

print("For Bernoulli distribution The expected value of a Bernoulli random variable  X is p (E[X] = p)")

# Variance is calculated as Var = E[(X - E[X])**2] property_5 = bernoulli_distribution.variance()
print("Var(x) = {0} (1 - {0})".format(property_5))

P(x = 1) = 0.5
P(x = 0) = 1 - 0.5 = 0.5
Property three is a generalization of property 1 and two
For Bernoulli distribution The expected value of a Bernoulli random variable  X is p (E[X] = p)
Var(x) = 0.25 (1 - 0.25) 
```

Enter fullscreen mode Exit fullscreen mode

[![Bernoulli Distribution](img/244eeab4f613d719248a3f22de38372b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nsn9uiMv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_63_1.png)

#### **3.9.2 多项分布**

**多内核**或**分类分布**是具有 *k* 个不同状态的单个离散变量的分布，其中 *k* 是有限的。多项分布是**多项分布**的特殊情况，多项分布是二项式分布的推广。多项分布是 0,⋯,n^k 向量的分布，代表从多项分布中抽取 *n* 个样本时，每个 *k* 类别被访问的次数。

```
# For a fair dice p = [1/6.]*6

# Multinoulli distribution with 60 trials and sampled once multinoulli_distribution = tfd.Multinomial(total_count=60., probs=p)
multinoulli_pdf = multinoulli_distribution.sample(1)

print("""Dice throw values: {}
In sixty trials, index 0 represents the times the dice landed on 1 (= {} times) and
index 1 represents the times the dice landed on 2 (= {} times)\n""".format(multinoulli_pdf,
                                                                           multinoulli_pdf[0][0],
                                                                           multinoulli_pdf[0][1]))

g = sns.distplot(multinoulli_pdf, color=color_b)
plt.grid()

Dice throw values: [[ 8. 10. 13. 12.  9.  8.]]
In sixty trials, index 0 represents the times the dice landed on 1 (= 8.0 times) and
index 1 represents the times the dice landed on 2 (= 10.0 times) 
```

Enter fullscreen mode Exit fullscreen mode

[![Multinomial Distribution](img/0a28fdbcc230962e547d744169be1bc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gPsBNZmH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_66_1.png)

还有其他离散分布，如:

*   超几何分布:没有替换的模型采样
*   泊松分布:表示给定数量的事件在固定的时间和/或空间间隔内发生的概率，如果这些事件以已知的平均速率发生，并且与上次事件后的时间无关。
*   几何分布:计算一次成功所需的伯努利试验次数。

因为这不是对发行版的详尽介绍，所以我只介绍了主要的发行版，对于好奇的人，如果你想了解更多，可以看看我在笔记本末尾提到的参考资料。

接下来，我们将看看一些连续分布。

#### **3.9.3 高斯分布**

实数上最常用的分布是**正态分布**，也称为**高斯分布**:

[![Gaussian Distribution](img/387aad02496a0a599bd389409dce2ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--79rkK3H1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0xmohnpw1um83est2a6.png)

两个参数μ∈R 和σ∈(0，∞)控制正态分布。参数μ给出了中心峰值的坐标。这也是分布的均值:E[x]=μ。分布的标准差由σ给出，方差由σ^2.给出

```
# We use linespace to create a range of values starting from -8 to 8 with incremants (= stop - start / num - 1) rand_x= tf.linspace(start=-8., stop=8., num=150)

# Gaussian distribution with a standard deviation of 1 and mean 0 sigma = float(1.)
mu = float(0.)
gaussian_pdf = tfd.Normal(loc=mu, scale=sigma).prob(rand_x)

# convert tensors into numpy ndarrays for plotting [rand_x_, gaussian_pdf_] = evaluate([rand_x, gaussian_pdf])

# Plot of the Gaussian distribution plt.plot(rand_x_, gaussian_pdf_, color=color_b)
plt.fill_between(rand_x_, gaussian_pdf_, color=color_b)
plt.grid() 
```

Enter fullscreen mode Exit fullscreen mode

[![Gaussian Distribution](img/a2ee71292cd731954c8aa0b1ec3a631a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TRYD1_Ps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_70_0.png)

对于许多应用来说，正态分布是一个明智的选择。在事先不知道实数的分布形式的情况下，正态分布是一个很好的默认选择，原因有两个。

1.  我们希望建模的许多分布确实接近正态分布。**中心极限定理**表明多个独立随机变量之和近似正态分布。
2.  在所有可能的具有相同方差的概率分布中，正态分布编码了实数上最大的不确定性。因此，我们可以认为正态分布是在模型中插入最少先验知识的分布。

正态分布推广到 R^n，在这种情况下，它被称为**多元正态分布**。它可以用正定对称矩阵σ来参数化:

[![Normal distributions](img/93f8f6f3769ead3aa14979b8304f9fbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0fH8XUUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fx4hp3ulhclzj05o75ma.png)

参数μ仍然给出分布的平均值，尽管现在它是向量值。参数σ给出了分布的协方差矩阵。

```
# We create a multivariate normal distribution with two distributions with mean 0\. and std.deviation of 2. mvn = tfd.MultivariateNormalDiag(loc=[0., 0.], scale_diag = [2., 2.])

# we take 1000 samples from the distribution samples = mvn.sample(1000)

# Plot of multi variate distribution g = sns.jointplot(samples[:, 0], samples[:, 1], kind='scatter', color=color_b)
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![multivariate normal distribution](img/1d98942aa0f3c2c4379228aa949bec65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---fK02MlM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_72_0.png)

#### **3.9.4 指数分布和拉普拉斯分布**

在深度学习的背景下，我们常常希望有一个概率分布，在 x = 0 处有一个尖点。为此，我们可以使用**指数分布**:

p(x)；λ)= 1 _(x≥0)exp(-x)

指数分布使用指示函数 1_(x≥0)将概率零赋给 *x* 的所有负值。

```
# We use linespace to create a range of values starting from 0 to 4 with incremants (= stop - start / num - 1) a = tf.linspace(start=0., stop=4., num=41)

# the tf.newaxis expression is used to increase the dimension of the existing array by one more dimension a = a[..., tf.newaxis]
lambdas = tf.constant([1.])

# We create a Exponential distribution and calculate the PDF for a expo_pdf = tfd.Exponential(rate=1.).prob(a)

# convert tensors into numpy ndarrays for plotting [a_, expo_pdf_] = evaluate([a,expo_pdf])

# Plot of Exponential distribution plt.figure(figsize=(12.5, 4))
plt.plot(a_.T[0], expo_pdf_.T[[0]][0], color=color_sb)
plt.fill_between(a_.T[0], expo_pdf_.T[[0]][0],alpha=.33, color=color_b)
plt.title(r"Probability density function of Exponential distribution with $\lambda$ = 1")
plt.grid() 
```

Enter fullscreen mode Exit fullscreen mode

[![Exponential distribution](img/8ac4701b1608355c1fc983977c321a39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMCST1eq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_75_0.png)

一个密切相关的概率分布是**拉普拉斯分布**，它允许我们在任意点μ放置一个概率质量的尖峰:

[![Laplace distribution](img/9288fa7b692defaecaa726691db2ecc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O6ou5EgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kqpq4nv9x1zoehsy6cbv.png)T3】

```
# We use linespace to create a range of values starting from 0 to 4 with incremants (= stop - start / num - 1) a = tf.linspace(start=0., stop=4., num=41)

# the tf.newaxis expression is used to increase the dimension of the existing array by one more dimension a = a[..., tf.newaxis]
lambdas = tf.constant([1.])

# We create a Laplace distribution and calculate the PDF for a laplace_pdf = tfd.Laplace(loc=1, scale=1).prob(a)

# convert tensors into numpy ndarrays for plotting [a_, laplace_pdf_] = evaluate([a, laplace_pdf])

# Plot of laplace distribution plt.figure(figsize=(12.5, 4))
plt.plot(a_.T[0], laplace_pdf_.T[[0]][0], color=color_sb)
plt.fill_between(a_.T[0], laplace_pdf_.T[[0]][0],alpha=.33, color=color_b)
plt.title(r"Probability density function of Laplace distribution")
plt.grid() 
```

Enter fullscreen mode Exit fullscreen mode

[![Laplace distribution](img/73e7157ac18f830b640ffdf65a9160c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_4BoEBcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_77_0.png)

#### **3.9.5 狄拉克分布和经验分布**

在某些情况下，我们希望指定概率分布中的所有质量都聚集在一个点周围。这可以通过使用狄拉克δ函数δ(x)定义 PDF 来实现:

p(x)=δ(x-μ)

狄拉克δ函数被定义为，除了 0 之外，它在任何地方都是零值，但积分到 1。我们可以认为狄拉克δ函数是一系列函数的极限点，这些函数在除零以外的所有点上的质量越来越小。

通过将 p(x)定义为δ位移μ，我们得到一个无限窄无限高的概率质量峰值，其中 x=μ

```
"""
There is no dirac distribution in tensorflow, you will be able to plot using the fast fourier transform in
the tf.signal but that would take us outside the scope of the book so we use the normal distribution
to plot a dirac distribution. Play around with the delta and mu values to see how the distribution moves.
"""

# We use linespace to create a range of values starting from -8 to 8 with incremants (= stop - start / num - 1) rand_x= tf.linspace(start=-8., stop=8., num=150)

# Gaussian distribution with a standard deviation of 1/6 and mean 2 delta = float(1./6.)
mu = float(2.)
dirac_pdf = tfd.Normal(loc=mu, scale=delta).prob(rand_x)

# convert tensors into numpy ndarrays for plotting [rand_x_, dirac_pdf_] = evaluate([rand_x, dirac_pdf])

# Plot of the dirac distribution plt.plot(rand_x_, dirac_pdf_, color=color_sb)
plt.fill_between(rand_x_, dirac_pdf_, color=color_b)
plt.grid() 
```

Enter fullscreen mode Exit fullscreen mode

[![Dirac distribution](img/f7a7576f1314952dd14f106f62e03a9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BbvmC5B7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_80_0.png)

狄拉克δ分布的一个常见用途是作为**经验分布**的一个组成部分:

[![empirical distribution](img/a70b92a5f20de92f8d505f390be7f2fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k6UdOTvS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3bstpqk2jhklh3ranfe.png)

其将概率质量 1/m 放在每个 *m* 点 x^(1),⋯,x^(m 上)，形成给定的数据集或样本集合。

狄拉克δ分布仅在定义连续变量的经验分布时是必要的。

对于离散变量，情况更简单:经验分布可以被概念化为多元分布，与每个可能的输入值相关联的概率简单地等于该值在训练集中的**经验频率**。

我们可以将从训练示例的数据集形成的经验分布视为指定了我们在该数据集上训练模型时所采样的分布。关于经验分布的另一个重要观点是，概率密度使训练数据的可能性最大化。

#### **3.9.6 混合分布**

组合更简单的分布来定义概率分布的一种常见方法是构建一个**混合分布**。混合分布由几个分量分布组成。在每次试验中，应选择哪种成分分布来产生样本，这是通过从多种成分分布中抽取成分标识来确定的:

[![Mixtures of Distributions](img/1db8e3e1da934243f9e6ddf341f71fac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n-04X8pN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q74rlnwn8vqh5n0h9z2h.png)

其中 P(c)是成分恒等式上的多内核分布。

```
"""
We will be creating two variable with two components to plot the mixture of distributions.

The tfd.MixtureSameFamily distribution implements a batch of mixture distribution where all components are from
different parameterizations of the same distribution type. In our example, we will be using tfd.Categorical to
manage the probability of selecting components. Followed by tfd.MultivariateNormalDiag as components.
The MultivariateNormalDiag constructs Multivariate Normal distribution on R^k
"""

num_vars = 2        # Number of variables (`n` in formula). var_dim = 1         # Dimensionality of each variable `x[i]`. num_components = 2  # Number of components for each mixture (`K` in formula). sigma = 5e-2        # Fixed standard deviation of each component. 
# Set seed. tf.random.set_seed(77)

# categorical distribution categorical = tfd.Categorical(logits=tf.zeros([num_vars, num_components]))

# Choose some random (component) modes. component_mean = tfd.Uniform().sample([num_vars, num_components, var_dim])

# component distribution for the mixture family components = tfd.MultivariateNormalDiag(loc=component_mean, scale_diag=[sigma])

# create the mixture same family distribution distribution_family = tfd.MixtureSameFamily(mixture_distribution=categorical, components_distribution=components)

# Combine the distributions mixture_distribution = tfd.Independent(distribution_family, reinterpreted_batch_ndims=1)

# Extract a sample from the distribution samples = mixture_distribution.sample(1000).numpy()

# Plot the distributions g = sns.jointplot(x=samples[:, 0, 0], y=samples[:, 1, 0], kind="scatter", color=color_b, marginal_kws=dict(bins=50))
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Mixtures of distribution](img/d568410789b400d5659ab731a955e455.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qOkY1Hj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/ch03/output_85_0.png)

混合模型让我们可以简单地瞥见一个稍后将变得至关重要的概念——潜在变量。潜变量是我们无法直接观察到的随机变量。潜在变量可能通过联合分布与 x 相关。

一种非常强大和常见的混合模型是高斯混合模型，其中分量 p(x|c=i)是高斯分布的。每个分量具有单独的参数化的平均μ^(i)和协方差σ^(i).与单个高斯分布一样，高斯混合可能会将每个分量的协方差矩阵约束为对角或各向同性。高斯混合模型是密度的**通用近似器**，在某种意义上，任何平滑密度都可以通过具有足够分量的高斯混合模型以任何特定的非零误差量来近似。

其他一些连续分布函数包括:

*   Erlang 分布:在速率为λ的泊松过程中，k 个事件之间的等待时间服从 Erlang 分布。
*   伽马分布:在速率为λ的泊松过程中，伽马分布给出了 k^{th}事件的时间。
*   贝塔分布:代表一系列概率，是用百分比或比例表示结果的通用方法。
*   狄利克雷分布:是贝塔分布的多元推广。狄利克雷分布是贝叶斯统计中常用的先验分布。

* * *

这是《用 Tensorflow 2.0 进行深度学习》一书《用 Tensorflow 2.0 进行概率和信息论》一章的第九节。

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