# 地图解决方案的简单技巧

> 原文：<https://dev.to/tolgahanuzun/simple-tricks-for-map-solution-2n92>

过去，我们使用谷歌地图服务来解决地图问题。随着项目的增长，谷歌地图开始写高额费用。我们在这里寻找一些解决方案，并将我们的需求分为两部分。

1.  经纬度数据的动态显示。(嵌入)
2.  使用街道、商店和家庭地址查找经度和纬度数据。(地理编码)

## 嵌入方案

这里有几个选择。(openstreetmap、谷歌地图、mapbox vs...)我来评价一下 google map 和 mapbox 的区别。

*   遗留方案: [**谷歌地图:SKU:动态地图**](https://developers.google.com/maps/billing/gmp-billing#dynamic-maps)

    对于超过 100，000 个请求:

    ```
    - 5.60 USD per 1000 
    ```

    例如:500.000 (100k 免费)请求: **2.240 美元**

*   新解决方案: [**为 Web 映射负载**](https://www.mapbox.com/pricing/#maploads)

    对于超过 50，000 个请求:

    ```
    - (50,001 to 100,000) 5.00 USD per 1000
    - (100,001 to 200,000) 4.00 USD per 1000
    - (20,001 to 1000,000) 3.00 USD per 1000 
    ```

    例如:500.000 (50k 免费)请求: **1.560 美元**

**利润** : 680 美元

### 发展过程

我在前面使用了反应传单库。(openstreetmap)传单库非常方便快捷。您可以将 api 定义为自定义。

强烈推荐`react-leaflet`。:)

```
import { Map as LeafletMap, TileLayer } from 'react-leaflet';

<LeafletMap>
    <TileLayer url={`https://api.mapbox.com/styles/v1/{#username}/{#project}/tiles/{z}/{x}/{y}?access_token={#token}`}/> </LeafletMap> 
```

## 地理编码解

目前这种物质没有替代品。替代方案效率很低。它会对用户体验产生负面影响。我推荐使用谷歌地图。