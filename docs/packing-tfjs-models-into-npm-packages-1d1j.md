# 将 TensorFlow.js 模型打包到 npm 包中

> 原文：<https://dev.to/paulsp94/packing-tfjs-models-into-npm-packages-1d1j>

目前我发了一篇关于[如何将 Keras 模型转换成 TensorFlow js one](https://dev.to/paulsp94/convert-keras-models-to-tfjs-2k3) 的帖子。但是一旦您转换了模型，在不同的项目之间共享它并轻松部署它的最简单的方法是什么。
为了解决这个问题，我考虑用一个简单的分类包装器将模型打包到一个 npm 包中。

为了实现这一目标，我们需要:

*   resnet50 模型预训练(tfjs)
*   模型输出的拟合标签
*   我们的模型包装
*   各种配置文件(npm 等。)

首先，我们通过运行`npm init`来设置 npm 包配置。在这个过程中，我们需要提供包的名称、版本、GitHub 和其他一些简单的信息。之后，我们已经有了已经可以发布的基本 npm 包。
然后我只使用一个基本的巴别塔配置，这样就可以在 ES6 中实现我的代码。并在`package.json`中添加一个`prepare`命令来编译要发布的文件。

```
"scripts":  {  "prepare":  "node_modules/@babel/cli/bin/babel.js src --out-dir lib"  } 
```

Enter fullscreen mode Exit fullscreen mode

在我之前的[帖子](https://dev.to/paulsp94/convert-keras-models-to-tfjs-2k3)中转换的模型现在放在`ResNet50`文件夹下。为了解码模型预测，我将原始的 [Keras labels](https://github.com/raghakot/keras-vis/blob/master/resources/imagenet_class_index.json) 文件的一个稍微简化的版本添加到文件夹 assets 下的 repo 中。现在我们可以开始主要任务了，围绕我们的模型构建一个简单易用的包装器。首先，我们需要安装我们的依赖项，`@tensorflow/tfjs`、`@tensorflow/tfjs-node`和`jimp`。虽然很清楚我们需要 tfjs 模块来做什么，但 jimp 用于将我们的图像加载到一个数组中，使其可以转换为张量。
现在，我们用一个简短的构造函数
构建我们的`ResNetPredictor`类

```
constructor() {
  this.model;
  this.labels = labels;
  this.modelPath = `file:///${__dirname}/../ResNet50/model.json`;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为`tf.loadLayersModel()`函数是我们需要的异步函数，不能在构造函数中调用，所以现在要用一点小技巧。我们构建一个异步工厂方法来初始化我们的对象。

```
initialize = async () => {
  this.model = await tf.loadLayersModel(this.modelPath);
};

static create = async () => {
  const o = new ResNetPredictor();
  await o.initialize();
  return o;
}; 
```

Enter fullscreen mode Exit fullscreen mode

当我们想在外部脚本中初始化我们的对象时，我们必须运行:

```
load = async () => {
  return ResNetPredictor().create();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要一个函数来从路径或 URL 加载图像，并将其转换为张量，这样我们就可以将它输入到我们的模型中。这就是我们需要 jimp 打开我们的图像的地方。

```
loadImg = async imgURI => {
  return Jimp.read(imgURI).then(img => {
    img.resize(224, 224);
    const p = [];
    img.scan(0, 0, img.bitmap.width, img.bitmap.height, function test(
      x,
      y,
      idx
    ) {
      p.push(this.bitmap.data[idx + 0]);
      p.push(this.bitmap.data[idx + 1]);
      p.push(this.bitmap.data[idx + 2]);
    });

    return tf.tensor4d(p, [1, img.bitmap.width, img.bitmap.height, 3]);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

该函数获取任何 URI，并从该地址加载图像。然后，将图像的大小调整为 224x224 像素，使其适合我们的模型，我们从图像位图生成一维数组。然后，这个数组被加载到一个具有正确维数的张量中。我们一开始需要第四维是因为`predict`函数需要一批张量来预测。

现在我们可以构建`classify`函数，这是最后一个有趣的函数，它生成包的值。

```
classify = async imgURI => {
  const img = await this.loadImg(imgURI);
  const predictions = await this.model.predict(img);
  const prediction = predictions
    .reshape([1000])
    .argMax()
    .dataSync()[0];
  const result = this.labels[prediction];
  return result;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们用图像的 URI 调用这个函数，我们要对其进行分类。然后图像被加载并被放入模型以获得预测。从预测中，我们得到张量中最大值的 id，并在 labels 对象中查找它。然后返回这个结果，并有希望预测正确的对象。

最后，我的项目结构如下所示。

```
.
├── assets
│   └── labels.json
├── .gitignore
├── .babelrc
├── package.json
├── package-lock.json
├── README.md
├── LICENSE
├── ResNet50
│   ├── group1-shard1of25.bin
                .
                .
                .
│   ├── group1-shard25of25.bin
│   └── model.json
└── src
    └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`npm run prepare && npm publish`发布我们的包了。

这里有一个简短的 CodeSandbox 示例，说明如何使用这个包。

[https://codesandbox.io/embed/jolly-khorana-qzwoq](https://codesandbox.io/embed/jolly-khorana-qzwoq)

如果你有任何公开的问题，例如关于我的具体巴别塔配置或其他任何东西，请随意查看我的 GitHub repo。如果你试用 npm 包，并给我关于可用性的反馈和任何改进的想法，我也会很高兴。

github:[https://github.com/paulsp94/tfjs_resnet_imagenet](https://github.com/paulsp94/tfjs_resnet_imagenet)T2:[https://www.npmjs.com/package/resnet_imagenet](https://www.npmjs.com/package/resnet_imagenet)