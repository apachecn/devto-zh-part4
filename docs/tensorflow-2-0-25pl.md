# TensorFlow 2.0 有何新内容

> 原文：<https://dev.to/gorschal/tensorflow-2-0-25pl>

t0tensorflow t1，对 T2machine learning T3 的影响比其他任何人都大。freimwork 最近已经在 Beth 了，文本为 2.0。这是一个相当严肃的评论，在这个文本中，我想强调最重要的几点。

这不是 t0ai t1 手册，为了理解，您应该已经熟悉了 t2tensorflow T3。

## t0t 1 数据组的安装和演示

Jupyter 笔记本电脑:

```
$ pip install tensorflow==2.0.0-beta1 
```

Enter fullscreen mode Exit fullscreen mode

如果需要使用图形处理器(cuda):t 0

```
$ pip install tensorflow-gpu==2.0.0-beta1 
```

Enter fullscreen mode Exit fullscreen mode

我们还将需要一个测试数据集。我们将使用 UCI 归档文件中的 adult t1 数据集。T2￥2

```
import pandas as pd

columns = ["Age", "WorkClass", "fnlwgt", "Education", "EducationNum",
        "MaritalStatus", "Occupation", "Relationship", "Race", "Gender",
        "CapitalGain", "CapitalLoss", "HoursPerWeek", "NativeCountry", "Income"]

data = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data',
                    header=None,
                    names=columns)

data.head() 
```

Enter fullscreen mode Exit fullscreen mode

数据集是一个二进制分类的任务，就是预测一个人是否会每年挣 5 万多美元。

首先，对数据进行基本预处理，然后按 80:20:t0 的比例配置数据拆分

```
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
import numpy as np

# Label Encode le = LabelEncoder()
data = data.apply(le.fit_transform)

# Segregate data features & convert into NumPy arrays X = data.iloc[:, 0:-1].values
y = data['Income'].values

# Split X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=7) 
```

Enter fullscreen mode Exit fullscreen mode

如果 t0 tensorflow t1 已安装，日期已下载，您就可以转到最有趣的地方了。

## t0t 1。默认 Eager execution

在 t0 tensorflow 2 . 0 t1 中，不再需要创建会议并在其中运行计算图。t2eager execution T3 在 2.0 版中默认启用，因此您可以创建模型并立即运行。可以按以下方式禁用 T4 eager execution T5:T6

```
tf.compat.v1.disable_eager_execution() (provided tensorflow is imported with tf alias.) 
```

Enter fullscreen mode Exit fullscreen mode

## 2。tf.function и自动签名

虽然 eager execution 使您能够处理必需的编程，但一旦涉及到分布式培训、全面优化，t0 style graph execution t1 生产环境在 T6 TENSORFLOW 2.0 T7 中，您保留基于图的执行，但方式更灵活。这可以通过 t8tf . function t9t 10 和 auto graph t11 来实现。

tf . function 【t 1 允许您使用函数定义带有 Python 样式语法的 tensorflow 【t 3 列 T6 Autograph T7 支持与 Python 的大范围兼容性，包括 T8 操作符 T9，周期 T10 栋然而，存在局限性。在此处，您可以查看当前可用的操作员的完整列表。以下是一个示例，说明如何使用简单的 T20 装饰器 T21 轻松定义 t 18】 tensorflow 【t 19。T22 型

```
import tensorflow as tf

# Define the forward pass @tf.function
def single_layer(x, y):
    return tf.nn.relu(tf.matmul(x, y))

# Generate random data drawn from a uniform distribution x = tf.random.uniform((2, 3))
y = tf.random.uniform((3, 5))

single_layer(x, y) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您不需要创建用于运行 t0 single _ layer()t1 的会话或填充字段。这是 T2 TF . function T3 的精彩功能之一。正因为如此，它执行所有必要的优化，使代码运行得更快。

## 3。tf . variable _ scope

在 tensorflow 1 . x 【t 1 版本中，要使用 tf . layers 【t 3 但是，在 T6 终端 2.0 T7 中不再需要这样做。由于 keras 作为 t8 tensorflow 2 . 0 和 T9 中的中央高级 API，使用 T10 TF . layy 创建的所有层这大大方便了代码的读取，还可以跟踪变量和损失。

示例:T0

```
# Define the model model = tf.keras.Sequential([
    tf.keras.layers.Dropout(rate=0.2, input_shape=X_train.shape[1:]),
    tf.keras.layers.Dense(units=64, activation='relu'),
    tf.keras.layers.Dropout(rate=0.2),
    tf.keras.layers.Dense(units=64, activation='relu'),
    tf.keras.layers.Dropout(rate=0.2),
    tf.keras.layers.Dense(units=1, activation='sigmoid')
])

# Get the output probabilities out_probs = model(X_train.astype(np.float32), training=True)
print(out_probs) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，您通过 t0 model t1 传递了一个学习数据集，只是为了获得未处理的输出概率。请注意，这只是一条直道。你当然可以往前走，训练你的模型:￥T2

