# 将熊猫对象保存到 HDF5

> 原文：<https://dev.to/epassaro/gsoc-2019-june-ii-fjk>

## 什么是 HDF5？

HDF 代表**“分层数据格式”**，它被设计用来存储海量数据。最初是在*国家超级计算应用中心*开发的，现在它得到了一家非盈利公司*HDF 集团*的支持。

## 为什么要用 HDF5？

*   HDF5 的核心是二进制文件类型规范。

*   它能够**存储许多数据集**，用户定义的**元数据**，优化的 I/O，并能够查询其内容。

*   许多编程语言都有与 HDF 一起工作的工具。

*   HDF 允许数据集存在于嵌套的树结构中。实际上，HDF5 是一个文件中的文件系统。这个文件系统中的“文件夹”被称为*组*，有时也被称为*节点*或*键*(或者至少这些术语被不加区分地使用)。

## 工具箱

至少有三个 Python 包可以处理 HDF5 文件:`h5py`、`pytables`和`pandas`。

还有，有几个工具可以把它们可视化:**hdf viewer**(Java)**hdf compass**(Python)和 **ViTables** (Python)。它们可以在 Ubuntu 的仓库中找到，但是经常不能像预期的那样工作。

幸运的是， **ViTables** 在`conda-forge`套装频道有售，运行完美。

## 示例#1:转储数据帧

```
import pandas as pd

# Create an example DataFrame data = {'A': [1,2,3], 'B': [4,5,6]}
df = pd.DataFrame.from_records(data)

with pd.HDFStore('test.h5', mode='w') as f:
    f.put(key='/new_dataset', df) 
```

Enter fullscreen mode Exit fullscreen mode

## 例 2:写元数据

也许 HDF 最有趣的方面之一是存储元数据的能力*。

```
meta = { 'date': '21/06/2019', 'author': 'epassaro'}

with pd.HDFStore('test.h5', mode='a') as f:
    f.get_storer('/new_dataset').attrs.metadata = meta 
```

Enter fullscreen mode Exit fullscreen mode

*传统的 FITS 格式也可以做到这一点！

## 示例#3:将元数据写入 root ("/")

```
meta = { 'date': '21/06/2019', 'author': 'epassaro'}

with pd.HDFStore('test.h5', mode='a') as f:
    f.root._v_attrs['author'] = 'epassaro' 
```

Enter fullscreen mode Exit fullscreen mode