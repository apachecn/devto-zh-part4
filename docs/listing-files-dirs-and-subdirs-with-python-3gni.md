# 用 Python 列出文件、目录和子目录

> 原文：<https://dev.to/petercour/listing-files-dirs-and-subdirs-with-python-3gni>

如果你懂 Python 编程，你可以做很多事情。您可以使用 os 模块列出目录中的所有文件(包括子目录)。

如果你用的是 Linux 或者 Mac，你甚至可以列出你电脑上的每一个文件(从/)搜索。或者您可以从您的主目录浏览。

如果您想要当前目录和子目录中的所有文件，请使用“.”。

```
#!/usr/bin/python3
import os
path = '.'
for dirpath, dirname, filenames in os.walk(path):
    for filename in filenames:
        print(os.path.join(dirpath,filename)) 
```

这将列出目录中的所有文件，包括所有隐藏文件。为了提高可读性，您可以将其改为:

```
#!/usr/bin/python3
...
    file_and_path = os.path.join(dirpath,filename)
    print(file_and_path) 
```

### 文件交互

您可以只显示特定的文件，并与它们进行交互。例如，你可以显示电脑上的每一张图片

[![](img/20117dd0be4ce69004fe09b1ed26fa21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hpU8ctYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fk2f1icapuc3hp32nov2.png)

```
#!/usr/bin/python3
import os
import cv2
import time

path = '/home/linux/Desktop'
for dirpath, dirname, filenames in os.walk(path):
    for filename in filenames:
        file_and_path = os.path.join(dirpath,filename)
        if ".jpg" in file_and_path or ".jpeg" in file_and_path:
            print(file_and_path)

            try:
                # show image
                image = cv2.imread(file_and_path)
                cv2.imshow(file_and_path, image)
                cv2.waitKey(2000)
                cv2.destroyAllWindows()
                #time.sleep(1)
            except:
                print('Error loading image ' + file_and_path) 
```

相关链接:

*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)