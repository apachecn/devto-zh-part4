# 深度学习，使用 Python 进行对象检测

> 原文：<https://dev.to/petercour/deep-learning-object-detection-with-python-20mh>

Luminoth 是用于计算机视觉的深度学习工具包。它在底层使用 tensorflow，所以安装 tensorflow 或者 tensorflow-gpu(如果你有 gpu 的话)。

```
pip install luminoth 
```

那就来一杯吧

```
pip install tensorflow
pip install tensorflow-gpu 
```

你需要的东西之一是一台更快的计算机(可能还有一个 GPU)。在我的情况下，像这样运行脚本有时会冻结我的计算机。

下载一张图片，像这样

[![](img/0913c6b51eb187eab97630dc9829f83f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HMnyo0MS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2016/01/25/15/33/bicycling-1160860_960_720.jpg)

然后运行命令

```
lumi predict bicycling-1160860_960_720.jpg 
```

然后确认您要下载

```
Checkpoint not found. Check remote repository? [y/N]: y
Retrieving remote index... done.
2 new remote checkpoints added.
Checkpoint not present locally. Want to download it? [y/N]: y
Downloading checkpoint...  [####################################]  100%
Importing checkpoint... done.
Checkpoint imported successfully. 
```

json 输出的结果如下:

```
{"objects": [{"prob": 0.9997, "bbox": [294.0, 231.0, 468.0, 536.0], "label": "person"}, {"prob": 0.9971, "bbox": [494.0, 289.0, 578.0, 439.0], "label": "person"}, {"prob": 0.997, "bbox": [727.0, 303.0, 800.0, 465.0], "label": "person"}, {"prob": 0.9965, "bbox": [555.0, 315.0, 652.0, 560.0], "label": "person"}, {"prob": 0.9934, "bbox": [569.0, 425.0, 636.0, 600.0], "label": "bicycle"}, {"prob": 0.9932, "bbox": [326.0, 410.0, 426.0, 582.0], "label": "bicycle"}, {"prob": 0.9334, "bbox": [744.0, 380.0, 784.0, 482.0], "label": "bicycle"}, {"prob": 0.8723, "bbox": [506.0, 360.0, 565.0, 480.0], "label": "bicycle"}, {"prob": 0.8142, "bbox": [848.0, 319.0, 858.0, 342.0], "label": "person"}], "file": "bicycling-1160860_960_720.jpg"} 
```

您可以获得检查点列表

```
lumi checkpoint list 
```

你可以运行程序

```
#!/usr/bin/python from luminoth import Detector, read_image, vis_objects

image = read_image('traffic-image.png')
detector = Detector(checkpoint='traffic')
objects = detector.predict(image)

print(objects)

vis_objects(image, objects).save('traffic-out.png') 
```

然后像上面一样输出对象。

相关链接:

*   [发光模块](https://tryolabs.com/blog/2018/04/17/announcing-luminoth-0-1/)
*   [Python 教程](https://pythonprogramminglanguage.com/)