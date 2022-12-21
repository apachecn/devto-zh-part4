# 如何向基于位置的应用程序添加流量 API

> 原文：<https://dev.to/tomtomdevs/how-to-add-traffic-apis-to-your-location-based-app-57e0>

如果你是一名开发人员，你可能已经知道为什么 API 是有价值的——通过将 API 集成到你的项目中，你可以通过利用行业领导者已经提供的解决方案，并使用他们提供的预先存在的代码，极大地简化和加速你正在构建的任何东西。

流量 API 很好地展示了 API 对于开发者的价值(更不用说那些可以利用 API 驱动的流量应用的终端用户了)。流量 API 为您提供了构建模块，您可以利用实时流量数据打造更好的用户体验。您可以构建有趣的工具和应用程序，并深入了解交通状况。

TomTom 是导航、地图和交通产品领域的行业领导者，并继续创造有影响力的、易于使用的导航系统。多年来，该公司已将其业务重点从设备制造扩展到软件开发。现在，他们决定分享他们最擅长的:先进的交通信息。TomTom 生产创新的软件和服务，并提供全面的 API，允许您检索某个地区的详细交通信息。

在本文中，我们将深入探讨 TomTom 的 Traffic API 如何工作，以及我们如何利用 web 服务的 Traffic 套件(基于 TomTom Traffic 的实时流量数据)来检索某个区域的详细流量信息，您可以将这些信息用于您的 web 和移动应用程序。我们将在 ReactJS 中使用一些 JavaScript，以及示例中的 bash 脚本。

## TOMTOM 流量是如何工作的？

TomTom Traffic API 是一种基于云的实时交通监控信息服务，可检测高速公路和二级公路上的交通，它的工作原理是来自全球各地的数十亿次匿名交通测量(GPS 跟踪或探测数据)。这项服务已经存在了十年，该公司可以获得大量关于世界各地人们在哪里、何时以及如何开车的大数据。

为了提供这种高质量的服务，将来自传统来源(例如道路感应环路、交通监控摄像机)、现代来源(例如来自数百万匿名移动电话用户的数据)和历史数据的数据结合起来。这些信息随后由 TomTom 数据中心进行整理，以进一步过滤和改进，然后作为相关的实时交通信息分发给客户，每两分钟更新一次。交通数据可靠、准确、频繁、覆盖面广。提供了许多细节(如交通堵塞、道路封闭、延误)，甚至是较小的道路。

考虑到 TomTom 流量数据的质量，我们可以通过使用基于这些数据的流量 API 套件来创建有用、可靠和高质量的应用程序。

## TOMTOM 和流量 API 入门

Traffic API 是一套 web 服务，通过使用实时交通数据构建用户友好的应用程序，您可以释放自己的创造力。该服务是为开发人员设计的，可以通过 RESTful APIs 使用，市场覆盖面非常广。

在 TomTom 开发者门户网站上，你可以找到使用这个 API 所需要的一切。您要做的第一件事是在门户上创建一个帐户。在主页中，输入您的电子邮件地址，然后单击“获取免费 API 密钥”按钮。

注册或登录后，创建一个新的应用程序来接收您的 API 密钥。应用程序需要一个名称，您需要启用应用程序需要访问的 API。对于这个例子，我们将使用的产品是**交通流量 API** 和**交通事件 API** 。如果您正在跟进，请选择**交通流量 API** 和**交通事件 API** 产品，并点击**创建应用**。

交通事件 API 提供了可在地图上显示的某个区域周围的交通堵塞、事件、事故和延误的准确信息。交通流 API 提供了特定道路网络的观测速度(当前速度、畅通速度)和行驶时间的相关信息。

本文主要讨论如何使用 TomTom 流量和流量事件 API，但是开发人员也可以访问包含类似功能的 Web 和移动 SDK。您可以通过以下链接了解每个 SDK 的更多信息:

