# 带有 Ruby 的 LightGBM

> 原文：<https://dev.to/kojix2/lightgbm-with-ruby-438>

LightGBM 是一个强大的梯度推进框架。

## 要求

*   [LightGBM](https://github.com/ankane/lightgbm) (红宝石绑定)
    *   `lib_lightgbm.so`、`lib_lightgbm.dylib`、`lib_lightgbm.dll`包含在[宝石](https://github.com/ankane/lightgbm/tree/master/vendor)中。
*   [红色数据集](https://github.com/red-data-tools/red-datasets)
    *   MNIST 数据集。

```
gem install red-datasets
gem install lightgbm 
```

Enter fullscreen mode Exit fullscreen mode

## 运行

```
require 'lightgbm'
require 'datasets'

train_mnist = Datasets::MNIST.new(type: :train)
test_mnist  = Datasets::MNIST.new(type: :test)

train_x = train_mnist.map(&:pixels)
train_y = train_mnist.map(&:label)

test_x  = test_mnist.map(&:pixels)
test_y  = test_mnist.map(&:label)

params = {
  task: :train,
  boosting_type: :gbdt,
  objective: :multiclass,
  num_class: 10
}

train_set = LightGBM::Dataset.new(train_x, label: train_y)
booster = LightGBM.train(params, train_set)

result = booster.predict(test_x)
result.map! { |i| i.index(i.max) }
accuracy = test_y.zip(result).count { |i, j| i == j } / test_y.size.to_f
puts accuracy 
```

Enter fullscreen mode Exit fullscreen mode

0.9727

很好！🌟