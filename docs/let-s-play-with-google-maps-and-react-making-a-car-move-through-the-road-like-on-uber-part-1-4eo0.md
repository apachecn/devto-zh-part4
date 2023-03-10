# 让我们玩玩谷歌地图并做出反应——让一辆车在路上行驶，就像在优步一样——第 1 部分

> 原文：<https://dev.to/zerquix18/let-s-play-with-google-maps-and-react-making-a-car-move-through-the-road-like-on-uber-part-1-4eo0>

让我们假设你是在优步工作的工程师(除非你是在优步工作的工程师)。你的任务是制作一辆汽车的动画，当它到达目的地时，在路上行驶。所以你要用 React(网上的优步用 React)。怎么做？

[https://codesandbox.io/embed/youthful-hermann-k3zfi](https://codesandbox.io/embed/youthful-hermann-k3zfi)

## 我们的工具

对于本指南，我将使用带有`react-google-maps`的 Create React App，它是 Google Maps 库的包装器，因此您知道该做什么:

`npm install react-google-maps`

## 基本地图

让我们从一张基本地图开始。谷歌地图库可以这样初始化:

```
import React from 'react';
import { withGoogleMap, withScriptjs, GoogleMap } from 'react-google-maps'

class Map extends React.Component {
  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
        >
      </GoogleMap>
    )
  }
}

const MapComponent = withScriptjs(withGoogleMap(Map))

export default () => (
  <MapComponent
  googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
  loadingElement={<div style={{ height: `100%` }} />}
  containerElement={<div style={{ height: `400px`, width: '500px' }} />}
  mapElement={<div style={{ height: `100%` }} />}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/unruffled-water-y81f1](https://codesandbox.io/embed/unruffled-water-y81f1)

我不打算深入讨论如何初始化`react-google-maps`的细节，而是将重点放在移动的逻辑上。如果你想学习如何设置，你可以[阅读他们的指南](https://tomchentw.github.io/react-google-maps/)。

我使用的主要道具是`defaultZoom`，它设置了谷歌地图的缩放比例。缩放比例越高，离地面越近，以及`defaultCenter`，它设置了地图的主要地理位置。

这应该在 Punta Cana 的环形交叉路口(靠近我住的地方)加载一个基本地图。

## 经纬度

在开始在地图上画之前，我们需要了解什么是经纬度。纬度和经度是表示地理位置的单位。纬度数字可以从 90 到-90，其中 0 是赤道，经度数字可以从 180 到-180，其中 0 是本初子午线。

基本上，用纬度你控制你的垂直位置，赤道在中心，用经度你控制你的水平位置，本初子午线在中心。

你真的不需要理解坐标是如何操作谷歌地图的(感谢谷歌！).谷歌为你提供了测量距离、计算物体朝向等工具，你只需把坐标传给他们。如果你有兴趣深入挖掘，你可以在[维基百科的文章](https://en.wikipedia.org/wiki/Geographic_coordinate_system)中读到更多。

## 标记

标记在地图上标识一个位置，通常使用我们都知道的位置图标:

[![](img/a852a5dca4342dc45ec90c75cf2379e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xHqVuhZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zxvirf8utpydrjja0kb8.png)

知道其纬度和经度，可以将标记放置在特定的位置。我们可以在环岛中间放置一个标记，如下所示:

```
import React from 'react';
import { withGoogleMap, withScriptjs, GoogleMap, Marker } from 'react-google-maps'

class Map extends React.Component {
  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
        >
          <Marker position={{
            lat: 18.559024,
            lng: -68.388886,
          }} />
      </GoogleMap>
    )
  }
}

const MapComponent = withScriptjs(withGoogleMap(Map))

