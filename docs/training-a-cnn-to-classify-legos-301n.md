# 训练 CNN 对乐高玩具进行分类

> 原文：<https://dev.to/ladvien/training-a-cnn-to-classify-legos-301n>

本文是系列文章的一部分。它应该解释了用于训练我们的卷积神经网络(CNN) LEGO 分类器的代码。

如果你想和这篇文章一起编码，我们已经在 Google 的 Colab 中提供了:

*   [乐高分类器](https://colab.research.google.com/drive/1gD51CXRngVZhTO9464aFKHV2pejsVPcx)

或者如果您想在本地运行代码:

*   [乐高 _ 分类器](https://github.com/Ladvien/lego_sorter)

这是一个 WIP，所以如果你遇到任何问题，请在下面评论。

## 分类器代码:

我们的代码始于在 Kaggle 上找到的一个笔记本:

*   [CCN 的乐高积木图片](https://www.kaggle.com/twhitehurst3/lego-brick-images-keras-cnn-96-acc)

然而，代码中存在问题。我重写了大部分，所以我不确定原著还剩多少。尽管如此，引用你的来源！

其中一些问题是:

*   它使用了一个比实际需要更复杂的模型。
*   代码格式乱七八糟。
*   目标产量和损失不匹配。

这是最后一个非常棘手但非常关键的问题。这是一个很难捕捉的错误，不准确地报告高准确性。下面我会详细讨论，但这是我自己掉进的陷阱。不管这些问题，这是一个很好的启动代码，因为我们从来没有和 CNN 合作过。

### 项目设置(仅限本地)

如果您在本地运行此代码，您将需要执行以下操作。

输入命令提示符并导航到您的主目录。我们将克隆项目存储库(repo)，然后在项目文件夹中克隆数据存储库。

```
git clone https://github.com/Ladvien/lego_sorter.git
cd lego_sorter
git clone https://github.com/Ladvien/lego_id_training_data.git 
```

Enter fullscreen mode Exit fullscreen mode

然后，打开您的 Python IDE，将您的目录设置为`./lego_sorter`，并打开`lego_classifier_gpu.py`。

最后，如果你看到一个像这样的单元格:

```
!git clone https://github.com/Ladvien/lego_id_training_data.git
!mkdir ./data
!mkdir ./data/output
!ls 
```

Enter fullscreen mode Exit fullscreen mode

跳过或删除它们，运行 Colab 笔记本时需要它们。当然，如果您运行的是 Colab 笔记本，请确保执行它们。

### 分类器代码:需要的库

下面是我们使用的代码。回顾它，我看到了一些清理它的方法，所以知道它可能会在未来发生变化。

以下是为什么需要这些库的原因分析:

*   `tensorflow` -谷歌主要的深度学习库，它是该项目的核心。
*   一个从创建机器学习模型中抽象出大量细节的库。
*   我们将这些类写到文件中以备后用。
*   一个用于可视化你的训练课程的库。
*   这是打开你的网页浏览器到 Tensorboard。

```
 # Import needed tools. import os
import matplotlib.pyplot as plt
import json
import numpy as np
from scipy import stats

# Import Keras import tensorflow as tf
import tensorflow.keras
from tensorflow.keras.layers import Dense,Flatten, Dropout, Lambda
from tensorflow.keras.layers import SeparableConv2D, BatchNormalization, MaxPooling2D, Conv2D, Activation
from tensorflow.compat.v1.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping, TensorBoard, CSVLogger, ReduceLROnPlateau
from tensorflow.keras.preprocessing import image

# Tensorboard from tensorboard import program
import webbrowser
import time 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在本地学习这些代码，并且在设置这些库时需要帮助，请在下面留下您的评论。我抓住你了。

### 分类器代码:参数

参数部分是培训的核心，我将解释参数的作用，并突出显示您可能想要调整的参数。

```
continue_training       = False
initial_epoch           = 0
clear_logs              = True

input_shape             = (300, 300, 3) # This is the shape of the image width, length, colors image_size              = (input_shape[0], input_shape[1]) # DOH! image_size is (height, width) train_test_ratio        = 0.2
zoom_range              = 0.1
shear_range             = 0.1

# Hyperparameters batch_size              = 16
epochs                  = 40
steps_per_epoch         = 400
validation_steps        = 100 
optimizer               = 'adadelta' 
learning_rate           = 1.0
val_save_step_num       = 1

path_to_graphs          = './data/output/logs/'
model_save_dir          = './data/output/'
train_dir               = './lego_id_training_data/gray_train/'
val_dir                 = './lego_id_training_data/gray_test/' 
```

Enter fullscreen mode Exit fullscreen mode

#### 参数:训练时段

前几个参数有助于从中断的训练会话中继续。例如，如果您的会话在 epoch 183 处中断，那么您可以设置`continue_training` = `True`和`initial_epoch` = 184，然后执行该脚本。然后，这将加载最后一个最佳模型，并从您停止的地方恢复训练。最后，如果你设置`clear_logs` = `True`，那么它会清除 Tensorboard 信息。因此，如果您继续一个会话，您会希望将其设置为`False`。

这部分是 WIP，有几个问题。首先，Tensorboard 日志应该保存在单独的文件夹中，不应该需要清除。此外，当继续训练会话时，它会重置最佳验证分数(在过度拟合之前跟踪以保存您的模型),从而导致性能暂时下降。

#### 参数:图像数据

`input_shape`指的是图像的尺寸:高度、宽度和颜色(RGB)值。`image_size`来源于`input_shape`。

注意，我在`image_size`早期遇到的一个问题。我尝试了非正方形的图像(这不利于训练，不推荐 CNN 使用)，艰难地发现大多数宽度和高度的图像参数颠倒了顺序。

例如，这是需要的:

```
...
    val_dir,
    target_size = (height_here, width_here),
... 
```

Enter fullscreen mode Exit fullscreen mode

我在期待:

```
...
    val_dir,
    target_size = (width_here, height_here),
... 
```

Enter fullscreen mode Exit fullscreen mode

这让我很难受，因为我用过的大多数框架都是先宽后高。我的意思是，甚至当我们谈论屏幕分辨率时，我们列出宽度*然后是*高度(例如，`1920x1080`)。使用矩形图像时要小心。总是 RTFM(因为，显然，我没有)。

`train_test_ratio`控制测试模型时保留多少图像。我不得不再次运行代码，但我不认为这是必要的。因为预处理脚本创建了一个包含验证图像的文件夹。嗯，我会把它加到我的[科技债务](https://en.wikipedia.org/wiki/Technical_debt)清单上。

`zoom_range`参数控制脚本应该将图像放大到什么程度。最后，`shear_range`控制在将图像传送给 CNN 之前，从图像的边缘切掉多少。

[![](img/764314226f00793f097d49da5814255f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RVnNhUvk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/lego_classifier/batch.png)

#### 参数:CNN 超参数

“超参数”是机器学习工程师所说的可能影响神经网络训练结果的参数。

*   什么是超参数？

以下是我们公开的超参数:

`batch_size`指的是在更新每个[感知器](https://towardsdatascience.com/what-the-hell-is-perceptron-626217814f53)的权重之前，神经网络应该尝试预测的照片数量。**注意**，最大批量通常受你的 GPU RAM 限制。在本地，我使用一个 6GB 内存的`GTX 1060`——我不能得到一个大于 6GB 的批处理。YMMV。

在认为一个历元完成之前要经历的批次数量。一个`epoch`是一个任意数字，表示在认为训练完成之前要经历多少个`batches` * `steps_per_epoch`。

因此，培训的时间长度将是`training schedule = epochs * steps_per_epoch * batch_size`

`validation_steps`是训练数据中用于验证当前权重的批次数。这将在我们`fit`(训练)我们的分类器和`evaluate`它时使用。

`optimizer`是所使用的优化器的名称。这是训练的核心，因为它负责决定如何在每批训练后更新重量。

我将代码设置为只使用三个优化器中的一个，或者是`adam`、`adagrad`、`sgd`。

```
def get_optimizer(optimizer, learning_rate = 0.001):
    if optimizer == 'adam':
        return tensorflow.keras.optimizers.Adam(lr = learning_rate, beta_1 = 0.9, beta_2 = 0.999, epsilon = None, decay = 0., amsgrad = False)
    elif optimizer == 'sgd':
        return tensorflow.keras.optimizers.SGD(lr = learning_rate, momentum = 0.99) 
    elif optimizer == 'adadelta':
        return tensorflow.keras.optimizers.Adadelta(lr=learning_rate, rho=0.95, epsilon=None, decay=0.0) 
```

Enter fullscreen mode Exit fullscreen mode

这里有更多关于优化器的信息。

易于阅读:

*   [随机梯度下降](https://en.wikipedia.org/wiki/Stochastic_gradient_descent)
*   [亚当](https://machinelearningmastery.com/adam-optimization-algorithm-for-deep-learning/)
*   阿达格勒

主要来源:

*   [亚当](https://arxiv.org/abs/1412.6980)
*   阿达格勒

据我所知，使用`adagrad`而不是`adam`的主要原因是`adagrad`的`learning_rate`会自然地修改自己，以更有利于最佳收敛。

但是，有很多优化器。很多在喀拉斯都有:

*   随机梯度下降
*   RMSprop
*   阿达格拉德
*   阿达德尔塔
*   圣经》和《古兰经》传统中）亚当（人类第一人的名字
*   那达慕
*   阿达马斯

Keras 关于优化器的文档:

*   [Keras Optimizers](https://keras.io/optimizers/)

`learning_rate`控制当感知器做出错误预测时，优化器应该如何彻底改变感知器的权重。太高就不会收敛(学习)太低也需要一段时间。

你会发现很多文档说，“优化器的默认学习速率是最好的，它不需要改变。”我发现这个建议基本上是正确的。在这个项目中使用`adam`的默认设置`0.001`时，我确实遇到了一个问题。神经网络就是不学习——我不得不把它放到大约`0.0001`的位置，这样做要好得多。

学习速度入门读物:

*   [如何选择最佳学习率](https://medium.com/octavian-ai/which-optimizer-and-learning-rate-should-i-use-for-deep-learning-5acb418f9b2)

这并不详尽。如果你有兴趣调整优化器或学习速度，谷歌和阅读尽可能多。

最后，`val_save_step_num`控制在验证器测试您的模型在测试集上是否表现良好之前，应该经过多少个训练时期。我们有这样的代码设置，如果验证器说这个模型比这个培训会话中的任何先前的测试执行得更好，那么它将自动保存这个模型。

### 分类器代码:数据准备

`make_dir`允许创建一个目录，如果它不存在的话。然后，我们使用它来创建我们的模型保存目录。

```
def make_dir(dir_path):
    if not os.path.exists(dir_path):
        os.mkdir(dir_path)

# Create needed dirs make_dir(model_save_dir) 
```

Enter fullscreen mode Exit fullscreen mode

下一位将`train_gen`找到的类保存到一个文件中。当我们试图快速地将模型部署到生产中时，这是非常有用的。

```
# Save Class IDs classes_json = train_gen.class_indices
num_classes = len(train_gen.class_indices) 
```

Enter fullscreen mode Exit fullscreen mode

这将一个对象保存到一个`json`文件中。密钥(如“2456”)代表乐高提供的代码。并且该值是由分类器分配的数值类。

```
{  "2456":  0,  "3001":  1,  "3002":  2,  "3003":  3,  "3004":  4,  "3010":  5,  "3039":  6,  "32064":  7,  "3660":  8,  "3701":  9  } 
```

Enter fullscreen mode Exit fullscreen mode

训练完模型后，我们可以做以下事情:

```
predicted_lego_code = json_classes[model.predict()] 
```

Enter fullscreen mode Exit fullscreen mode

并且模型将返回它已经识别的乐高类。

### 分类器代码:数据生成器

在处理 CNN 时，通常情况下，训练数据太大，无法一次性放入 RAM，更不用说 GPU RAM 了。

而是使用一个`DataGenarator`。一个`DataGenerator`是由`Keras`提供的类，它以可管理的块加载训练数据，在训练期间提供给你的模型。让我们用它来演示一下。

我们初始化`ImageDataGenerator`-`keras`'`DataGenerator`的子类。然后，我们创建两个`flows`，一个用于将数据从培训文件夹加载到模型中。另一个是相同的，但是，它从测试文件夹加载数据。后者将用于验证模型。

我们的`ImageDataGenerator`中使用的参数:

*   `shear_range` -控制图像边缘被修剪的部分占整个图像的百分比。这有助于快速减小图像的大小(从而提高训练速度)。
*   `zoom_range` -是在将图像输入模型之前要放大的距离。
*   `horizontal_flip` -如果设置为`true`，图像随机水平镜像。这基本上是你训练图像的两倍。但是，它不应该在所有情况下都使用。如果目标有“惯用手”，那么这将破坏准确性。一个简单的例子就是训练 CNN 来判断棒球运动员是左撇子还是右撇子。
*   `validation_split` -确定保留用于验证的图像的百分比。

```
# These Keras generators will pull files from disk
# and prepare them for training and validation. augs_gen = ImageDataGenerator (
    shear_range = shear_range,  
    zoom_range = shear_range,        
    horizontal_flip = True,
    validation_split = train_test_ratio
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，`ImageDataGenerator.flow_from_directory`方法的参数:

*   这只咬了我。它是作为一个元组的图像的大小(例如，“(150，150)”)。**它期望高度*然后*宽度。**
*   `batch_size` -这是在更新权重之前加载到 GPU RAM 中并经过训练的图像数量。
*   `class_mode` - **一个重要的论点。**这为模型的预测尝试设定了目标。`sparse`表示目标为`LabelEncoded`。

下面是一个我一直暗示的悲惨故事。

如果你有不止一个类要预测，像我们一样，你有两个选择。不是`sparse`就是`categorical`。

**稀疏**

| 目标 |
| --- |
| one |
| Two |
| three |
| Two |

**分类的**

| one | Two | three |
| --- | --- | --- |
| one | Zero | Zero |
| Zero | one | Zero |
| Zero | Zero | one |
| Zero | one | Zero |

然而，这就是原始代码中的 bug 所在。它将目标设置为分类目标，但是，它使用`binary_crossentropy`作为损失函数。这个错误很难捕捉——它相当于机器学习中的“那里”和“他们的”错误。

目标和损失函数的不匹配也没有帮助。该模型仍然可以顺利编译和训练。但是`categorical`目标和`binary_crossentropy`的残酷组合导致了极高的精度和*极差的生产精度。问题是损失函数只有*查看上面`categorical`表中的`1`列。如果当第一列是`1`时，模型预测它是`1`，那么它认为它是“正确的”否则，如果当列`1`为`0`时，模型预测为`0`，那么模型仍然认为它是正确的。毕竟“不是`1`”需要说明的是，这个模型没有错——我们只是给了它错误的目标标签。**

这就是典型的[“热狗，不是热狗”](https://medium.com/@timanglade/how-hbos-silicon-valley-built-not-hotdog-with-mobile-tensorflow-keras-react-native-ef03260747f3)问题。

简而言之，如果你觉得你的模型很快训练到一个好得令人难以置信的精确度，那就是真的。

```
train_gen = augs_gen.flow_from_directory (
    train_dir,
    target_size = image_size, # THIS IS HEIGHT, WIDTH
    batch_size = batch_size,
    class_mode = 'sparse',
    shuffle = True
)

test_gen = augs_gen.flow_from_directory (
    val_dir,
    target_size = image_size,
    batch_size = batch_size,
    class_mode = 'sparse',
    shuffle = False
) 
```

Enter fullscreen mode Exit fullscreen mode

### 分类器代码:建立模型

快完成了。出于两个原因，我不打算讨论 CNN 的设计。我仍在学习这一切意味着什么，其他地方还有更好的解释。

*   [卷积神经网络入门](https://medium.com/abraia/getting-started-with-image-recognition-and-convolutional-neural-networks-in-5-minutes-28c1dfdd401)

然而，有几件事对我们很重要。

*   是我们试图分类的乐高积木的数量。
*   最后一层的`activation`控制 CNN 的输出类型。它需要与`optimizer`相对应，并且需要与`DataGenerators`的`class_mode`设置相对应。
*   `build_model`是一个方便函数。它允许我们快速建立一个 Keras CNN 模型并返回使用。
*   `model.summary`输出模型的文本图。
*   `model.compile`为训练准备整个模型。

```
def build_model(opt, input_shape, num_classes):
    model = tf.keras.models.Sequential()
    model.add(tf.keras.layers.Conv2D(32, (3, 3), input_shape = input_shape))
    model.add(tf.keras.layers.Activation('relu'))
    model.add(tf.keras.layers.Dropout(0.2))
    model.add(tf.keras.layers.MaxPooling2D(pool_size=(2, 2)))

    model.add(tf.keras.layers.Conv2D(64, (3, 3)))
    model.add(tf.keras.layers.Activation('relu'))
    model.add(tf.keras.layers.Dropout(0.2))
    model.add(tf.keras.layers.MaxPooling2D(pool_size=(2, 2)))

    model.add(tf.keras.layers.Conv2D(128, (3, 3)))
    model.add(tf.keras.layers.Activation('relu'))
    model.add(tf.keras.layers.Dropout(0.2))
    model.add(tf.keras.layers.MaxPooling2D(pool_size=(2, 2)))

    model.add(tf.keras.layers.Flatten())  # this converts our 3D feature maps to 1D feature vectors
    model.add(tf.keras.layers.Dense(256))
    model.add(tf.keras.layers.Activation('relu'))

    model.add(tf.keras.layers.Dropout(0.2))

    model.add(tf.keras.layers.Dense(num_classes, activation = 'softmax'))
    return model

#################################
# Create model
################################# 
selected_optimizer = get_optimizer(optimizer, learning_rate)

model = build_model(selected_optimizer, input_shape, num_classes)
model.summary()

model.compile(
    loss = 'sparse_categorical_crossentropy',
    optimizer = selected_optimizer,
    metrics = ['accuracy']
) 
```

Enter fullscreen mode Exit fullscreen mode

### 分类器代码:创建回调

在我们执行培训之前，我们应该设置 Keras 回调。

*   [Keras callbacks](https://keras.io/callbacks/)

这些预先编写的回调函数将被传递给模型，并在整个培训过程中的重要点上执行。

*   `ModelCheckpoint`该方法在`val_save_step_num`设定的时期数后调用。它运行一个验证批次，并将`val_loss`与其他过去的分数进行比较。如果它是目前最好的`val_loss`，这个方法将保存模型，更重要的是，保存`best_model_weights`路径的权重。
*   `TensorBoard`打开一个 [TensorBoard](https://www.tensorflow.org/tensorboard/r1/summaries) 会议，用于可视化训练会议。

```
best_model_weights = model_save_dir + 'base.model'

checkpoint = ModelCheckpoint(
    best_model_weights,
    monitor = 'val_loss',
    verbose = 1,
    save_best_only = True,
    mode = 'min',
    save_weights_only = False,
    period = val_save_step_num
)

tensorboard = TensorBoard(
    log_dir = model_save_dir + '/logs',
    histogram_freq=0,
    batch_size=16,
    write_graph=True,
    write_grads=True,
    write_images=False,
) 
```

Enter fullscreen mode Exit fullscreen mode

在将任何`KerasCallbacks`添加到培训课程之前，必须将它们收集到一个列表中，因为这是培训方法接收
的方式

```
callbacks = [checkpoint, tensorboard] 
```

Enter fullscreen mode Exit fullscreen mode

### 分类器编码:训练

恶心，我需要重写这部分代码。这是一种在中断后重新开始训练的蹩脚方式。

它会检查您是否表示要继续会话。然后，它加载最佳保存的模型，并根据测试数据对其进行评估。

```
if continue_training:
    model.load_weights(best_model_weights)
    model_score = model.evaluate_generator(test_gen, steps = validation_steps)

    print('Model Test Loss:', model_score[0])
    print('Model Test Accuracy:', model_score[1]) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们走到了尽头。以下函数执行培训会话。它将初始化回调，然后为设定的纪元数进行训练。每个时期它都从`train_gen` ( `DataGenerator`)提取一批数据，尝试预测，然后根据结果更新权重。在`checkpoint`回调中设置的时期数之后，模型将从`test_gen`中提取数据，这些数据它以前“从未”见过，并尝试预测。如果测试的结果比之前任何测试的结果都好，那么模型将被保存。

```
history = model.fit_generator(
    train_gen, 
    steps_per_epoch  = steps_per_epoch, 
    validation_data  = test_gen,
    validation_steps = validation_steps,
    epochs = epochs, 
    verbose = 1,
    callbacks = callbacks
) 
```

Enter fullscreen mode Exit fullscreen mode

呼，就这样。上述模型在 20 分钟后对我来说达到了 98%的验证准确率。然而，还有很多事情要做。正如我以前说过的，“仅仅因为我们有高验证准确性并不意味着我们将有高生产准确性。”将来，我会写关于快速生成训练数据的转盘。很俏皮。基于 NEMA17、斜坡套件和带 RPi 摄像机的 RPi。这是炸弹网站。