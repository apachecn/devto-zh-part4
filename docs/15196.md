# 宇宙魔方训练

> 原文：<https://dev.to/sandro/tesseract-training-49ji>

# 培训流程概述

## [https://github . com/tessera CT-ocr/tessera CT/wiki/exercise act-4.00 # introduction](https://github.com/tesseract-ocr/tesseract/wiki/TrainingTesseract-4.00#introduction)

概念上相同:

1 准备培训文本。
2 渲染文本到图像+盒子文件。(或者为现有图像数据创建手工制作的框文件。)
3 制作 unicharset 文件。(可以部分指定，即手动创建)。
4 从 unicode 和可选字典数据中创建一个 starter traineddata。
5 运行 tesseract 处理图像+ box 文件，制作训练数据集。
6 在训练数据集上运行训练。
7 合并数据文件。

## 主要区别在于:

*   这些框只需要在文本行级别。因此，从现有图像数据制作训练数据要容易得多。
*   的。tr 文件被替换为。lstmf 数据文件。
*   字体可以也应该自由混合，而不是各自为政。
*   聚类步骤(mftraining、cntraining、shapeclustering)由单个缓慢的 lstmtraining 步骤代替。

# 了解培训时使用的各种文件

与基础 Tesseract 一样，完整的 LSTM 模型和它需要的所有东西都收集在 traineddata 文件中。与基础 Tesseract 不同，starter traineddata 文件是在训练期间给出的，并且必须提前设置。它可以包含:

*   提供控制参数的配置文件。
*   定义字符集的 Unicharset。
*   Unicharcompress，也称为 recoder，它将 unicharset 进一步映射到神经网络识别器实际使用的代码。
*   标点符号模式 dawg，单词周围允许使用标点符号模式。
*   一言为定。系统词表语言模型。
*   数字狗，有允许的数字模式。

必须提供粗体元素。其他的是可选的，但是如果提供了任何 dawg，还必须提供标点 dawg。提供了一个新工具:combine_lang_model，用于从一个 unicharset 和可选单词列表生成一个 starter traineddata。

# 制作盒子文件

## [https://github . com/tesserac t-ocr/tesserac t/wiki/training tesserac t-4.00 #创建-训练-数据](https://github.com/tesseract-ocr/tesseract/wiki/TrainingTesseract-4.00#creating-training-data)

宇宙魔方 4 接受多种格式的盒子文件用于 LSTM 训练，尽管它们与宇宙魔方 3 使用的不同(详情)。

框文件中的每一行都匹配 tiff 图像中的一个“字符”(字形)。

其中可能是单个字形或整个文本行的边界框坐标(见示例)。

要标记文本行的结尾，必须在一系列行之后插入一个特殊的行。

请注意，在所有情况下，即使对于从右向左的语言，如阿拉伯语，该行的文本转录也应该从左向右排序。换句话说，无论使用何种语言，网络都将从左向右学习，而从右向左/双向处理发生在 Tesseract 内部的更高层。

使用 tesstrain.sh
运行 tesstrain.sh 的设置与基本 Tesseract 相同。使用- linedata_only 选项进行 LSTM 培训。请注意，拥有更多的训练文本和制作更多的页面是有益的，因为神经网络不具备泛化能力，需要在类似于它们将要运行的内容上进行训练。如果目标域非常有限，那么所有关于需要大量训练数据的可怕警告可能都不适用，但网络规范可能需要更改。

使用 tesstrain.sh 创建训练数据如下:

```
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata --output_dir ~/tesstutorial/engtrain 
```

上述命令使得 LSTM 训练数据等同于用于为英语训练 base Tesseract 的数据。对于制作一个基于 LSTM 的通用 OCR 引擎来说，这是非常不够的，但却是一个很好的教程演示。

现在尝试为“Impact”字体制作评估数据:

```
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata \
  --fontlist "Impact Condensed" --output_dir ~/tesstutorial/engeval 
```

稍后我们将使用这些数据来演示调优。