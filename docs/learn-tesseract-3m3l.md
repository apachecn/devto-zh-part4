# 学习宇宙魔方

> 原文：<https://dev.to/sandro/learn-tesseract-3m3l>

# 亚当优化简介

Adam 优化算法是随机梯度下降的扩展，最近在计算机视觉和自然语言处理的深度学习应用中得到了更广泛的采用。
([https://machinelementmastery . com/Adam-optimization-algorithm-for-deep-learning/](https://machinelearningmastery.com/adam-optimization-algorithm-for-deep-learning/))

# ...但是，什么是随机梯度下降？

是一种迭代方法，用于优化具有适当光滑属性(例如，可微或次可微)的目标函数。它被称为随机的，因为该方法使用随机选择的(或混洗的)样本来评估梯度，因此 SGD 可以被视为梯度下降优化的随机近似。
(维基:[https://en.wikipedia.org/wiki/Stochastic_gradient_descent](https://en.wikipedia.org/wiki/Stochastic_gradient_descent))

# 我该如何处理这些信息？

使用 LSTM 单元的 RNN 可以在一组训练序列上以监督的方式进行训练，使用优化算法，如**梯度下降**，结合通过时间的反向传播来计算优化过程中所需的梯度，以便与相对于相应权重的误差导数(在 LSTM 网络的输出层)成比例地改变 LSTM 网络的每个权重。【https://en.wikipedia.org/wiki/Long_short-term_memory】(维基:)