# 我正在为 React Native 创建一个深度挖掘，以创建带有您不可避免会遇到的功能的列表。

> 原文：<https://dev.to/technoplato/i-m-creating-a-deep-dive-for-react-native-into-creating-lists-with-functionality-that-you-will-inevitably-encounter-2nd1>

## 强制:[最终结果的预览。](https://youtu.be/0VJRFZmr7Mk?t=161)

## TL；博士:[视频链接在哪里](https://www.youtube.com/watch?v=vNq1P06KkoU)

## 我不喜欢视频:[给我看代码就行](https://github.com/technoplato/TutorialListAndHooks)

## 我不喜欢视频或阅读代码:[你的媒体报道呢](https://medium.com/@michaellustig)

我作弊了，没有在 Medium 上写任何东西，但如果你订阅并向我抱怨那是你想看到的东西，好吧，供需！T3】

最近在 React(React 本地使用)中，团队发布了一套非常酷的功能，叫做 Hooks。钩子允许我们创建共享的功能，可以抽象出很多复杂性。在本教程系列中，我将从您可以创建的最基本的列表(由示例文档[提供，此处为](https://facebook.github.io/react-native/docs/flatlist.html))开始，并将其转换为一些非常酷的东西，包括:

*   带分页的无限滚动
*   从远程数据源与您的列表状态同步的实时更新(我使用并向您展示如何使用 Firestore)
*   乐观列表项更新
*   实时更新每个列表项中的计数器，该计数器将显示各种“计时器”,记录列表项创建的时间
*   发布新帖子(有点像 Twitter 会问你是否想在新帖子到达时“加载更多的帖子”)
*   以编程方式滚动到列表顶部
*   使用伪造者轻松创建测试数据

…可能更多！

我不会删除错误，因为我真的认为这剥夺了学习的机会。很多教程内容做得如此完美，千篇一律，我觉得它给观众留下了“我是这样醒来的”的印象，而事实上很少是这样的。

希望你喜欢，这里有[播放列表 URL](https://www.youtube.com/playlist?list=PL3z1TiLmRFczvrGE0N7O1aG1x0a_u_Ctb) 和一些其他链接，如果你喜欢这个视频。请务必让我知道，如果有任何你想了解的相关列表，我会试着把它制作成一个视频。

[YouTube](https://www.youtube.com/c/MichaelLustig)|[dev . to](https://dev.to/technoplato)|[Twitter](https://twitter.com/technoplato/)|[Github](https://github.com/technoplato)|[Medium](https://medium.com/@michaellustig)|[Reddit](https://www.reddit.com/user/halfjew22)

### 代码(因为它仍然很短并且是独立的)

```
import React, {useState, useRef, useMemo} from 'react';
import {
  SafeAreaView,
  TouchableOpacity,
  FlatList,
  TextInput,
  Button,
  Alert,
  View,
  StyleSheet,
  Text,
} from 'react-native';

const DATA = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
    selected: false,
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
    selected: false,
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
    selected: false,
  },
];

function Item({item: {id, title, selected}, onSelect}) {
  console.log('L29 "item is rendering" ===', id);

  return (
    <TouchableOpacity
      onPress={() => onSelect(id)}
      style={[
        styles.item,
        {backgroundColor: selected ? '#6e3b6e' : '#f9c2ff'},
      ]}>
      <View>
        <Text style={styles.title}>{title}</Text> 
        <Button title="Like Post" onPress={() => {}} /> 
        <Button title="Dislike Post" onPress={() => {}} />
      </View>
    </TouchableOpacity>
  );
}

function ItemPureFunctional({item: {id, title, selected}, onSelect}) {
  return useMemo(() => {
    console.log('L44 "item is rendering" ===', id);
    return (
      <TouchableOpacity
        onPress={() => onSelect(id)}
        style={[
          styles.item,
          {backgroundColor: selected ? '#6e3b6e' : '#f9c2ff'},
        ]}>
        <View>
          <Text style={styles.title}>{title}</Text> 
          <Button title="Like Post" onPress={() => {}} /> 
          <Button title="Dislike Post" onPress={() => {}} />
        </View>
      </TouchableOpacity>
    );
  }, [id, onSelect, selected, title]);
}

class ItemPureComponent extends React.PureComponent {
  render() {
    return (
      <TouchableOpacity
        onPress={() => this.props.onSelect(this.props.id)}
        style={[
          styles.item,
          {backgroundColor: this.props.selected ? '#6e3b6e' : '#f9c2ff'},
        ]}>
        <Text style={styles.title}>{this.props.title}</Text>
      </TouchableOpacity>
    );
  }
}

export default function App() {
  const [data, setData] = useState(DATA);
  const [text, setText] = useState('');

  const onSelect = useRef(id => {
    setData(oldData => {
      return [
        ...oldData.map(item => {
          if (id === item.id) {
            return {
              ...item,
              selected: !item.selected,
            };
          }
          return item;
        }),
      ];
    });
  });

  const changeTextOfSecondItem = newtext => {
    setData(oldData => {
      return [
        ...oldData.map(item => {
          if (item.id === '3ac68afc-c605-48d3-a4f8-fbd91aa97f63') {
            return {
              ...item,
              title: newtext,
            };
          }
          return item;
        }),
      ];
    });
  };

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={data}
        renderItem={({item}) => (
          <ItemPureFunctional item={item} onSelect={onSelect.current} />
        )}
        keyExtractor={item => item.id}
      />
      <TextInput
        placeholder={'Enter some data'}
        style={{height: 40, borderColor: 'gray', borderWidth: 1}}
        onChangeText={setText}
        value={text}
      />
      <Button
        title="Change the text of the second list item"
        color="#f194ff"
        onPress={() => {
          changeTextOfSecondItem(text);
          setText('');
        }}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 24,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode