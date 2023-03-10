# 将 Keras 模型转换为 TensorFlow.js

> 原文：<https://dev.to/paulsp94/convert-keras-models-to-tfjs-2k3>

如果您使用 TensorFlow (Python)开发了 ML 应用程序，并希望将它们移动到 web 上，现在您可以使用 TensorFlow js 框架轻松完成。没有必要重新训练模型，只要它们不使用任何定制层，这是目前仍然不支持的。

要开始转换，您必须将模型加载到 python 中，例如:

```
from tensorflow.keras.applications import resnet50

model = resnet50.ResNet50(include_top=True, weights='imagenet')
model.save('./ResNet50.h5') 
```

Enter fullscreen mode Exit fullscreen mode

加载模型后，将其包括权重保存到 hdf5 文件中。[1]

对于模型的转换，你要安装 tensorflowjs python 包:`pip install tensorflowjs`

然后，您可以使用以下命令转换 Keras 模型。

```
tensorflowjs_converter \
    --input_format=keras \
    --output_format=tfjs_layers_model \
    ./ResNet50.h5 \
    ./ResNet50 
```

Enter fullscreen mode Exit fullscreen mode

该命令将您的模型转换为一个 model.json 文件，其中包含架构配置和多个存储了权重的`.bin`文件。

命令完成后，您可以通过下面几行代码将模型导入到 node 中。

```
const tf = require('@tensorflow/tfjs-node');

const ResNet50URI = `file:///${__dirname}/ResNet50/model.json`
const ResNet50 = await tf.loadLayersModel(ResNet50URI); 
```

Enter fullscreen mode Exit fullscreen mode

或者在网络中:

```
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@1.0.0/dist/tf.min.js"></script> 
<script> 
    const ResNet50URI = `file:///${__dirname}/ResNet50/model.json`
    const ResNet50 = await tf.loadLayersModel(ResNet50URI);
</script> 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以像往常一样使用`predict`函数来执行模型。

做那件事时，有一件事你需要注意。由于图层命名的原因，每次加载模型时都必须重启 python 内核。这个 [GitHub 问题](https://github.com/tensorflow/tfjs/issues/755#issuecomment-433022367)很好的说明了问题。
此外，TensorFlow (python)中构建的一些定制模型还不能转换。只能转换在 TensorFlow js 中实现了层、初始化器和操作的模型。

结果可以在这里找到:
[https://github . com/paulsp 94/tfjs _ resnet _ imagenet/tree/master/resnet 50](https://github.com/paulsp94/tfjs_resnet_imagenet/tree/master/ResNet50)