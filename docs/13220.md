# 在谷歌地图上显示地理围栏和多边形

> 原文：<https://dev.to/danvoyce/displaying-geofences-and-polygons-on-google-maps-3lio>

嗯，过去几周我一直在重新设计[local Engage](https://locally.io/products/engage)，它将在未来几天推出！这个功能很有挑战性，需要我使用很久没用的东西！我必须做的事情之一是绘制一张地图，显示某个客户的信标和地理围栏。

在我们的例子中，范围非常简单，因为地理围栏总是多边形或圆形，而信标由标记表示。

因为这个仪表板的提议是让一切都动态化，这意味着所有的数据都必须通过 AJAX 请求获得。(我们决定此时不迁移到 React，因为我们继承的遗留系统已经使用 CakePHP 的模板语言构建)地图信息也需要通过 AJAX 提取，然后显示在客户端。

在后端构建了所有的功能后，我的决定和使用 Maps JavaScript API 没什么不同，这是一种非常简单的方法。

## 我们来做点编码吧！

**服务器响应**
假设您有一个类似信标的 JSON 响应:

```
{  
   "beacons":[  
      {  
         "lat":-37.829996,
         "lng":144.967707,
         "address":"65 Coventry Street, Southbank, Victoria 3006, Australia"
      },
      {  
         "lat":-37.829857,
         "lng":144.967851,
         "address":"1021 / 65 Coventry street, Southbank, Victoria 3006, Australia"
      }
   ]
} 
```

还有一个类似 Geofences 的 JSON 响应:

```
{ 
  "geofences": [{
                "type": "POLYGON",
                "object": "POLYGON((-76.94644698125 38.74057911875,-76.33609541875 38.74057911875,-76.33609541875 39.35093068125,-76.94644698125 38.74057911875))",
                "description": "A nice Polygon!",
                "lat": 38.94403,
                "lng": -76.539546,
                "coordinates": [
                    {
                        "lat": 38.74057911875,
                        "lng": -76.94644698125
                    },
                    {
                        "lat": 38.74057911875,
                        "lng": -76.33609541875
                    },
                    {
                        "lat": 39.35093068125,
                        "lng": -76.33609541875
                    },
                    {
                        "lat": 38.74057911875,
                        "lng": -76.94644698125
                    }
                ]
            },
             {
                "type": "RADIUS",
                "object": "POINT(-83.4885590000 42.5722669000)",
                "description": "An amazing circle!",
                "lat": 42.5722669,
                "lng": -83.488559,
                "radius": 0.010840289
            }]
} 
```

## 创建谷歌地图

让我们创建一个非常基本的地图来显示我们的对象:

```
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no">
    <meta charset="utf-8">
    Beacons and Geofences Map
  </head>
  <body>
    <div id="map"></div>
    <script>
       function initMap() {
        map = new google.maps.Map(document.getElementById('map'), {
          center: {lat: -34.397, lng: 150.644},
          zoom: 5,
          mapTypeId: google.maps.MapTypeId.ROADMAP
        });
      }
    </script>
    <script async defer
    src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap">
    </script>
  </body>
</html> 
```

## 显示信标和地理围栏

现在，显示信标，循环响应对象和:

```
var markers = [];
//Creating the icon
var icon = new google.maps.MarkerImage('/path/to/icon/image.png', new google.maps.Size(32, 32));

//Let's display the Beacon address when clicked on the arrow
var markerContent = '<p>' + beacons.address + '</p>';

var infoWindowMap = new google.maps.InfoWindow({
    content: markerContent
});

//Now the marker, our Beacon
var marker = new google.maps.Marker({
    position: {
        lat: beacon.lat,
        lng: beacon.lng
    },
    draggable: false,
    icon: icon
});
// When we click on the marker, show the window with the address
marker.addListener('click', function() {
    infoWindowMap.open(map, marker);
});

//Closing the info Window with when the mouse is out of the beacon
google.maps.event.addListener(marker, 'mouseout', function() {
    infoWindowMap.close();
});

//Setting the beacon in the map
marker.setMap(map);
//Let's store these beacons in an array, so we can cluster them! 
markers.push(marker);
//Clustering the beacons
var options = {imagePath: '/path/to/cluster/image'};
var cluster = new MarkerClusterer(map, markers, options);
To display the geofences, it is as simple as it can be. Looping the geofences object, we need to know what type of object we’ll create, as the API has different options and functions for polygons and circles:

//Configuration for objects type: POLYGON
if (geofence.type == 'POLYGON') {
    var paths = geofence.coordinates;
    var polygon = new google.maps.Polygon({
        paths: paths,
        strokeColor: '#FF0000',
        strokeOpacity: 0.8,
        stokeWeight: 2,
        fillColor: '#FF0000',
        fillOpacity: 0.35
    });

    //Set the polygon in the map
    polygon.setMap(map);

    //Plot the bounds
    var bounds = new google.maps.LatLngBounds();
    for (var i = 0; i < paths.length; i++) {
        bounds.extend(paths[i]);
    }
    map.fitBounds(bounds);
} else {
    //It is a circle!
    var circle = new google.maps.Circle({
        strokeColor: '#FF0000',
        strokeOpacity: 0.8,
        strokeWeight: 2,
        fillColor: '#FF0000',
        fillOpacity: 0.35,
        map: map,
        center: {
            lat: geofence.lat,
            lng: geofence.lng
        },
        radius: geofence.radius * 1609.344,
    });
    //Set the circle in the map
    circle.setMap(map);
    //Plot the bounds
    var bounds = new google.maps.LatLngBounds();
    bounds.union(mapType.getBounds());
    map.fitBounds(bounds);
} 
```

## 最终结果，地图完成！

[![](img/0dff329bee75c73e0fbbd9681e9ca461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sF54_H8V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kkdp8e8ymxvug4ouaxdf.png)

地图应该看起来像那样酷！

结果是一个结合了地理围栏和信标的非常好的地图，这些信息对很多事情都很方便！Google Maps JavaScript API 让这项任务变得简单而可信，只需几行代码，就能显示你的地理围栏在哪里，你周围有什么信标！

| ![](img/633019f04780d005bf5578ed2c4d44d3.png) | **Lorena Santana -平台开发商**在过去的+8 年中，使用不同的技术和方法进行系统分析师/Web 开发。有 ERP，CMS 和框架的经验，如 CakePHP，Bootstrap 和 Wordpress。全栈开发人员，自学，擅长数据库建模和维护，也擅长需求收集。有敏捷方法的经验，能在压力下很好地工作。实习生经验。 |