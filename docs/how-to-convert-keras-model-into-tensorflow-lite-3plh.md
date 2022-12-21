# 如何将 Keras 模型转换成 Tensorflow lite

> 原文：<https://dev.to/0xkoji/how-to-convert-keras-model-into-tensorflow-lite-3plh>

我刚刚发现了一个很棒的视频，用于将 Keras 模型转换为 Tensorflow lite 模型。

[https://www.youtube.com/watch?v=IJt_veyTlv8](https://www.youtube.com/watch?v=IJt_veyTlv8)

## 要求

我向`android daily life with ameer`询问了 Tensorflow 的版本。他告诉我他正在使用 tensorflow1.12\.
所以需要安装那个版本

```
!pip uninstall tensorflow
!pip install tensorflow==1.12 
```

Enter fullscreen mode Exit fullscreen mode

## 代码

```
from tensorflow.contrib import lite
converter = lite.TFLiteConverter.from_keras_model_file('/content/VGG_cross_validated.h5')
tfmodel = converter.convert()
open("model.tflite","wb").write(tfmodel) 
```

Enter fullscreen mode Exit fullscreen mode

我将尝试 Tensorflow lite 模型与边缘 TPU。

## 文章不再可用