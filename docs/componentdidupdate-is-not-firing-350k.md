# ComponentDidUpdate 没有启动！！！！！

> 原文：<https://dev.to/hasan101002/componentdidupdate-is-not-firing-350k>

我是这样更新 reducer 中的某个 redux 状态的，

```
case UPDATE_SOMETHING:
      return {
        ...state,
        something: action.something,
      }; 
```

Enter fullscreen mode Exit fullscreen mode

我检查了如下的状态更新，

```
componentDidUpdate(prevProps, prevState) {
    const { something } = this.props;
    if (!_.isEqual(prevProps.something, something)) {
      this.setState({ something });
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

但问题是，在 redux 调试器中我可以看到我的 redux 状态是 update，但是 componentDidUpdate 不再触发了！！！！。在疑惑了一会儿之后，我尝试使用浅层复制来更新 redux 状态。像这样，

```
case UPDATE_SOMETHING:
      return {
        ...state,
        something: {
          ...state.something,
          ...action.something,
        },
      }; 
```

Enter fullscreen mode Exit fullscreen mode

它，现在工作了！！！！！！