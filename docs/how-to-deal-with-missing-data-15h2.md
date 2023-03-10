# 如何处理缺失数据

> 原文：<https://dev.to/vincajayi/how-to-deal-with-missing-data-15h2>

数据科学家(DS)和数据分析师(DA)面临的最常见挑战是丢失数据。每天，DA 和 DS 都要花几个小时来处理丢失的数据。问题是为什么缺失数据是一个问题？分析师假设所有变量在特定状态下都应该有特定值，当变量没有值时，我们称之为缺失数据。缺失数据会对统计模型产生严重影响，忽略它可能会导致有偏差的估计，从而使统计结果无效。在这篇文章中，我将提出解决丢失数据问题的方法。尽管不同的研究提出了处理缺失数据的各种方法，但我注意到这些方法都没有理论或数学支持来证明它们的过程。在本文中，我将分析数据科学家解决缺失数据问题必须遵循的九个基本步骤。这些步骤是基于我作为定量研究员和数据科学家 7 年多的个人经验。

处理缺失数据的基本步骤

1.  目的和目标:在采用任何估计缺失数据的方法之前，我们必须知道项目背后的动机，以确定研究问题。必须概述项目的目标，以指定可能与项目相关的关键变量。你必须能够列出有助于回答定义项目目标的问题的相关数据。

2.  检查合适的变量:如果你已经得到了数据集，问自己一个问题:数据集是否包含解决研究问题所需的所有相关变量？例如，数据科学家可能对借助多变量模型预测通货膨胀感兴趣，而收到的数据可能不包含可能的通货膨胀指标，如消费价格或 GDP 平减指数。要解决这个问题，您应该联系您的直线经理或数据部门，为您提供包含相关变量的适当数据集。

3.  可视化数据并检查缺失值:如果有缺失值，检查数据库；请记住，寻找缺失值的最佳方法是在源位置寻找该值。可能是提取过程有问题。

4.  变量替换:处理缺失数据的一个简单方法是用类似的指标替换变量，特别是当大部分数据缺失时。我强烈建议用另一个指标来代替缺失值，尤其是对于连续变量。例如，国内生产总值平减指数可以用来代替消费价格指数来衡量或预测通货膨胀。然而，在应用这种方法时需要小心，因为不同变量的不同代理可能导致不同的结果或结果。

5.  均值/众数/中位数替代:如果缺失值的百分比较小(例如，小于 30%)，可以应用此方法。对于连续变量，缺失值可以用它的中值或均值代替。对于类别变量，缺少的值可以用它的模型值替换。这种方法的局限性在于它减少了数据的可变性。

6.  删除缺失的属性:如果大部分数据缺失(例如，超过 30%)，如果变量是自变量，不依赖于因变量，并且与模型无关，则可以删除所有行或列。例如，如果您希望使用多元回归来预测收入，并且某个产品编号上的某个变量有一个缺失的编号，则可以删除该变量，而不是填充缺失的值。请注意，您可能会丢失样本、重要信息并使模型欠拟合。

7.  评估和预测:您可以使用不同的统计模型或理论模型来估计或预测缺失值。例如，统计模型可以从可用数据集中估计或预测缺失值。

8.  应用复杂的统计模型，这些模型在处理缺失数据时是稳健的，不需要插补:例如，如果您有缺失数据，可以应用 XGBoost 模型进行预测，而不是使用线性回归。默认情况下，XGBoost 模型将处理缺少的值。该模型将最小化训练损失，并在值缺失时为数据集选择最佳插补值。

9.  样本缩减:此步骤适用于时间序列数据，如果您有缺失数据，可以缩减样本以避免寻找缺失值，并基于没有缺失值的缩减样本进行估计。请注意，样品减少会显著影响结果的精密度和准确度。