# 我使用 ReactNative 中的钩子创建了无限分页平面列表的最简单实现。

> 原文：<https://dev.to/technoplato/i-created-the-simplest-implementation-of-an-infinite-paging-flatlist-using-hooks-in-reactnative-2i0l>

欢迎任何反馈，并希望有所帮助！

接下来，我将创建一个无限分页、实时更新、加载新帖子的按钮，它有一个带 Firebase 和钩子的列表。敬请期待！

让我知道你的想法！这是我在 Dev 上的第一篇帖子，抱歉有点短！只是想试试脚。

gist:[https://gist . github . com/techno Plato/e 394369 a6f 202 a 58 BF 010635 E6 EB 32 c 7](https://gist.github.com/technoplato/e394369a6f202a58bf010635e6eb32c7)

```
import React, { useState, useEffect } from 'react'
import {
  SafeAreaView,
  View,
  FlatList,
  StyleSheet,
  Text,
  Dimensions
} from 'react-native'

const useInfiniteScroll = load => {
  const [isFetching, setIsFetching] = useState(true)
  const [data, setData] = useState([])

  useEffect(() => {
    let didCancel = false
    if (!isFetching) return

    const loadAsync = async () => {
      const lastIndex = data.length - 1
      const lastItem = data.length ? data[lastIndex] : null

      const newData = await load({ lastIndex, lastItem })
      if (!didCancel) {
        setData(prevState => [...prevState, ...newData])
        setIsFetching(false)
      }
    }

    loadAsync()

    return () => {
      didCancel = true
    }
  }, [isFetching])

  return [data, isFetching, setIsFetching]
}

const INITIAL_LOAD = 30
const PAGE_SIZE = 20

export default () => {
  /**
   * Right now, I'm mandating that whatever this method is accepts as a
   * parameter an object containing the objects `lastIndex` and `lastObject`
   * respectively. I believe this should suffice for effective paging.
   *
   * @param lastIndex
   * @returns {Promise<R>}
   */
  const fetchMoreListItems = ({ lastIndex }) => {
    // Simulate fetch of next 20 items (30 if initial load)
    return new Promise(resolve => {
      setTimeout(() => {
        resolve([
          ...Array.from(
            Array(lastIndex === -1 ? INITIAL_LOAD : PAGE_SIZE).keys(),
            n => {
              n = n + lastIndex
              return {
                number: n.toString(),
                id: n.toString()
              }
            }
          )
        ])
      }, 2000)
    })
  }

  const [data, isFetching, setIsFetching] = useInfiniteScroll(
    fetchMoreListItems
  )

  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.blueBox}>
        <Text style={styles.bigWhiteBoldText}>
          {`${data.length} Items Loaded`}
        </Text>
      </View>
      <FlatList
        onEndReachedThreshold={7}
        onEndReached={() => {
          if (!isFetching) {
            setIsFetching(true)
          }
        }}
        data={data}
        keyExtractor={item => item.id}
        renderItem={({ item }) => {
          return <Item item={item} />
        }}
      />
      {isFetching && (
        <View style={styles.blueBox}>
          <Text style={styles.bigWhiteBoldText}>(Fetching More)</Text>
        </View>
      )}
    </SafeAreaView>
  )
}

class Item extends React.PureComponent {
  render() {
    return (
      <View style={styles.item}>
        <Text style={styles.title}>{this.props.item.number}</Text>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 24,
    backgroundColor: 'yellow'
  },
  item: {
    backgroundColor: '#f9c2ff',
    alignItems: 'center',
    justifyContent: 'center',
    height: Dimensions.get('window').height * 0.45,
    marginVertical: 8,
    marginHorizontal: 16
  },
  title: {
    fontSize: 48
  },
  blueBox: {
    height: 50,
    backgroundColor: 'blue',
    justifyContent: 'center',
    alignItems: 'center'
  },
  bigWhiteBoldText: {
    color: 'white',
    fontSize: 32,
    fontWeight: 'bold'
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是我另一个账户[https://dev.to/halfjew22](https://dev.to/halfjew22)的转贴，我用 Github 登录时误贴在了这个账户下。无论如何，这将是我将使用的主要帐户，所以我决定在这里转贴。

**编辑**:感谢 [@milansusnjar_](https://dev.to/milansusnjar_) 的推荐编辑。