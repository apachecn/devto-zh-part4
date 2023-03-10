# 使用快速人工智能和 restnet 34 预训练模型的石头剪式分类器

> 原文：<https://dev.to/abhishekori/rock-paper-scissor-classifier-using-fast-ai-and-restnet-34-pretrained-model-gg7>

## 我们将执行【https://colab.research.google.com】T2 笔记本中的以下代码

## 第一步:导入快速 ai 视觉库

 `from fastai.vision import *` 

## 第二步:创建路径和目录

 `path = Path('data/rps')
path.mkdir(parents=True, exist_ok=True)` 

## 第三步:从你的 kaggle 个人资料中下载 kaggle json 文件，并上传到当前目录(/content)

## 第四步:将 kaggle json 复制到~/。卡格尔

更改文件权限
下载 drgfreeman/rockpaperscissors 数据集
 `!mkdir ~/.kaggle
!cp kaggle.json ~/.kaggle/
!chmod 600 ~/.kaggle/kaggle.json
!kaggle datasets download -d drgfreeman/rockpaperscissors` 

## 第五步:将数据集复制到 data/rps/ dir 并解压

 `!cp rockpaperscissors.zip data/rps/rockpaperscissors.zip
!unzip data/rps/rockpaperscissors.zip -d data/rps` 

## 第六步:创建 ImageDataBunch bunch 对象，该对象将

创建训练和验证数据集
get _ transforms 将获得默认转换，以增加数据集的多样性。它可能会裁剪图像，倾斜图像或稍微倾斜图像等等..
valid_pct 为 0.2，即 80%训练 20%测试
224 像素的图像大小
根据 imagenet 统计数据对像素进行标准化，以避免数值失控！
 `np.random.seed(42)
data = ImageDataBunch.from_folder(path, train=".", valid_pct=0.2,ds_tfms=get_transforms(), size=224, num_workers=4).normalize(imagenet_stats)` 

### 让我们看看我们的数据集是什么样子的

 `data.show_batch(rows=3, figsize=(7,8))` 

### 浏览数据串对象以查看类的数量、数据集的长度

 `data.classes, data.c, len(data.train_ds), len(data.valid_ds)` 

## 步骤 7:让我们通过传入 databunch 对象、restnet 34 预训练模型(fast AI lib 将为我们下载该模型)来创建一个 CNN 学习者，并告诉训练者我们对 error_rate 感兴趣，将其作为我的衡量标准

 `learn = cnn_learner(data, models.resnet34, metrics=error_rate)` 

## 第八步:让我们通过调用 fit_one_cycle 来尝试拟合模型。

这个方法接受整数，它将告诉学习者应该查看我们的数据多少次。在我们的例子中，我们将做 4 次。
每次它看到我们的数据时，它都会学习一些功能
如果我们增加循环次数，我们可能会试图使模型过度适应我们的数据集。我们希望它只是学习我们图像的一般特征
 `learn.fit_one_cycle(4)` 

### 让我们分析一下我们的指标

**历元**:经历所有类别数据的一个周期
**train_loss** :训练数据集中预测值与实际地面真实值之差
**valid_loss** :验证数据集中预测值与实际地面真实值之差
**error_rate** :错误的预测数。
您可以看到，训练损失大于验证损失，因为训练时它没有关于图像的信息。当它了解这些特性时，它会尝试根据验证集来测试自己的能力。因此，正如您可以预测的那样，当针对验证集运行时，它确实对图像有一些了解，并且与训练集相比性能更好

## 第 9 步:让我们保存我们的模型！

它在模型目录
 `learn.save('stage-1')` 中创建一个 pth 文件

## 第十步:现在我们将从我们训练好的模型中得到一些启示

classification interpretation . from _ learner 会给生成一些见解。
 `interp = ClassificationInterpretation.from_learner(learn)` 

让我们看看哪些图像我们的模型出错了，为什么？
你可以看到预测的、实际的损失以及预测类别的概率。

您还可以注意到底部的 2 幅图像，它正确预测了类别，但可信度较低
 `interp.plot_top_losses(4, figsize=(15,11))` 

让我们探索一下混淆矩阵，它告诉我们正确和错误预测的确切数量
在我们的示例中，我们有 2 个错误预测

它预测了岩石，但它是一张纸
它预测了纸张，但它是一块岩石
，你可以在上面的图顶损失中看到那些不正确的预测。有了这些数据，您可以进一步清理您的数据并调整您的模型以获得最佳结果
 `interp.plot_confusion_matrix(figsize=(12,12), dpi=60)` 

最困惑的打印出它无法预测的确切类
 `interp.most_confused()` 
让我们解冻我们的模型并做一点实验
解冻是什么意思？到目前为止，我们只是训练了现有预训练模型的最后几层。unfeeze 将帮助我们训练模型的所有层。
 `learn.unfreeze()` 
训练我们模型的所有层
正如你所看到的，这是一个坏主意。当训练所有层时，1 个 epoc 的训练丢失、有效丢失和错误率比只训练最后几层高得多。这是因为你要求模型重新学习图像的所有细微特征。最好保持原样，因为预训练的模型具有良好的边缘和小形状，这对于所有类型的图像都是常见的。
通过我们有限的专业知识和数据告诉 it 重新学习所有这些功能是不合适的。
 `learn.fit_one_cycle(1)` 

## 步骤 11:让我们加载我们保存的模型，并尝试找到学习率

学习率是随机梯度下降过程中步长的变化。
我们将观察损失如何变化
 `learn.load('stage-1');
learn.lr_find()
learn.recorder.plot()` 

从图中我们可以看到，在学习率为 3 的幂之后，它开始射击。所以好的经验法则是将模型重新训练到上限的 10 倍以下

## 第 12 步:现在让我们用 10 的 06 次方到 04 次方之间的受控学习率来重新训练我们的模型

 `learn.fit_one_cycle(2, max_lr=slice(1e-6,1e-4))` 

## 第 13 步:保存我们的最终模型

 `learn.save('rock-paper-scissors-1')` 

## 考试时间到了！

我拍了一张我的手的照片，去掉了背景，换成了绿色的屏幕。使用这个工具 [https://www.remove.bg](https://www.remove.bg)

 `img = open_image('/content/sci-removebg-preview.png')
img` 
T1】

这是一个迁移学习的小教程，采用现有的预训练模型，并根据您的用例对其进行重新训练。

快速 AI 库对程序员真的很好，我强烈推荐。我从这节课中学到了这个概念。更多[https://course.fast.ai/](https://course.fast.ai/)

如果你想试试你的，自己检查这个 jupyter 笔记本，并在[https://colab.research.google.com](https://colab.research.google.com)上运行。

确保您更改了运行时类型 GPU

是你尝试的时候了！

[https://gist . github . com/abhi skori/4e 4697 ba 7 CB 7 D2 b49 制造 674ce31cc00](https://gist.github.com/abhishekori/4e4697ba7cb7d2b49fece674ce31cc00)

让我知道你的想法< 3