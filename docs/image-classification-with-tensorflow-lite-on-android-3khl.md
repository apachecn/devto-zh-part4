# 基于 Android 平台的 TensorFlow Lite 图像分类

> 原文：<https://dev.to/brightdevs/image-classification-with-tensorflow-lite-on-android-3khl>

正如我在最近的[博客文章](https://brightinventions.pl/blog/are-we-ready-for-deep-learning-on-mobile-devices/)中所列，直接在移动设备上进行推理比使用云解决方案有很多优势。由于移动设备的计算限制，我们无法将所有可用的模型移植到移动设备上。不幸的是，它们中的许多不能在移动设备上工作，但这没关系，因为我们通常不需要这些笨重的移动设备。在这篇博文中，我们将创建一个简单的 Android 应用程序，它将利用在 ImageNet 上预先训练的 [MobileNetV2](https://arxiv.org/abs/1801.04381) 。

[![](img/8bf8542f2d156bb0a51dea9949dfa5e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxQEOUFI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/girm9r0x52frcwvlqflw.jpg)

## 让我们把手弄脏吧...

在我们的 Android 应用程序项目中，我们需要将 TFLite 依赖项添加到`build.gradle`文件中。

```
implementation 'org.tensorflow:tensorflow-lite:1.13.1' 
```

和下面提到的防止压缩模型的代码片段。

```
aaptOptions {  
    noCompress "tflite"  
    noCompress "lite"  
} 
```

下一步是得到图像分类问题的模型。一种方法是创建你自己的或者从[这里](https://www.tensorflow.org/lite/guide/hosted_models)拿一个预先训练好的，然后把它放到`assets`文件夹。我们将使用我为了这个演示而创建的定制的预培训 MobileNetV2。我们的模型将能够或者至少应该区分🌊*风筝冲浪、帆板*和*冲浪*🏄‍♂️.您可以从我的 [git 库](https://github.com/ares97/tflitedemo-mobilenetv2-imagenet-classification/tree/master/app/src/main/assets)下载这个模型和标签。

[![](img/8622fc92fb061343e0e3362ad48e5201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tL-cvr_H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ghtn0j1rwnhqkrbvg4q5.jpg)

### 潜入代码

为了利用准备好的模型，我们需要以某种方式将其导入代码。让我们使用`tf.lite.Interpreter`界面进行建模。

你可以用很多方法设置一个解释器，在 [TF 网站](https://www.tensorflow.org/lite/models/image_classification/android)上推荐的一个就是利用`MappedByteBuffer`。

```
@Throws(IOException::class)  
private fun getModelByteBuffer(assetManager: AssetManager, modelPath: String): MappedByteBuffer {  
    val fileDescriptor = assetManager.openFd(modelPath)  
    val inputStream = FileInputStream(fileDescriptor.fileDescriptor)  
    val fileChannel = inputStream.channel  
    val startOffset = fileDescriptor.startOffset  
    val declaredLength = fileDescriptor.declaredLength  
    return fileChannel.map(FileChannel.MapMode.READ_ONLY, startOffset, declaredLength) 
} 
```

然后...

```
model = Interpreter(loadModelFile(activity)) 
```

不幸的是，使用`MappedByteBuffer`作为参数已经被否决，并将在未来的版本中被删除，但你可以通过提供 ByteBuffer 来解决这个问题，这就像调用`loadModelFile`方法上的`.asReadOnlyBuffer()`一样简单。

下一步是读取带有标签的文件。您可以使用
轻松获取它们

```
@Throws(IOException::class)  
private fun getLabels(assetManager: AssetManager, labelPath: String): List<String> {  
    val labels = ArrayList<String>()  
    val reader = BufferedReader(InputStreamReader(assetManager.open(labelPath)))  
    while (true) {
        val label = reader.readLine() ?: break
        labels.add(label)
    }  
    reader.close()  
    return labels  
} 
```

最后一件事是创建一个方法，该方法将一个图像作为参数，并返回一个标签列表，这些标签具有分配给它们的概率。

```
fun recognize(bitmap: Bitmap): List<Recognition>{ 
```

因为我们的模型期望精确的输入形状(224x224 像素),所以我们需要重新缩放交付的位图以适应这些约束。

```
 val scaledBitmap =  Bitmap.createScaledBitmap(bitmap, MODEL_INPUT_SIZE, MODEL_INPUT_SIZE, false) 
```

接下来，我们需要创建适当大小的 byteBuffer，它将作为参数传递给模型。

```
 val byteBuffer = ByteBuffer  
        .allocateDirect(  
                    BATCH_SIZE *         // amount of images per single processing
                    MODEL_INPUT_SIZE *   // img height
                    MODEL_INPUT_SIZE *   // img width
                    BYTES_PER_CHANNEL *  // size of float = 4
                    PIXEL_SIZE           // r+g+b = 1+1+1
      )  
        .apply { order(ByteOrder.nativeOrder()) } // force device's native order (BIG_ENDIAN or LITTLE_ENDIAN) 
```

并将图像数据作为*浮点数*载入`byteByffer`。为了解码位图中每个像素的颜色(忽略 alpha ),我们需要屏蔽最低有效的 8 位及其倍数。

```
 val pixelValues = IntArray(MODEL_INPUT_SIZE * MODEL_INPUT_SIZE)  
    bitmap.getPixels(pixelValues, 0, bitmap.width, 0, 0, bitmap.width, bitmap.height)  

    var pixel = 0  
    for (i in 0 until MODEL_INPUT_SIZE) {  
        for (j in 0 until MODEL_INPUT_SIZE) {  
            val pixelValue = pixelValues[pixel++]  
            byteBuffer.putFloat((pixelValue shr 16 and 0xFF) / 255f)  
            byteBuffer.putFloat((pixelValue shr 8 and 0xFF) / 255f)  
            byteBuffer.putFloat((pixelValue and 0xFF) / 255f)  
        }  
    } 
```

最后，我们可以将 *byteBuffer* 传递给模型。解释器期望结果的第二个变元容器，它是*数组*到*浮点数组*(每个图像的*数组*，每个图像将包含概率的*浮点数组*)。

```
 val results = Array(BATCH_SIZE) { FloatArray(labels.size) }
    model.run(byteBuffer, results)
    return parseResults(results)
} 
```

最后一步是将概率与适当的类绑定。

```
private fun parseResults(result: Array<FloatArray>): List<Recognition> {  

    val recognitions = mutableListOf<Recognition>()  

    labels.forEachIndexed { index, label ->  
        val probability = result[0][index]  
        recognitions.add(Recognition(label, probability))  
    }  

  return recognitions.sortedByDescending { it.probability }  
} 
```

其中*识别*是我们简陋的结果数据类。

```
data class Recognition(  
    val name: String,  
    val probability: Float  
) {  
    override fun toString() =  
        "$name : ${probability*100}%"  
} 
```

###### 不要忘记，要让它工作得更好，你需要考虑很多事情，例如，如果你需要更高的速度、稍低的精度和更轻的重量，你需要处理摄像机的方向或使用训练后量化。

## 好戏上场了！

上面的代码是让 TFLite 为我们解决*图像分类*问题的极简版本。使用提供的模型，您可以成功地对这篇博客文章中的所有照片进行分类。📸
你可以在这里找到试玩[。](https://github.com/ares97/tflitedemo-mobilenetv2-imagenet-classification)

[![](img/183ece48766c352feba30a7f42b767dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UClo-acB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o8yx83vtewsrlioc9jdm.png)

最初发表于 brightinventions.pl

作者拉多斯瓦夫·索维斯基，软件工程师@光明发明