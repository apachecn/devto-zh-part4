# 我正在创建一个深入的纯功能组件，平面列表，和真正酷的东西，你可以用钩子！这是视频 1

> 原文：<https://dev.to/technoplato/i-m-creating-a-deep-dive-into-pure-functional-components-flatlists-and-really-cool-stuff-you-can-do-with-hooks-here-s-video-1-50jj>

[https://www.youtube.com/watch?v=vNq1P06KkoU&feature = youtu . be](https://www.youtube.com/watch?v=vNq1P06KkoU&feature=youtu.be)

```
import React, {useState, useRef, useMemo} from 'react';
import {
  SafeAreaView,
  TouchableOpacity,
  FlatList,
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
      <Text style={styles.title}>{title}</Text>
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
        <Text style={styles.title}>{title}</Text>
      </TouchableOpacity>
    );
  }, [selected]);
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

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={data}
        renderItem={({item}) => (
          <ItemPureFunctional item={item} onSelect={onSelect.current} />
        )}
        keyExtractor={item => item.id}
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