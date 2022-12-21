# 使用 React 绘制谷歌地图和图钉。语境

> 原文：<https://dev.to/rihdusr/google-map-and-pins-using-react-3j5c>

# 目标

我所设想的是一种渲染地图和绘图项目*(大头针，弹出窗口等)的反应方式..)*在地图上。

```
<Map>
  {// Marker component that renders a pin icon on the map }
  <Marker lat={lat1} lng={lng1} />
  <Marker lat={lat2} lng={lng2} /> </Map> 
```

[https://codepen.io/rihdus-1470886313/embed/qBWMrKg?height=600&default-tab=result&embed-version=2](https://codepen.io/rihdus-1470886313/embed/qBWMrKg?height=600&default-tab=result&embed-version=2)

## 第一步:创建封装谷歌地图 api 的基本组件

Google [Map](https://developers.google.com/maps/documentation/javascript/reference/map#Map.constructor) 类在给定的 DOM 元素中呈现新的地图，相应的 Map 实例提供 API 来与地图交互。其他类如[标记](https://developers.google.com/maps/documentation/javascript/reference/marker#Marker.constructor)、[信息窗口](https://developers.google.com/maps/documentation/javascript/reference/info-window#InfoWindow.constructor)和[叠加](https://developers.google.com/maps/documentation/javascript/reference/overlay-view#OverlayView)允许你在地图上绘制自定义 UI。

### 地图组件

这是一个在给定容器中呈现地图的基本地图组件。

```
class Map extends React.Component {
   /** Map instance */
   map = null;
   /** DOM container where the map canvas gets rendered. */
   mapContainer = React.createRef();
   componentDidMount() {
     /** Create new google map. */
      this.map = new google.maps.Map(this.mapContainer.current, {
        zoom: this.props.zoom,
        center: this.props.center
      })
   }
   render() {
      return <div ref={this.mapContainer}
        style={{ height: '100vh', width: '100vw'}}></div>
   }
}
ReactDOM.render(<Map />, document.getElementById('root')) 
```

### 标记组件

在给定的地图上给定的位置绘制标记。
要绘制一个标记，我们需要在 DOM 上呈现的`map`对象和对或`lat`、`lng`值来在地图上定位标记。

```
class Marker extends React.Component {
   componentWillUnmount() {
      this.marker.setMap(null); // Remove the marker from the map
   }
   render() { 
     const { map, lat, lng } = this.prop
     // Create new marker and render it on the map.
     this.marker =  new Marker({ 
       map: map, // the map instance
       position:  { lat, lng } // position of the marker on the map
     });
     return null; 
   }
} 
```

*详见 Google 提供的[添加带有标记](https://developers.google.com/maps/documentation/javascript/adding-a-google-map)的地图使用示例。*

## 第二步:将标记渲染为地图中的子组件

让我们再看看我们的目标..

```
ReactDOM.render(<>
    <Map>
      <Marker lat={lat1} lng={lng1} />
    </Map>
  </>, document.getElementById('root')) 
```

标记组件需要访问`map`实例，该实例是在前面定义的`Map`组件的`componentDidMount`函数中创建的。
`Map`组件可以通过[渲染道具](https://reactjs.org/docs/render-props.html)或者使用 [React.createContext](https://reactjs.org/docs/context.html#reactcreatecontext) 来传递地图实例。

### 使用 React 上下文的子标记。

React 上下文可用于将道具从父`Map`组件发送到子`Marker`组件。
让我们首先使用 [createContext](https://reactjs.org/docs/context.html#reactcreatecontext) api 为地图实例创建一个上下文。

> 地图将使用`<MapContext.Provider>`组件提供上下文的值。
> 见[上下文。提供者](https://reactjs.org/docs/context.html#contextprovider)组件 api。

```
// Map context with default value of the map set to `null`.
const MapContext = React.createContext({ map: null })
...
class Map extends React.Component {
  render() {
  /**
  * Provide `map` value in map context. This value can be consumed 
  * in any child component using `<MapContext.Consumer>` component.
  */
    return <MapContext.Provider value={{map: this.map}} >
      {this.props.children}
    </MapContext.Provider>
  }
} 
```

> 在标记组件中，我们可以使用`MapContext.Consumer`组件访问地图实例。
> 见[上下文。消费者](https://reactjs.org/docs/context.html#contextconsumer)组件 api

```
class Marker extends React.Component() {
  /**
  * In the render function, we can use `<MapContext.Consumer>` component 
  * to receive the `map` received from parent `Map` component.
  */  
  render() {
    return <MapContext.Consumer>{({map}) => {
      const { lat, lng } = this.props
      // Create new marker and render it on the map.
      this.marker = this.marker || new Marker({ 
        map: this.map, // the map instance
        position:  { lat, lng }
      });
      this.marker.setPosition({ lat, lng })
      return null;
    }}</MapContext.Consumer>
  }
} 
```

## 搞定！

```
// Resulting JSX for rendering Marker on Maps.
ReactDOM.render(<>
    <Map>
      <Marker lat={lat1} lng={lng1} />
    </Map>
  </>, document.getElementById('root')) 
```

概括一下，

*   我们使用 **React 中的**提供者**组件，创建了一个呈现*地图画布*的地图组件，并将相应的**地图**对象的实例提供给孩子。上下文** api。
*   我们使用相应的**消费者**组件来检索 Marker 组件中的**地图**实例，在*地图画布*中绘制图钉。

另一种方法是使用渲染属性技术为子标记组件提供地图对象实例。`Marker`组件中的`<MapContext.Consumer />`使用这个渲染道具技术来提供对`map`实例的访问。

#### 使用渲染道具实现子标记的示例。

```
class Map extends React.Component {
  render() {
    return this.props.children(this.map)
  }
}
// Render prop usage
ReactDOM.render(<>
    <Map>{map => {
      <Marker map={map} lat={lat1} lng={lng1} />
    }}</Map>
  </>, document.getElementById('root')) 
```

做出反应。上下文只是 React 中传递数据的一种方式，还有其他更适合其他用例的技术。在这次练习以及过去的其他练习中，我确实发现了 React 的一些好处。语境

*   由此产生的 JSX 更加清洁
*   父组件提供的数据可以被任何子组件以任何深度访问，而不需要[显式的属性传递](https://twitter.com/acdlite/status/955955121979969537?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed&ref_url=https%3A%2F%2Fblog.logrocket.com%2Freact-custom-hooks-and-the-death-of-render-props-a0ce5cba387f%2F)。

感谢你阅读我的第一篇技术文章。欢迎任何和所有的反馈。
干杯。