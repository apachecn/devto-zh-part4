# 更好的 useReducer:使用 useReducer 将副作用与操作放在一起

> 原文：<https://dev.to/stillconor/colocating-side-effects-with-actions-using-usereducer-34e1>

如今,“主机代管”这个词被频繁使用。

在我看来，样式是最大的例子，围绕着*将*样式和组件放在一起，似乎有一个持续不断的对话，通常的论点是样式是组件的**部分**，没有它们就不会是现在的样子。将这两者放在一起可以让你在一个地方思考页面上会出现什么。

我不会在这里深入讨论这个问题，因为我希望把大部分时间*而不是*花在 twitter 上讨论风格上。

相反，我要谈的是 React hooks，我相信它们已经引入了一个地方，在这里我们有一个惊人的机会在数据获取和数据流方面进行共存。

我没有随意选择在标题图像中放置面包和奶酪表情符号，因为这恰好是我目前脑海中的两件事，我认为它们是一个很好的例子，说明在这个真实的世界中，每个人都告诉我的事情通常都在一起(但我没有收到 MTV，我对狂喜的笑声做出了回应)。

随着钩子越来越受欢迎，特别是 **useReducer** 我们经常开始看到它与 **useEffect** 以多种不同的方式在获取数据中配对。类似下面这个做作的例子:

```
function Avatar({ userName }) {
  const [state, dispatch] = useReducer(
    (state, action) => {
      switch (action.type) {
        case FETCH_AVATAR: {
          return { ...state, fetchingAvatar: true };
        }
        case FETCH_AVATAR_SUCCESS: {
          return { ...state, fetchingAvatar: false, avatar: action.avatar };
        }
        case FETCH_AVATAR_FAILURE: {
          return { ...state, fetchingAvatar: false };
        }
      }
    },
    { avatar: null }
  );

  useEffect(() => {
    dispatch({ type: FETCH_AVATAR });
    fetch(`/avatar/${usereName}`).then(
      avatar => dispatch({ type: FETCH_AVATAR_SUCCESS, avatar }),
      dispatch({ type: FETCH_AVATAR_FAILURE })
    );
  }, [userName]);

  return <img src={!fetchingAvatar && state.avatar ? state.avatar : DEFAULT_AVATAR} />
} 
```

除了我几乎肯定会犯的错误之外，这段代码应该可以工作，但是每当我做一些像加载头像这样的事情时，想到要费力地完成这段代码就像是一场噩梦。但是我仍然喜欢 useReducer 模式，所以我该怎么办呢？

我相信**reason**已经用 [**还原剂**](https://github.com/reasonml/reason-react/blob/master/docs/state-actions-reducer.md) 解决了这个问题

**ReasonReact** 提供了函数 *Update* 、 *UpdateWithSideEffect* 、*sideffect*和 *NoUpdate* ，这些函数用于包装从 reducer 返回的值，从而不允许过多的副作用与 reducer(我们仍然希望保持纯净)共存，而是允许将随之而来的副作用的 **INTENT** 共存。

我们可以将这个想法带到 React hooks 的世界中，给我们一个有些类似的体验，删除上面看到的多余代码，并为团队执行导致副作用的操作提供一个通用模式。希望能更容易理解代码，更容易审查拉请求，以及更少的错误到达最终用户。

在那个世界里，上面的代码可能是这样的。

```
function Avatar({ userName }) {
  const [{ avatar }, dispatch] = useReducerWithSideEffects(
    (state, action) => {
      switch (action.type) {
        case FETCH_AVATAR: {
          return UpdateWithSideEffect({ ...state, fetchingAvatar: true }, (state, dispatch) => {
                fetch(`/avatar/${usereName}`).then(
                  avatar =>
                    dispatch({
                      type: FETCH_AVATAR_SUCCESS,
                      avatar
                    }),
                  dispatch({ type: FETCH_AVATAR_FAILURE })
                );
          });
        }
        case FETCH_AVATAR_SUCCESS: {
          return Update({ ...state, fetchingAvatar: false, avatar: action.avatar });
        }
        case FETCH_AVATAR_FAILURE: {
          return Update({ ...state, fetchingAvatar: false })
        }
      }
    },
    { avatar: null }
  );

  useEffect(() => dispatch({ type: FETCH_AVATAR }) , [userName]);

  return <img src={!fetchingAvatar && state.avatar ? state.avatar : DEFAULT_AVATAR} />;
} 
```

我们现在能够将获取虚拟形象与我们意图获取虚拟形象的声明放在一起，允许我们在一段代码中准确地跟踪正在发生的事情。

我相信 **ReasonReact** 得到了这个**超级骗子极其正确的**，并且很高兴在我开发新功能时使用 React 钩子的这个模式。

你可以在这里看到一个实现这种模式的库[，老实说，你自己做这个或者类似的事情并不困难。](https://github.com/conorhastings/use-reducer-with-side-effects)

如果你对这个话题感兴趣，并且是旧金山湾区的，我会在 7 月的 React 会议上就这个话题发言

期待听听大家的看法！