export default () => (
  <MapComponent
  googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
  loadingElement={<div style={{ height: `100%` }} />}
  containerElement={<div style={{ height: `400px`, width: '500px' }} />}
  mapElement={<div style={{ height: `100%` }} />}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/zealous-rhodes-n9fjz](https://codesandbox.io/embed/zealous-rhodes-n9fjz)

# 折线

Polyline 组件基于`path`属性在地图上绘制一条线，该属性是一个坐标列表。我们可以用两个坐标画一条直线，这就是直线的端点。

```
import React from "react";
import {
  withGoogleMap,
  withScriptjs,
  GoogleMap,
  Polyline
} from "react-google-maps";

class Map extends React.Component {
  path = [
    { lat: 18.55996, lng: -68.388832 },
    { lat: 18.558028, lng: -68.388971 }
  ];
  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
      >
        <Polyline path={this.path} options={{ strokeColor: "#FF0000 " }} />
      </GoogleMap>
    );
  };
}

const MapComponent = withScriptjs(withGoogleMap(Map));

export default () => (
  <MapComponent
    googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
    loadingElement={<div style={{ height: `100%` }} />}
    containerElement={<div style={{ height: `400px`, width: "500px" }} />}
    mapElement={<div style={{ height: `100%` }} />}
  />
); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/nervous-goodall-0r4h4](https://codesandbox.io/embed/nervous-goodall-0r4h4)

我们刚刚画了一条穿过环形交叉路口的直线！不过，我不建议在开车时这么做。

但是曲线呢？我有个坏消息。**曲线不存在**。它们只是一堆直线在一起，给你一种有曲线的错觉。如果你放大足够多，它们总是会变得可见。让我们画一条曲线，加上足够的坐标。

```
import React from "react";
import {
  withGoogleMap,
  withScriptjs,
  GoogleMap,
  Polyline
} from "react-google-maps";

class Map extends React.Component {
  path = [
    { lat: 18.558908, lng: -68.389916 },
    { lat: 18.558853, lng: -68.389922 },
    { lat: 18.558375, lng: -68.389729 },
    { lat: 18.558032, lng: -68.389182 },
    { lat: 18.55805, lng: -68.388613 },
    { lat: 18.558256, lng: -68.388213 },
    { lat: 18.558744, lng: -68.387929 }
  ];
  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
      >
        <Polyline path={this.path} options={{ strokeColor: "#FF0000 " }} />
      </GoogleMap>
    );
  };
}

const MapComponent = withScriptjs(withGoogleMap(Map));

export default () => (
  <MapComponent
    googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
    loadingElement={<div style={{ height: `100%` }} />}
    containerElement={<div style={{ height: `400px`, width: "500px" }} />}
    mapElement={<div style={{ height: `100%` }} />}
  />
); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/floral-glitter-1er3s](https://codesandbox.io/embed/floral-glitter-1er3s)

这就是你画曲线的方式！通过添加更多的坐标，我们可以使直线不那么明显。

## 制作动画

这就是乐趣的开始。让我们在`path`的末尾添加一个标记。代表我们的车和它前进的路线。

```
import React from "react";
import {
  withGoogleMap,
  withScriptjs,
  GoogleMap,
  Polyline,
  Marker
} from "react-google-maps";

class Map extends React.Component {
  path = [
    { lat: 18.558908, lng: -68.389916 },
    { lat: 18.558853, lng: -68.389922 },
    { lat: 18.558375, lng: -68.389729 },
    { lat: 18.558032, lng: -68.389182 },
    { lat: 18.55805, lng: -68.388613 },
    { lat: 18.558256, lng: -68.388213 },
    { lat: 18.558744, lng: -68.387929 }
  ];
  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
      >
        <Polyline path={this.path} options={{ strokeColor: "#FF0000 " }} />
        <Marker position={this.path[this.path.length - 1]} />
      </GoogleMap>
    );
  };
}

const MapComponent = withScriptjs(withGoogleMap(Map));

export default () => (
  <MapComponent
    googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
    loadingElement={<div style={{ height: `100%` }} />}
    containerElement={<div style={{ height: `400px`, width: "500px" }} />}
    mapElement={<div style={{ height: `100%` }} />}
  />
); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/rough-night-k2j81](https://codesandbox.io/embed/rough-night-k2j81)

现在我们需要为我们的动画准备逻辑。我们将有一串直线，我们需要把车放在路径内。我们可以把逻辑分成 4 个步骤。

1.  计算第一个点和每个坐标之间的距离。**这里假设路径中的坐标是有序的**。
2.  设定一个速度，计算汽车随着时间的推移前进的距离。
3.  使用计算的距离，我们可以使用完整的路径，并获得汽车通过的路径。
4.  将汽车当前所在的最后一条直线制作成动画。

### 计算距离

谷歌为我们提供了计算两个坐标之间距离的工具。正在讨论的功能是`google.maps.geometry.spherical.computeDistanceBetween`

我们可以在安装组件之前执行这个步骤。它将计算每个坐标和路径中第一个元素之间的距离:

```
 componentWillMount = () => {
    this.path = this.path.map((coordinates, i, array) => {
      if (i === 0) {
        return { ...coordinates, distance: 0 } // it begins here! 
      }
      const { lat: lat1, lng: lng1 } = coordinates
      const latLong1 = new window.google.maps.LatLng(lat1, lng1)

      const { lat: lat2, lng: lng2 } = array[0]
      const latLong2 = new window.google.maps.LatLng(lat2, lng2)

      // in meters:
      const distance = window.google.maps.geometry.spherical.computeDistanceBetween(
        latLong1,
        latLong2
      )

      return { ...coordinates, distance }
    })

    console.log(this.path)
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 设定速度，每秒计算距离。

现在进入物理学。假设我们想让物体以每秒 5 米的速度运动。为此，我们需要一个初始时间和一个速度。让我们每秒钟记录一次这段距离。

```
 velocity = 5
  initialDate = new Date()

  getDistance = () => {
    // seconds between when the component loaded and now
    const differentInTime = (new Date() - this.initialDate) / 1000 // pass to seconds
    return differentInTime * this.velocity // d = v*t -- thanks Newton!
  }

  componentDidMount = () => {
    this.interval = window.setInterval(this.consoleDistance, 1000)
  }

  componentWillUnmount = () => {
    window.clearInterval(this.interval)
  }

  consoleDistance = () => {
    console.log(this.getDistance())
  } 
```

Enter fullscreen mode Exit fullscreen mode

这将记录一个每秒增加 5 的数字，就像我们汽车的速度一样。

让我们一起来看看当前的进展:

```
import React from 'react';
import { withGoogleMap, withScriptjs, GoogleMap, Polyline, Marker } from 'react-google-maps'

class Map extends React.Component {
  path = [
    { lat: 18.558908, lng: -68.389916 },
    { lat: 18.558853, lng: -68.389922 },
    { lat: 18.558375, lng: -68.389729 },
    { lat: 18.558032, lng: -68.389182 },
    { lat: 18.558050, lng: -68.388613 },
    { lat: 18.558256, lng: -68.388213 },
    { lat: 18.558744, lng: -68.387929 },
  ]

  velocity = 5
  initialDate = new Date()

  getDistance = () => {
    // seconds between when the component loaded and now
    const differentInTime = (new Date() - this.initialDate) / 1000 // pass to seconds
    return differentInTime * this.velocity // d = v*t -- thanks Newton!
  }

  componentDidMount = () => {
    this.interval = window.setInterval(this.consoleDistance, 1000)
  }

  componentWillUnmount = () => {
    window.clearInterval(this.interval)
  }

  consoleDistance = () => {
    console.log(this.getDistance())
  }

  componentWillMount = () => {
    this.path = this.path.map((coordinates, i, array) => {
      if (i === 0) {
        return { ...coordinates, distance: 0 } // it begins here! 
      }
      const { lat: lat1, lng: lng1 } = coordinates
      const latLong1 = new window.google.maps.LatLng(lat1, lng1)

      const { lat: lat2, lng: lng2 } = array[0]
      const latLong2 = new window.google.maps.LatLng(lat2, lng2)

      // in meters:
      const distance = window.google.maps.geometry.spherical.computeDistanceBetween(
        latLong1,
        latLong2
      )

      return { ...coordinates, distance }
    })

    console.log(this.path)
  }

  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
        >
          <Polyline path={this.path} options={{ strokeColor: "#FF0000 "}} />
          <Marker position={this.path[this.path.length - 1]} />
      </GoogleMap>
    )
  }
}

const MapComponent = withScriptjs(withGoogleMap(Map))

export default () => (
  <MapComponent
  googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
  loadingElement={<div style={{ height: `100%` }} />}
  containerElement={<div style={{ height: `400px`, width: '500px' }} />}
  mapElement={<div style={{ height: `100%` }} />}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/small-bird-5tibj](https://codesandbox.io/embed/small-bird-5tibj)

### 实时渲染踪迹

现在我们需要实时渲染汽车。我们有一堆直线，汽车会在其中两条直线内。因此，我们将把一些逻辑移到我们的状态中，并每秒更新一次。

首先，让我们将`progress`添加到我们的状态，并使我们的折线和标记跟随该状态。

```
 state = {
    progress: [],
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
 render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
        >
          { this.state.progress && (
            <>
              <Polyline path={this.state.progress} options={{ strokeColor: "#FF0000 "}} />
              <Marker position={this.state.progress[this.state.progress.length - 1]} />
            </>
          )}
      </GoogleMap>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将 or `consoleDistance`改为`moveObject`并提取汽车已经通过的路段:

```
 componentDidMount = () => {
    this.interval = window.setInterval(this.moveObject, 1000)
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
 moveObject = () => {
    const distance = this.getDistance()
    if (! distance) {
      return
    }
    const progress = this.path.filter(coordinates => coordinates.distance < distance)
    this.setState({ progress })
  } 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，我们有:

[https://codesandbox.io/embed/jolly-sky-3ez0m](https://codesandbox.io/embed/jolly-sky-3ez0m)

正如您注意到的，汽车“跳跃”了，因为我们添加了已经通过的行，但是汽车在`progress`的最后一个元素和`this.path`的剩余元素中。所以为了让动画更流畅，我们必须知道那两条线内的进度，然后找到那两条线内的坐标。谷歌在`google.maps.geometry.spherical.interpolate`为我们提供了这样一个功能。

完成我们的`moveObject`函数，我们有:

```
 moveObject = () => {
    const distance = this.getDistance()
    if (! distance) {
      return
    }

    let progress = this.path.filter(coordinates => coordinates.distance < distance)

    const nextLine = this.path.find(coordinates => coordinates.distance > distance)
    if (! nextLine) {
      this.setState({ progress })
      return // it's the end!
    }
    const lastLine = progress[progress.length - 1]

    const lastLineLatLng = new window.google.maps.LatLng(
      lastLine.lat,
      lastLine.lng
    )

    const nextLineLatLng = new window.google.maps.LatLng(
      nextLine.lat,
      nextLine.lng
    )

    // distance of this line 
    const totalDistance = nextLine.distance - lastLine.distance
    const percentage = (distance - lastLine.distance) / totalDistance

    const position = window.google.maps.geometry.spherical.interpolate(
      lastLineLatLng,
      nextLineLatLng,
      percentage
    )

    progress = progress.concat(position)
    this.setState({ progress })
  } 
```

Enter fullscreen mode Exit fullscreen mode

而且现在看起来很光滑！

[https://codesandbox.io/embed/unruffled-robinson-l2k4p](https://codesandbox.io/embed/unruffled-robinson-l2k4p)

我们的结果将是:

```
import React from 'react';
import { withGoogleMap, withScriptjs, GoogleMap, Polyline, Marker } from 'react-google-maps'

class Map extends React.Component {
  state = {
    progress: [],
  }

  path = [
    { lat: 18.558908, lng: -68.389916 },
    { lat: 18.558853, lng: -68.389922 },
    { lat: 18.558375, lng: -68.389729 },
    { lat: 18.558032, lng: -68.389182 },
    { lat: 18.558050, lng: -68.388613 },
    { lat: 18.558256, lng: -68.388213 },
    { lat: 18.558744, lng: -68.387929 },
  ]

  velocity = 5
  initialDate = new Date()

  getDistance = () => {
    // seconds between when the component loaded and now
    const differentInTime = (new Date() - this.initialDate) / 1000 // pass to seconds
    return differentInTime * this.velocity // d = v*t -- thanks Newton!
  }

  componentDidMount = () => {
    this.interval = window.setInterval(this.moveObject, 1000)
  }

  componentWillUnmount = () => {
    window.clearInterval(this.interval)
  }

  moveObject = () => {
    const distance = this.getDistance()
    if (! distance) {
      return
    }

    let progress = this.path.filter(coordinates => coordinates.distance < distance)

    const nextLine = this.path.find(coordinates => coordinates.distance > distance)
    if (! nextLine) {
      this.setState({ progress })
      return // it's the end!
    }
    const lastLine = progress[progress.length - 1]

    const lastLineLatLng = new window.google.maps.LatLng(
      lastLine.lat,
      lastLine.lng
    )

    const nextLineLatLng = new window.google.maps.LatLng(
      nextLine.lat,
      nextLine.lng
    )

    // distance of this line 
    const totalDistance = nextLine.distance - lastLine.distance
    const percentage = (distance - lastLine.distance) / totalDistance

    const position = window.google.maps.geometry.spherical.interpolate(
      lastLineLatLng,
      nextLineLatLng,
      percentage
    )

    progress = progress.concat(position)
    this.setState({ progress })
  }

  componentWillMount = () => {
    this.path = this.path.map((coordinates, i, array) => {
      if (i === 0) {
        return { ...coordinates, distance: 0 } // it begins here! 
      }
      const { lat: lat1, lng: lng1 } = coordinates
      const latLong1 = new window.google.maps.LatLng(lat1, lng1)

      const { lat: lat2, lng: lng2 } = array[0]
      const latLong2 = new window.google.maps.LatLng(lat2, lng2)

      // in meters:
      const distance = window.google.maps.geometry.spherical.computeDistanceBetween(
        latLong1,
        latLong2
      )

      return { ...coordinates, distance }
    })

    console.log(this.path)
  }

  render = () => {
    return (
      <GoogleMap
        defaultZoom={16}
        defaultCenter={{ lat: 18.559008, lng: -68.388881 }}
        >
          { this.state.progress && (
            <>
              <Polyline path={this.state.progress} options={{ strokeColor: "#FF0000 "}} />
              <Marker position={this.state.progress[this.state.progress.length - 1]} />
            </>
          )}
      </GoogleMap>
    )
  }
}

const MapComponent = withScriptjs(withGoogleMap(Map))

export default () => (
  <MapComponent
  googleMapURL="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places"
  loadingElement={<div style={{ height: `100%` }} />}
  containerElement={<div style={{ height: `400px`, width: '500px' }} />}
  mapElement={<div style={{ height: `100%` }} />}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要改变我们的路径和速度，让它看起来更好。这是根据路线和驾驶员而变化的。

使用更好的路径(使用[这个](https://www.doogal.co.uk/polylines.php)神奇的工具生成)，在 100km/h 时，我们有:

[https://codesandbox.io/embed/youthful-hermann-k3zfi](https://codesandbox.io/embed/youthful-hermann-k3zfi)

对于第 2 部分，我们将自定义汽车图标，并使其面向前进的方向！

如果有任何问题，请告诉我，:D