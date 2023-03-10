# 带有@react-native-mapbox-gl/maps 的超级集群

> 原文：<https://dev.to/itzsaga/supercluster-with-react-native-mapbox-gl-maps-kh2>

在我最近在[飞艇](https://teamairship.com)工作的一个项目中，我不得不停止使用`@react-native-mapbox-gl/maps`提供的内置集群功能，转而使用[超级集群](https://github.com/mapbox/supercluster)。原因是我们需要访问构成集群的点。我们有一些项目从未脱离它们的集群，因为它们有相同的经度&纬度组合。以及希望在列表视图中显示这些位置的上滑视图。让我走上这条路的是一个关于废弃的`react-native-mapbox-gl`库的问题，它与新库共享了很多功能。你可以在这里查看问题。老实说，我很惊讶这个功能在库中不可用，因为它*在 Mapbox JS SDK 中得到*支持，正如这里的[和`getClusterLeaves()`函数所记录的那样。我注意到人们在问如何做到这一点，所以当我把它固定下来时，我知道该怎么做了。](https://docs.mapbox.com/mapbox-gl-js/api/#geojsonsource)

## 无超星系团

我在这里设置了一个代码示例[来展示利用内置集群功能设计的起始状态。这基本上是我开始使用核心地图框功能的地方。让我们浏览一下这段代码。](https://snack.expo.io/@sethalexander/before-supercluster) 

```
render() {
  return (
    <View style={{ flex: 1 }}>
      <MapboxGL.MapView
        ref={c => (this._map = c)}
        zoomEnabled
        style={[{ flex: 1 }]}
      >
        {this.renderPoints()}
      </MapboxGL.MapView>
    </View>
  );
} 
```

这是我们用来设置地图的 MapboxGL 容器。然后我们需要在地图上渲染我们所有的点，我们在一个助手函数`renderPoints()`中处理它。

```
renderPoints = () => {
  const { points } = this.state;

  return (
    <MapboxGL.ShapeSource
      id="symbolLocationSource"
      hitbox={{ width: 18, height: 18 }}
      onPress={this.onMarkerSelected}
      shape={points}
      cluster
    >
      <MapboxGL.SymbolLayer
        minZoomLevel={6}
        id="pointCount"
        style={mapStyles.clusterCount}
      />

      <MapboxGL.CircleLayer
        id="clusteredPoints"
        minZoomLevel={6}
        belowLayerID="pointCount"
        filter={["has", "point_count"]}
        style={mapStyles.clusteredPoints}
      />

      <MapboxGL.SymbolLayer
        id="symbolLocationSymbols"
        minZoomLevel={6}
        filter={["!has", "point_count"]}
        style={mapStyles.icon}
      />
    </MapboxGL.ShapeSource>
  );
}; 
```

因此，这里我们从 state 获取组列表，并将它们传递给`MapboxGL.ShapeSource`并打开`cluster`功能。我们有三层，我用 ID 值来表示。

–`pointCount`是组成集群的项目数量的实际数值。

–`clusteredPoints`是我们看到的位于`pointCount`层下面的星团圈。

–`symbolLocationSymbols`是地图上未被聚类的单个位置的地图标记。

当我们单击一个标记时，无论它是地图上的一个聚类还是单个点，我们都称之为`onMarkerSelected`，它目前只为非聚类实现功能，例如:

```
onMarkerSelected = event => {
  const point = event.nativeEvent.payload;
  const { name, cluster } = point.properties;
  const coordinates = point.geometry.coordinates;

  if (cluster) {
    console.log(cluster);
  } else {
    this.setState(
      {
        selectedPointName: name,
        selectedPointLat: coordinates[1],
        selectedPointLng: coordinates[0],
      },
      () => {
        this.map.flyTo(point.geometry.coordinates, 500);
      }
    );
  }
}; 
```

`if/else`语句只是记录集群(如果有的话),否则它会将状态设置为所选的点，并将地图集中在该点上。将点信息添加到状态的想法是用该信息做一些事情。

我们决定是否要根据传递给`CircleLayer`和`SymbolLayer`的`filter`标准来绘制圆或标记。

```
const mapStyles = MapboxGL.StyleSheet.create({
  icon: {
    iconAllowOverlap: true,
    iconSize: 0.35
  },
  clusteredPoints: {
    circleColor: "#004466",
    circleRadius: [
      "interpolate",
      ["exponential", 1.5],
      ["get", "point_count"],
      15,
      15,
      20,
      30
    ],
    circleOpacity: 0.84
  },
  clusterCount: {
    textField: "{point_count}",
    textSize: 12,
    textColor: "#ffffff"
  }
}); 
```

这最后一部分提供了一些样式，实际上在我们交换集群功能时不会改变。

## 实现超级集群

因此，切换到超级集群来取代原始数据的内置集群的想法。Supercluster 不会对这些数据的实际呈现做任何事情。我们需要使用 Supercluster 初始化一个集群，然后根据地图的边界和缩放级别更新该集群。我将详细介绍这一转换的要点。

首先，您需要我决定存储在 state 中的集群。我认为这是最有意义的，对我来说效果很好。

```
const collection = MapboxGL.geoUtils.makeFeatureCollection(groupFeatures);
const cluster = new Supercluster({ radius: 40, maxZoom: 16 });
cluster.load(collection.features);

this.setState({
  point: collection,
  loading: false,
  selectedPoints: [],
  superCluster: cluster,
  userFound: false
}); 
```

所以现在我有了作为 T1 的 T0 和作为 T2 的超星系团。然而，我们还不能将这个`superCluster`传入我们的`MapboxGL.ShapeSource`。这个集群是不可变的，本质上我们现在将使用它来创建我们将传递到`ShapeSource`的形状对象。接下来让我们像这样更新我们的`MapView`:

```
<MapboxGL.MapView
  ref={c => (this._map = c)}
  onRegionDidChange={this.updateClusters}
  zoomEnabled
  style={[{ flex: 1 }]}
>
  {this.renderPoints()}
</MapboxGL.MapView> 
```

注意添加的`onRegionDidChange`，它采用了一个回调函数。我发现这个道具最适合我，然而，随着`react-native-mapbox-gl/maps`库的不断发展，可能会有更好的解决方案。这在地图被移动后调用`updateClusters`。现在让我们来看看`updateClusters`函数:

```
updateClusters = async () => {
  const sc = this.state.superCluster;
  if (sc) {
    const bounds = await this._map.getVisibleBounds();
    const westLng = bounds[1][0];
    const southLat = bounds[1][1];
    const eastLng = bounds[0][0];
    const northLat = bounds[0][1];
    const zoom = Math.round(await this._map.getZoom());
    this.setState({
      superClusterClusters: sc.getClusters(
        [westLng, southLat, eastLng, northLat],
        zoom
      )
    });
  }
}; 
```

因此，我将在我的`componentDidMount()`中创建的集群设置为一个局部变量。以防我在做其他事情之前检查它是否存在。接下来，我从设置在`MapView`上的`_map` ref 获取可见边界。我将这四个界限提取到它们自己的变量中，主要是为了容易知道它们是什么。然后我得到缩放比例，并将其四舍五入为整数(我发现十进制缩放会产生超星系团问题)。最后，我获取所有信息，为这些边界和缩放级别创建适当的集群，并将它们保存在 state to `superClusterClusters`中。

这个`superClusterClusters`就是输入到`renderPoints`中的`ShapeSource`的内容，就像这样:

```
renderPoints = () => {
  const { superClusterClusters } = this.state;

  return (
    <MapboxGL.ShapeSource
      id="symbolLocationSource"
      hitbox={{ width: 18, height: 18 }}
      onPress={this.onMarkerSelected}
      shape={{ type: "FeatureCollection", features: superClusterClusters }}
    >
      <MapboxGL.SymbolLayer
        id="pointCount"
        minZoomLevel={6}
        style={mapStyles.clusterCount}
      />

      <MapboxGL.CircleLayer
        id="clusteredPoints"
        minZoomLevel={6}
        belowLayerID="pointCount"
        filter={[">", "point_count", 1]}
        style={mapStyles.clusteredPoints}
      />

      <MapboxGL.SymbolLayer
        id="symbolLocationSymbols"
        minZoomLevel={6}
        filter={["!", ["has", "point_count"]]}
        style={mapStyles.icon}
      />
    </MapboxGL.ShapeSource>
  );
}; 
```

注意，`shape`属性需要创建一个对象，我没有直接从状态中传递`superClusterClusters`。还要注意的是`cluster`道具不再包含在`ShapeSource`中。这是我忘记的事情，给我带来了很多悲伤。内置聚类与我的聚类冲突。

最后，我们添加了一个功能，当我们在手机上触摸某个点时，可以从聚类中获取该点的信息，就像这样:

```
onMarkerSelected = event => {
  const point = event.nativeEvent.payload;
  const { name, cluster } = point.properties;
  const coordinates = point.geometry.coordinates;

  if (cluster) {
    const sc = this.state.superCluster;
    if (sc) {
      const points = sc
        .getLeaves(point.properties.cluster_id, Infinity)
        .map(leaf => ({
          selectedPointName: leaf.properties.name,
          selectedPointLat: leaf.geometry.coordinates[1],
          selectedPointLng: leaf.geometry.coordinates[0],
        }));
      this.setState({ selectedPoints: points });
      console.log(points);
    } else {
      this.setState(
        {
          selectedPoints: [
            {
              selectedPointName: name,
              selectedPointLat: coordinates[1],
              selectedPointLng: coordinates[0],
            },
          ],
        },
        () => {
          this.camera.flyTo(point.geometry.coordinates, 500);
        }
      );
    }
  }
}; 
```

通过使用超级集群`getLeaves()`,我们将它们映射到一个新的数组，并将我们的`selectedPoints`状态设置为该数组。我们现在可以在状态中使用这个新数据在 UI 中呈现一些东西。

### 最后的想法

虽然将 Supercluster 添加到`react-native-mapbox-gl/maps`地图以访问集群的底层点似乎需要很多步骤，但我分享的大部分代码都可以按原样重用，以简化过渡。

作为参考，我在这个例子中使用的确切版本是:

`react-native-mapbox-gl/maps`:7 . 0 . 1

`supercluster`:6 . 0 . 2

一个最终的代码样本在这里[可用](https://snack.expo.io/@sethalexander/after-supercluster)。

**注意:**代码示例不起作用。不过，如果你正在阅读这篇文章，我假设你已经实现了 Mapbox。我还假设您已经用访问令牌初始化了库。

我希望这能让人们在前进的道路上省去一些麻烦，并且您可以通过访问构成集群的底层数据点来构建令人惊奇的东西。

带有@ react-native-map box-GL/maps 的帖子[最早出现在](https://sethaalexander.com/supercluster-with-react-native-mapbox-gl-maps/) [Seth Alexander](https://sethaalexander.com) 上。