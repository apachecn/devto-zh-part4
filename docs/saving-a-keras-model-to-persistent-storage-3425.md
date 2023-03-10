# 将 Keras 模型保存到持久存储

> 原文：<https://dev.to/aveeksaha/saving-a-keras-model-to-persistent-storage-3425>

了解如何将 Keras 模型保存到持久存储或您的 Google drive，并从您停止的地方继续训练它。

# 简介

很多时候，深度学习模型可能需要几个小时、几天或几周的时间来训练，如果运行它的机器在训练结束前意外关闭，可能会导致所有工作都付诸东流。这就需要一种保存和加载模型的方法，以便可以从某个检查点继续训练。

幸运的是，Keras 为我们提供了 [`ModelCheckpoint`](https://keras.io/callbacks/#modelcheckpoint) 回调类。

# 保存模型

假设你有一个简单的神经网络

```
from keras import Sequential
from keras.layers import Dense

classifier = Sequential()
classifier.add(Dense(10, activation='relu', input_dim=56))
classifier.add(Dense(10, activation='relu'))
classifier.add(Dense(1, activation='sigmoid'))

classifier.compile(optimizer ='adam',
                loss='binary_crossentropy', metrics =['accuracy']) 
```

Enter fullscreen mode Exit fullscreen mode

在拟合模型之前，创建一个 ModelCheckpoint 对象，我们将在一分钟内检查每个参数的作用，但是现在，在这个例子中，在每个时期之后，模型将被保存到当前工作目录中的一个`hdf5`文件中。

```
from keras.callbacks import ModelCheckpoint

filepath="./weights-{epoch:02d}-{val_acc:.3f}.hdf5"
checkpoint = ModelCheckpoint(filepath, monitor='val_acc',
                               verbose=1, mode='max')

callbacks_list = [checkpoint] 
```

Enter fullscreen mode Exit fullscreen mode

现在，在拟合模型时传递回调列表

```
classifier.fit(X_train, Y_train, batch_size=100, epochs=20,
                          validation_data=(X_validation, Y_validation),
                          callbacks=callbacks_list) 
```

Enter fullscreen mode Exit fullscreen mode

一旦模型完成运行，您将能够在您的工作目录中看到 20 个`hdf5`文件，这些文件标有它们的纪元编号和验证准确性。

# 参数

有几个参数可以传递给 ModelCheckpoint，我们将在这里讨论它们

*   **filepath** :一个字符串，你要保存模型文件的路径。
*   **监控**:监控的数量。例如:val_acc，acc，val_loss，loss 等。
*   **详细**:详细模式，0 或 1。
*   **save_best_only** :如果为真，那么只有当新模型在监控数量上优于上一个保存的模型时，才保存该模型。
*   **save_weights_only** :如果为 True，则只保存模型的权重，否则保存整个模型(包括优化器状态)。
*   **模式**:{自动、最小、最大}之一。如果=True，则根据监控数量的最大化或最小化来决定是否覆盖当前保存文件。对于 val_acc，这应该是 max，对于 val_loss，这应该是 min，依此类推。在自动模式下，方向是从被监控量的名称自动推断出来的。
*   **周期**:检查点之间的时期数。

# 将模型保存到 Colab 笔记本上的 Google Drive

谷歌的 Colab 笔记本是开始原型化和创建神经网络和机器学习模型的一个很好的方式。如果你选择了 GPU 运行时，你会得到一个免费的 GPU，可以大大减少你的模型训练时间。

尽管 Colab 很棒，但它也有一些警告

1.  如果您与 internet 断开连接或关闭窗口超过 90 分钟，运行时将自动关闭或被回收。

2.  您在 VM 实例上一次最多有 12 个小时的时间。

在这两种情况下，您都可以重新连接到实例，但是所有的局部变量都将丢失。

在每个时期之后将模型保存到 Google Drive 使得从保存的最后一个时期重新开始训练变得容易。

谷歌让在 Colab 上做这件事变得超级容易。首先，我们必须允许 Colab 访问 Google Drive。

```
from google.colab import drive
drive.mount('/content/gdrive') 
```

Enter fullscreen mode Exit fullscreen mode

当你运行它时，它会生成一个链接，点击它。选择您想要安装其驱动器的 google 帐户。然后它会把你带到一个新的标签页，上面写着`Google Drive File Stream wants to access your Google Account`。点击`allow`,它会生成一个授权码，你必须将它粘贴到你刚才运行的代码下面的文本框中。粘贴代码并点击`enter`。

在刷新你的文件浏览器时，你应该看到一个名为`gdrive`的文件夹，那是挂载的驱动器文件夹。

要将它保存到您的驱动器中，代码几乎与上一节中的示例相同，但是文件名应该更改，以便模型存储在 drive 中。

```
# Create a folder in your drive called model before running this filepath="/content/gdrive/My Drive/model/weights-{epoch:02d}-{val_acc:.3f}.hdf5" 
```

Enter fullscreen mode Exit fullscreen mode

# 从保存的模型中恢复训练

如果保存了整个模型(不仅仅是权重)，那么模型可以从它停止的地方继续训练。

```
from keras.models import load_model

classifier = load_model("/content/gdrive/My Drive/model/weights-15-0.815.hdf5")

classifier.fit(X_train, Y_train, batch_size=100, epochs=20,
                           validation_data=(X_validation, Y_validation),
                          callbacks=callbacks_list,
                          initial_epoch = 15) 
```

Enter fullscreen mode Exit fullscreen mode