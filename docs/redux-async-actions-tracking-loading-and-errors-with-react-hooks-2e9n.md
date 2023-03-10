# 还原异步操作。用 React 钩子跟踪装载和错误。

> 原文：<https://dev.to/atamurana/redux-async-actions-tracking-loading-and-errors-with-react-hooks-2e9n>

如果您使用 redux 和 async 操作，那么您可能不得不处理这样一种情况，您需要监视这个操作的加载和错误状态。随着钩子的出现，方便地将这个逻辑转移到一个块中并在任何地方使用它成为可能。

```
import { useState, useCallback } from 'react';
import { useDispatch } from 'react-redux';

function useAsyncAction(action, dependeces = []) {
  const dispatch = useDispatch();
  const [loading, setLoading] = useState(false);
  const [isError, setIsError] = useState(false);

  const asyncAction = useCallback(
    (...args) => {
      async function callback() {
        setLoading(true);
        try {
          const res = await dispatch(action(...args));
          setIsError(false);
          setLoading(false);
          return res;
        } catch (e) {
          setLoading(false);
          setIsError(true);
          return e;
        }
      }
      callback();
    },
    [action, ...dependeces],
  );

  return [asyncAction, loading, isError];
} 
```

现在你可以在你的功能组件中使用这个钩子了。

```
// …
  const [load, loading, isError] = useAsyncAction(() => loadActivityRequest(applicationId), [applicationId]);
// …
  load(); 
```