```
model.compile(loss='binary_crossentropy', optimizer='adam')

model.fit(X_train, y_train,
              validation_data=(X_test, y_test),
              epochs=5, batch_size=64) 
```

Enter fullscreen mode Exit fullscreen mode

您可以逐层获取要学习的模型参数列表，例如:￥t0

```
# Model's trainable parameters in a layer by layer fashion model.trainable_variables 
```

Enter fullscreen mode Exit fullscreen mode

## t0t 1 号 4。种姓层这很简单

在机器学习研究和工业应用中，常常需要写种姓层来满足特定的使用场合。t0 tensorflow 2 . 0 t1 使您能够非常简单地编写自己的图层，并与现有图层一起使用。你也可以用任何你方便的方式定制你的模型的直接通过。

要创建种姓层，最简单的方法是从 T2 . TF . keras . layers 扩展 t0 . layer t1 类您将创建一个种姓层，然后定义其直接计算。

您可以通过从 T2 . TF . keras 扩展 t0 . model t1 类来创建多个图层。如需建立模型的详细资讯，请参阅此处的 T4。

## t0t 1 号 5。模型学习的灵活性

t0tensorflow t1 可以使用 t2t 3 自动微分来计算损失函数相对于模型参数的梯度。T4 TF。gradienttape 【t 5 在一个上下文中创建一个磁带，该上下文用于跟踪每次在该磁带上计算时记录的渐变。为了理解这一点，让我们通过扩展 t8tf . keras . model t9t 类，在较低的级别定义一个模型。T10 型

```
from tensorflow.keras import Model

class CustomModel(Model):
    def __init__(self):
        super(CustomModel, self).__init__()
        self.do1 = tf.keras.layers.Dropout(rate=0.2, input_shape=(14,))
        self.fc1 = tf.keras.layers.Dense(units=64, activation='relu')
        self.do2 = tf.keras.layers.Dropout(rate=0.2)
        self.fc2 = tf.keras.layers.Dense(units=64, activation='relu')
        self.do3 = tf.keras.layers.Dropout(rate=0.2)
        self.out = tf.keras.layers.Dense(units=1, activation='sigmoid')

    def call(self, x):
        x = self.do1(x)
        x = self.fc1(x)
        x = self.do2(x)
        x = self.fc2(x)
        x = self.do3(x)
        return self.out(x)

model = CustomModel() 
```

Enter fullscreen mode Exit fullscreen mode

请注意，此模型的拓扑结构与您之前定义的完全相同。为了使用自动区分来训练这个模型，您需要用不同的方式定义损失函数和最佳:￥t0

```
loss_func = tf.keras.losses.BinaryCrossentropy()
optimizer = tf.keras.optimizers.Adam() 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将定义用于衡量包括其培训的网络性能的度量标准。这里的性能是指模型的损失和精度。t0￥0

```
# Average the loss across the batch size within an epoch train_loss = tf.keras.metrics.Mean(name='train_loss')
train_acc = tf.keras.metrics.BinaryAccuracy(name='train_acc')

valid_loss = tf.keras.metrics.Mean(name='test_loss')
valid_acc = tf.keras.metrics.BinaryAccuracy(name='valid_acc') 
```

Enter fullscreen mode Exit fullscreen mode

t0tf . data t1 提供了确定输入数据流水线的实用方法。当您处理大量数据时，这特别有用。

现在，您将定义一个数据生成器，在模型学习期间生成数据包。t0￥0

```
X_train, X_test = X_train.astype(np.float32), X_test.astype(np.float32)
y_train, y_test = y_train.astype(np.int64), y_test.astype(np.int64)
y_train, y_test = y_train.reshape(-1, 1), y_test.reshape(-1, 1)

# Batches of 64 train_ds = tf.data.Dataset.from_tensor_slices((X_train, y_train)).batch(64)
test_ds = tf.data.Dataset.from_tensor_slices((X_test, y_test)).batch(64) 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已准备好使用 t0tf 培训模型。绿色 T1。首先，你定义一种方法，用你刚才定义的数据训练模型，使用 T2 . TF . data . dataset . T3。您还可以使用 t4tf . function t5tf . function 编写模型学习步骤，以加快计算速度。

## t0t 1 号 6。TensorFlow 数据集

单独的 t0datasets t1t 模块用于处理复杂的网络模型。你在前面的例子中已经看过了。在本节中，您将看到如何以您需要的方式加载 MNIST 数据集。

您可以使用 T2 pip T3 安装 t0tenorflow _ datasets t1 库。一旦安装好，您就可以开始工作了。它提供了几个辅助功能，可以帮助您灵活地准备数据集构建流水线。您可以在这里了解更多关于 T4 的信息，在这里了解 T5，在这里了解 T6。现在，您将看到您如何构建一个数据输入流水线，以便在 MNIST 数据集中加载。T8 型

```
import tensorflow_datasets as tfds

# You can fetch the DatasetBuilder class by string mnist_builder = tfds.builder("mnist")

# Download the dataset mnist_builder.download_and_prepare()

# Construct a tf.data.Dataset: train and test ds_train, ds_test = mnist_builder.as_dataset(split=[tfds.Split.TRAIN, tfds.Split.TEST]) 
```

