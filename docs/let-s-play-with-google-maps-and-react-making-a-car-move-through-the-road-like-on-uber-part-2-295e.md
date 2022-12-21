# 让我们玩玩谷歌地图并做出反应——让一辆车在路上行驶，就像在优步一样——第 2 部分

> 原文：<https://dev.to/zerquix18/let-s-play-with-google-maps-and-react-making-a-car-move-through-the-road-like-on-uber-part-2-295e>

在我的[上一篇文章](https://dev.to/zerquix18/let-s-play-with-google-maps-and-react-making-a-car-move-through-the-road-like-on-uber-part-1-4eo0)中，我解释了如何通过谷歌地图移动一个标记。在这篇文章中，我想解释一下如何定制图标，让它面向它在路上行驶的方向。

这是我们离开的地方:

[https://codesandbox.io/embed/youthful-hermann-k3zfi](https://codesandbox.io/embed/youthful-hermann-k3zfi)

# 定制图标

为了定制图标，我们需要使用`icon`属性，它允许我们设置 url、大小和锚点。

```
 const icon = {
      url: 'https://images.vexels.com/media/users/3/154573/isolated/preview/bd08e000a449288c914d851cb9dae110-hatchback-car-top-view-silhouette-by-vexels.png',
      scaledSize: new window.google.maps.Size(20, 20),
      anchor: { x: 10, y: 10 }
    } 
```

Enter fullscreen mode Exit fullscreen mode

`url`道具是图像的链接。如果它有一个透明的背景，就更好了。
`scaledSize`道具将缩放图像。在本例中，为 20x20 像素(其中第一个参数是宽度，第二个参数是高度)。此外，可以使用`size`道具，但它不会按比例缩放图像。
`anchor`图像将具体确定图像将显示在该点的哪个位置。想象这是一个卡德西坐标系，其中中心是你的纬度/经度，你可以使用`x`和`y`键移动标记。现在车出现在路上了！

[https://codesandbox.io/embed/beautiful-bhabha-mvr2q](https://codesandbox.io/embed/beautiful-bhabha-mvr2q)

## 方位

现在我们需要让汽车面向它前进的方向。现在，它确实在移动，但看起来还是有点静止，对吗？

我提前道歉，但我要给你看一个黑客。Google Maps SDK for Javascript 没有可以改变标记旋转的属性。相反，你必须用 CSS 修改图像并旋转它。不太好笑，但也没那么糟糕！

### 度

在旋转之前，我们需要计算图像应该旋转多少度。我使用的的[图像已经旋转了 90 度，所以需要考虑到这一点。谷歌有一个功能可以帮我们完成这项工作。是`window.google.maps.geometry.spherical.computeHeading`。](https://images.vexels.com/media/users/3/154573/isolated/preview/bd08e000a449288c914d851cb9dae110-hatchback-car-top-view-silhouette-by-vexels.png)

提醒一下，我们正在渲染汽车，它在两条线之间，所以我们将使用这两条线来确定它的方向。

作为第二个提醒，我们将更新标记的 CSS，所以一旦设置了标记的位置，我们需要使用`componentDidUpdate`来更新标记。

```
 componentDidUpdate = () => {
    const distance = this.getDistance()
    if (! distance) {
      return
    }

    let progress = this.path.filter(coordinates => coordinates.distance < distance)

    const nextLine = this.path.find(coordinates => coordinates.distance > distance)

    let point1, point2

    if (nextLine) {
      point1 = progress[progress.length -1]
      point2 = nextLine
    } else {
      // it's the end, so use the latest 2
      point1 = progress[progress.length - 2]
      point2 = progress[progress.length - 1]
    }

    const point1LatLng = new window.google.maps.LatLng(point1.lat, point1.lng)
    const point2LatLng = new window.google.maps.LatLng(point2.lat, point2.lng)

    const angle = window.google.maps.geometry.spherical.computeHeading(point1LatLng, point2LatLng)
    console.log(angle)
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要找到标记的元素并旋转它。记住标记已经旋转了 90 度。

```
 componentDidUpdate = () => {
    const distance = this.getDistance()
    if (! distance) {
      return
    }

    let progress = this.path.filter(coordinates => coordinates.distance < distance)

    const nextLine = this.path.find(coordinates => coordinates.distance > distance)

    let point1, point2

    if (nextLine) {
      point1 = progress[progress.length -1]
      point2 = nextLine
    } else {
      // it's the end, so use the latest 2
      point1 = progress[progress.length - 2]
      point2 = progress[progress.length - 1]
    }

    const point1LatLng = new window.google.maps.LatLng(point1.lat, point1.lng)
    const point2LatLng = new window.google.maps.LatLng(point2.lat, point2.lng)

    const angle = window.google.maps.geometry.spherical.computeHeading(point1LatLng, point2LatLng)
    const actualAngle = angle - 90

    const markerUrl = 'https://images.vexels.com/media/users/3/154573/isolated/preview/bd08e000a449288c914d851cb9dae110-hatchback-car-top-view-silhouette-by-vexels.png'
    const marker = document.querySelector(`[src="${markerUrl}"]`)

    if (marker) { // when it hasn't loaded, it's null
      marker.style.transform = `rotate(${actualAngle}deg)`
    }

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/blissful-silence-ibo7c](https://codesandbox.io/embed/blissful-silence-ibo7c)

感谢您的阅读！