[Maps SDK for Web](https://developer.tomtom.com/)
[Maps SDK for Android](https://developer.tomtom.com/maps-sdk-android)
[Maps SDK for iOS](https://developer.tomtom.com/maps-sdk-ios)

## 示例用例 1

让我们找到用户的位置，然后根据他们的位置显示实时交通堵塞和事故数据。当用户使用移动设备时(即使他们只是连接到一个网站，而不是使用原生应用程序)，您可以询问他们的 GPS 位置。在本例中，我们将根据用户的 IP 地址来定位用户。

我们将使用交通事故 API 套件中的交通事故详细信息 API。此 API 的请求 URL 采用以下格式:

`https://api.tomtom.com/traffic/services/{versionNumber}/incidentDetails/{style}/{boundingBox}/{zoom}/{trafficModelID}/{format}?key={API_KEY}&language={string}&projection={string}&geometries={string}&expandCluster={boolean}&originalPosition={boolean}`

让我们更深入地了解一下每个参数的含义。

| [https://api.tomtom.com](https://api.tomtom.com) |  |
| --- | --- |
| 交通/服务 | //流量服务 |
| {版本号} | //服务的版本 |
| /事件详细信息 | //事件详细信息服务 |
| /{style} | //要呈现的平铺样式 |
| /{boundingBox} | //左下角纬度， |
|  | 左下角经度， |
|  | 右上角的纬度， |
|  | 右上经度 |
| /{缩放} | //缩放级别 |
| /{trafficModelID} | //流量模型 ID(默认值-1) |
| /{format} | // xml，json，jsonp |
| ？key={API_KEY} | // API 密钥 |
| &language={string}。 | //描述语言 |
| 投影={string} | //坐标系的类型 |
|  | (EPSG900913 或 EPSG4326) |
| &geometries={string} | //添加到的矢量几何的类型 |
|  | 事件 |
| &expandCluster={boolean} | //发送聚集点 |
| &originalPosition={boolean} | //返回的原始位置 |
|  | 事件和一个转移到 |
|  | 交通管道的起点 |

基于此请求的示例响应:

```
{
"tm": {
    "@id": "1537875895566",
    "poi": [
        {
            "id": "europe_CLUSTER_9_-1546746781",
            "p": {
                "x": 11.368265,
                "y": 48.002922
            },
            "ic": 13,
            "ty": 1,
            "cbl": {
                "x": 11.28824,
                "y": 47.969362
            },
            "ctr": {
                "x": 11.44829,
                "y": 48.03646
            },
            "cs": 13,
            "l": 27210
        },
        {
            "id": "europe_HD_DE_TTR131344535899136",
            "p": {
                "x": 11.237004,
                "y": 48.082583
            },
            "ic": 9,
            "ty": 1,
            "cs": 0,
            "d": "roadworks",
            "c": "new roadworks layout",
            "f": "Wörthsee (A96)",
            "t": "Germering-Süd (A96)",
            "l": 5840,
            "dl": 113,
            "r": "A96/E54"
        }
    ]
} 
```

现在我们对如何使用 API 有了更好的理解，让我们试着用它来构建一个简单的应用程序。我们可以使用 ReactJS 来使用 API 并操作数据，如下所示:

## 指标。射流研究…

```
import React, { Component } from "react";
import ReactDOM from "react-dom";
import publicIP from "public-ip";
import geoPoint from "geopoint";
import IncidentCategory from './components/incident_category';
import IncidentData from './components/incident_data';
import IncidentLegend from './components/incident_legend';
// Your API KEY can be hardcoded, but I recommend setting it as an env variable.
const API_KEY = '*****';
class App extends Component {
 constructor() {
   super();
   this.state = {
       error: null,
       isLoaded: false,
       trafficData: []
   };
 }
 componentDidMount() {
   publicIP.v4()
   .then(ip => fetch(`https://ipapi.co/${ip}/json`))
   .then(res => res.json())
   .then(result => this.getBoundingBox(result.latitude, result.longitude))
   .then(
       values =>
       fetch(`https://api.tomtom.com/traffic/services/4/incidentDetails/s3/${values[0]._degLat},${values[0]._degLon},${values[1]._degLat},${values[1]._degLon}/10/-1/json?key=${API_KEY}&projection=EPSG4326`)
   ) 
   .then(res => res.json())
   .then(
       payload => {
           this.setState({
               isLoaded: true,
               trafficData: payload["tm"]["poi"]
           });
       },
       error => {
           this.setState({
               isLoaded: true,
               error
           });
       }
   )
 }
 getBoundingBox(latitude, longitude) {
   const bboxValues = new geoPoint(latitude, longitude).boundingCoordinates(10, true);
   return bboxValues;
 }
 render() {
   const { error, isLoaded, trafficData } = this.state;
   let date = new Date();
   let currentDate = date.toDateString();
   let currentTime = date.getHours() + ':' + date.getMinutes() + ':' + date.getSeconds();
   if (error) {
    return 
        Error: {error.message};
   }
   else if (!isLoaded) {
       return  Loading...;
   } 
   else {
       return (
          Traffic Incidents
               {currentDate}
               Time: {currentTime} 
```

## 组件/事件 _ 数据。射流研究…

```
 import React from 'react';
const IncidentData = (props) => {
   const incidents = props.data;

   return (

           {incidents.map((el) => {  
               return (

                       {el["p"]["x"]}, {el["p"]["y"]}   // location
                       {el["l"]}                                    // length of delay
                       {el["d"]}                                   // description
                       {el["ic"]}                                  // type
                       {el["ty"]}                                 // severity

               )
           })}

   );
};
export default IncidentData; 
```

更多的代码可以在这里查看。

关于向你的应用添加流量 API 的第二个用例，请在 TomTom 的开发者博客上阅读本文的其余部分。