# 使用 Typescript 将 React 和 Redux 提升到下一个级别

> 原文：<https://dev.to/leomeloxp/taking-react-and-redux-to-the-next-level-with-typescript-1m84>

## 开场白

如果您曾经使用过 Redux，那么您就会知道我们编写 Redux 逻辑的许多方式以及它的工作原理依赖于我们提前了解我们的状态。这种需求与好的 typescript 代码迫使我们在构建输出 JavaScript 代码之前定义函数和变量的形状是一致的。

由于我将在不久的将来大量使用 Redux，并且我已经有一段时间没有使用它了，所以我决定通过 Level Up Tutorials(LUT)React 和 Redux For Everyone 课程来刷新我关于 Redux 的许多概念的记忆。为了给它添加一些趣味，也因为我喜欢 TS，这次我决定用 Typescript 编写教程应用程序。

这篇文章收集了我的一些想法和经历。

## 一些示例代码

你可以在[我的 github](https://github.com/leomeloxp/lut-react-redux-typescript) 上通过 git 标签看到课程的代码和我的每一步。我还创建了一个[代码沙箱](https://codesandbox.io/s/59sp8)，其中包含了`react-redux`的最小设置和一个使用 Typescript 连接的组件。

您可以随意浏览它们，或者将它们作为您自己代码的灵感。这里我主要用 Github 上的回购来说明一些观点。

## 定义全局状态和根减速器

在我的 repo 中，我有两个 reducers 被`combineReducers`合并，它们的状态定义如下:

*   `movies`

```
export interface IReduxMoviesState {
  movies: IMovie[];
  moviesLoaded: boolean;
  moviesLoadedAt?: number;
  movie?: IMovie;
  movieLoaded: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `toggle`

```
export interface IReduxMessageState {
  messageVisibility: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

通过我们的 reducers 返回这些状态中的每一个，我们可以定义全局应用程序状态如下:

```
const rootReducer = combineReducers({
  toggle,
  movies
});

export type AppState = ReturnType<typeof rootReducer>; 
```

Enter fullscreen mode Exit fullscreen mode

这使得`AppState`看起来像:

```
type AppState = {
  toggle: IReduxMessageState;
  movies: IReduxMoviesState;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这很好，因为在任何使用 redux 状态的地方，我们都确切地知道它看起来是什么样子，以及在连接组件时我们可以从中引用什么。

## 定义动作创建者和动作类型常量

Redux 中常见的做法是将动作类型定义为常量。因为我们使用 Typescript，所以我们可以利用枚举和扩展接口来使我们的代码更具描述性。在我的回购中，我有以下动作类型的枚举:

```
export enum EReduxActionTypes {
  GET_MOVIE = 'GET_MOVIE',
  GET_MOVIES = 'GET_MOVIES',
  RESET_MOVIE = 'RESET_MOVIE',
  TOGGLE_MESSAGE = 'TOGGLE_MESSAGE'
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你熟悉 Typescript，你会发现我让枚举定义了值。这是为了避免枚举键被赋予数值，这可能会降低代码的弹性。无论哪种方式，这将使定义我们的动作创建者更容易一些。

我基于一个具有更一般的`type`值的接口定义了这些动作，这是一个非常简单的框架，但是它允许很好的可伸缩性:

```
export interface IReduxBaseAction {
  type: EReduxActionTypes;
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，在 movies reducer 的情况下，有几个不同的动作可以被调度:

```
export interface IReduxGetMoviesAction extends IReduxBaseAction {
  type: EReduxActionTypes.GET_MOVIES;
  data: IMovie[];
}
export interface IReduxGetMovieAction extends IReduxBaseAction {
  type: EReduxActionTypes.GET_MOVIE;
  data: IMovie;
}

export interface IReduxResetMovieAction extends IReduxBaseAction {
  type: EReduxActionTypes.RESET_MOVIE;
} 
```

Enter fullscreen mode Exit fullscreen mode

与 Typescript 中的许多事情一样，您不需要知道数据的值是如何定义的，在这种情况下，您需要知道的是每个动作都将包含正确类型的对象或动作的`data`属性的数组。

通过将这些类型聚合成一个联合类型，我可以编写如下的 movies reducer:

```
type TMoviesReducerActions = IReduxGetMoviesAction | IReduxGetMovieAction | IReduxResetMovieAction;

export default function(state: IReduxMoviesState = initialState, action: TMoviesReducerActions) {
  switch (action.type) {
    case EReduxActionTypes.GET_MOVIES:
      return { ...state, movies: action.data, moviesLoaded: true, moviesLoadedAt: Date.now() };
    case EReduxActionTypes.GET_MOVIE:
      return { ...state, movie: action.data, movieLoaded: true };
    case EReduxActionTypes.RESET_MOVIE:
      return { ...state, movie: undefined, movieLoaded: false };
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个缩减器是这个 ts 和 Redux 实现中我最喜欢的部分之一。

因为我对每个动作使用不同的`EReduxActionTypes`值。当我在不同的`case`中得到`action.data`时，Typescript 已经知道数据是正确的类型，即`IReduxGetMovieAction`的`Imovie`和`IReduxGetMoviesAction`的`IMovie[]`(电影数组)。

这是一个非常强大的东西。

在我的教程应用程序中，reducers 相当简单，但我们已经可以看到，扩展这不会是一个很大的问题，也不会真正增加我们商店的复杂性。

如果我们考虑到 VS Code 为 Typescript 提供的优秀开发人员体验，这一点尤其正确。

## 连接组件并使用本店的状态

为了用一个`MoviesList`组件连接我们的`movies`状态，使用的代码如下:

```
const mapStateToProps = (state: AppState) => ({
  movies: state.movies.movies,
  isLoaded: state.movies.moviesLoaded,
  moviesLoadedAt: state.movies.moviesLoadedAt
});

const mapDispatchToProps = (dispatch: Dispatch<AnyAction>) =>
  bindActionCreators(
    {
      getMovies
    },
    dispatch
  );

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(MoviesList); 
```

Enter fullscreen mode Exit fullscreen mode

这里有相当多的代码和重新赋值。通常这可能会导致一些混乱，比如哪些道具将可用于我们的`MoviesList`组件，但是 Typescript 将通过让我们解析`mapStateToProps`和`mapDispatchToProps`的类型定义并在创建我们的组件
时使用它来确保这不会发生

```
class MoviesList extends PureComponent<ReturnType<typeof mapStateToProps> & ReturnType<typeof mapDispatchToProps>, {}> {
  // Code for the component goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以通过创建一个`MoviesList`道具类型来稍微简化事情，比如:

```
type TMoviesListProps = ReturnType<typeof mapStateToProps> & ReturnType<typeof mapDispatchToProps>;

class MoviesList extends PureComponent<TMoviesListProps, {}> {
  // Code for the component goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们试图在我们的组件中引用来自`this.props`的任何东西，我们将完全看到`mapStateToProps`和`mapDispatchToProps`提供给我们的所有属性。

## 结论

尽管使用 Redux 管理状态并遵循它的标准实践会导致我们将逻辑分散到许多文件和/或添加大量样板代码。通过使用 Typescript，我们可以极大地提高代码的可读性，并可能使那些不了解复杂应用程序的来龙去脉、每个部分负责什么以及他们期望从其他组件获得什么的人更容易理解。

教程应用程序可能不是最复杂的，可能我没有充分利用 Typescript。我仍然认为它强调了 Typescript 的一些功能，以及为什么最近越来越多的人开始研究它。

你如何看待 Typescript，以及它如何改变我们开发人员在创建和扩展应用程序时的体验？欢迎在下面评论或在社交媒体上联系我，详情可在我的网站上找到: [leomeloxp.dev](https://www.leomeloxp.dev) 。

最后一件事。当编写这个应用程序时，我试图让代码尽可能接近 LUT React 和 Redux for Everyone 课程中的原始代码。如果你想了解更多关于课程或升级教程，请随时访问他们的网站。

这个帖子不是由升级教程发起的，我只是真的很喜欢他们的内容。