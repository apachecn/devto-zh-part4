# 对自定义挂钩问题做出反应

> 原文：<https://dev.to/ttamminen/react-custom-hook-question-10me>

我已经实现了一个 React 定制钩子。在项目中，我注意到在获取 React 组件的数据时有一个模式。

*   首先，我们检查存储中是否已经有数据(函数中的选择器将给出数据)
*   如果没有，我们将分派一个具有令牌和参数的动作

因为这是应用程序的一个重要部分，我想有钩子经验的人可能会看看这个。

尤其是有效载荷和动作部分。我应该使用`usePrevious`钩子和使用来自 lodash 的`isEqual`。或者，`useMemo`？。

感谢所有的想法！

```
/* annotated version of the hook */

export function useFetchIfNeeded<T>(
  selector: (state: AppState) => T,
  action: (payload: any) => ThunkAction<any, any, any, any>,
  payload: Object,
  checkFn?: (input: T) => boolean
) {
  // redux hook
  const dispatch = useDispatch()

  // get token
  const token = useSelector((state: AppState) => state.global.token)

  // get data using the selector function
  const data = useSelector(selector)

  // use check function to verify do we need to fetch data or not
  // default is null comparison
  const check = useCallback(() => (checkFn ? checkFn(data) : data !== null), [
    checkFn,
    data
  ])
  const fetch = useCallback(() => {
    if (!token) {
      return
    }

    if (check()) {
      return
    }

    dispatch(
      action({
        ...payload,
        token
      })
    )
    // TODO: action and payload are objects, should I make my own comparison?
  }, [dispatch, token, check, action, payload])
  useEffect(() => {
    fetch()
  }, [fetch]) 
```