Enter fullscreen mode Exit fullscreen mode

您可以忽略警告。请注意 t0 tennsflow _ datasets t1 如何处理传送带。T2￥2

```
# Prepare batches of 128 from the training set ds_train = ds_train.batch(128)

# Load in the dataset in the simplest way possible for features in ds_train:
    image, label = features["image"], features["label"] 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以显示您上传到的图像集合中的第一个图像。请注意，t0 tennsflow _ datasets t1 处于活动模式，并且是基于图形配置的。T2￥2

```
import matplotlib.pyplot as plt
%matplotlib inline

# You can convert a TensorFlow tensor just by using
# .numpy() plt.imshow(image[0].numpy().reshape(28, 28), cmap=plt.cm.binary)
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

## t0t 1 号 7。自动混合精度政策

混合精度政策是 NVIDIA 去年提出的。文章的原文在此处为 t0 t1。简要介绍了混合精度政策的基本思路:使用半精(FP16)和全精(FP32)的混合，并利用两个世界的优势。她在学习非常深层的神经网络(就像在时间和成绩方面一样)方面表现惊人。

如果您在支援 CUDA 的 GPU 环境(例如，Volta Generation、Tesla T4)中工作，并且已安装 t0 tensorflow 2 . 0 t1 图形

`os.environ['TF_ENABLE_AUTO_MIXED_PRECISION'] = '1'`

这将自动分别分配 TensorFlow 图的操作。您将能够看到您的型号性能的良好飞跃。您还可以通过混合精度策略优化基本的 TensorFlow 操作。请参阅本文 t0 t1，了解更多信息。

## t0t18 号。分布式学习

t0tensorflow 2 . 0t 1 可让您轻松地将学习分散到多个 GPU。当你不得不遇到超负荷时，这对生产目的特别有用。这就像把你的训练单元放在 T2 With T3 单元一样简单。

您首先将分配策略指定为:T0

`mirrored_strategy = tf.distribute.MirroredStrategy()`

镜像策略为每个图形处理器创建一个副本，模型变量同样反映在不同的图形处理器中。现在，您可以使用特定策略如下:T0

```
with mirrored_strategy.scope():
    model = tf.keras.Sequential([tf.keras.layers.Dense(1, input_shape=(1,))])
    model.compile(loss='mse', optimizer='sgd')
    model.fit(X_train, y_train,
             validation_data=(X_test, y_test),
             batch_size=128,
             epochs=10) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，只有在同一系统上配置了多个图形处理器时，上述代码段才会有用。有几种分配策略可以配置。如需详细资讯，请参阅此处的 T2。

## 9。TensorBoard в Jupyter 笔记本

这也许是这个阿普代尔最激动人心的部分。您可以通过 T2 tensorboard T3 直接在 t0 jupyter notebook t1 中可视化模型学习。新的 T4 tensorboard t5 有许多有趣的功能，如内存分析、查看图像数据，包括不准确性矩阵、概念模型图等。如需详细资讯，请参阅此处的 T6。

在本节中，您将配置您的环境，使 t0 tensorboard t1 显示在 T2 jupyter T3 中。您需要先下载 T4 tenorboard . note book T5:T6 记事本的扩展

```
%load_ext tensorboard.notebook 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将使用 T2 . TF . keras . call backs t 3 模块定义 t0 tensorboard t1 回调。T4￥的

```
from datetime import datetime
import os

# Make a directory to keep the training logs os.mkdir("logs")

# Set the callback logdir = "logs"
tensorboard_callback = tf.keras.callbacks.TensorBoard(log_dir=logdir) 
```

Enter fullscreen mode Exit fullscreen mode

使用 t0 sequential t1 API T2 TF . keras T3:t 重新构建模型

```
# Define the model model = tf.keras.Sequential([
    tf.keras.layers.Dropout(rate=0.2, input_shape=X_train.shape[1:]),
    tf.keras.layers.Dense(units=64, activation='relu'),
    tf.keras.layers.Dropout(rate=0.2),
    tf.keras.layers.Dense(units=64, activation='relu'),
    tf.keras.layers.Dropout(rate=0.2),
    tf.keras.layers.Dense(units=1, activation='sigmoid')
])

# Compile the model model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy']) 
```

Enter fullscreen mode Exit fullscreen mode

培训和测试套件已为各种目的进行了修改。所以再把它们分开会有什么好处:￥t0

```
# Split X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=7) 
```

Enter fullscreen mode Exit fullscreen mode

你们都准备好了吗

```
# The TensorBoard extension %tensorboard --logdir logs/
# Pass the TensorBoard callback you defined model.fit(X_train, y_train,
         validation_data=(X_test, y_test),
         batch_size=64,
         epochs=10,
         callbacks=[tensorboard_callback],
         verbose=False) 
```

Enter fullscreen mode Exit fullscreen mode

t0 tensorboard t1 工具栏必须下载到 T2 jupyter T3，您才能跟踪培训度量和验证