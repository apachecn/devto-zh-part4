# 如何使用 refs 在 FlatList 上设置回调？

> 原文：<https://dev.to/technoplato/how-can-i-use-refs-to-set-callbacks-on-a-flatlist-3fcj>

[StackOverflow Post](https://stackoverflow.com/questions/58106958/how-can-i-use-refs-to-set-callbacks-on-a-flatlist)

我正在尝试使用`refs`来强制设置我的 FlatList 的`onEndReached`属性。有办法做到这一点吗？

我修改了 PR 中的一个例子，增加了`setNativeProps`来间隔地将颜色从黑色切换到白色，但是不能让`onEndReached`或`onScroll`被调用。

有没有人能帮我理解一下我做错了什么？

```
export default class Testing extends React.Component {
  componentDidMount() {
    let tick = 0
    this.list.setNativeProps({
      onEndReached: info => {
        // NEVER CALLED 😢
        console.log('L231 on Scroll info ===', info)
      },

      onScroll: info => {
        // NEVER CALLED 😢
        console.log('L250 info ===', info)
      },

      // Background DOES flash red on load... 🤔 
      style: { backgroundColor: 'red' }
    })
    setInterval(() => {
      this.list.setNativeProps({
        onEndReached: info => {
          console.log('L231 on Scroll info ===', info)
        },

        // Background DOES toggle black and white... 🤔 
        style: { backgroundColor: tick++ & 2 ? 'white' : 'black' }
      })
    }, 1000)
  }

  render() {
    return (
      <View style={styles.container}>
        <FlatList
          ref={component => (this.list = component)}
          style={{ backgroundColor: 'black' }}
          data={[{ key: 'a' }, { key: 'b' }]}
          renderItem={({ item }) => <Text>{item.key}</Text>}
        />
      </View>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode