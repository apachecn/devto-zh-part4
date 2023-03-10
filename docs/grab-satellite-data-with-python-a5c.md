# 用 Python 抓取卫星数据

> 原文：<https://dev.to/petercour/grab-satellite-data-with-python-a5c>

可以用 Python 下载卫星数据。让我们试试那个。首先你需要[为 API 创建用户名和密码](https://scihub.copernicus.eu/)。这是来自塞尼特卫星的。

[![](img/42443f1cc9ba3a7d6f3d55038f7952c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgwHgbrY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F10lIlvHeCFpMhq%252Fgiphy-downsized-large.gif%26f%3D1)

然后安装高级模块。你可以打字

```
pip install senitel 
```

我让它在 Python 2.x 上工作，而不是在 Python 3 上。也许只是安装问题。

它需要一个搜索多边形作为输入(cities.geojson)。你可以从这里下载文件。

更改下面的用户名和密码，然后运行代码。

```
#!/usr/bin/python3
from sentinelsat import SentinelAPI, read_geojson, geojson_to_wkt
from datetime import date

api = SentinelAPI('username', 'password', 'https://scihub.copernicus.eu/dhus')

# search by polygon, time, and SciHub query keywords
footprint = geojson_to_wkt(read_geojson(r'cities.geojson'))
products = api.query(footprint,
                     date=('20151219', date(2015, 12, 29)),
                     platformname='Sentinel-2',
                     cloudcoverpercentage=(0, 30))

#Download all products 
api.download_all(products) 
```

它将下载一个包含大量数据的 zip 文件。提取 zip 文件并输入目录:

```
cd /S2A_MSIL1C_20151221T140112_N0201_R024_T21HVB_20151221T140113.SAFE/GRANULE/L1C_T21HVB_A002595_20151221T140113/IMG_DATA/ 
```

很好，但是所有的图像格式都是 jp2 格式。听说过吗？我也没有。你可以在线转换这些图像。似乎在这一步之后需要合并颜色通道。

相关链接:

*   [哥白尼](https://scihub.copernicus.eu/)
*   [Python 中的卫星图像访问](https://towardsdatascience.com/satellite-imagery-access-and-analysis-in-python-jupyter-notebooks-387971ece84b)
*   [学习 Python 编程](https://pythonbasics.org)
*   [Python 学习资源](https://pythonprogramminglanguage.com/)