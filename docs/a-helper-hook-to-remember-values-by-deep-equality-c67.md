# 通过深度相等来记忆值的助手钩子

> 原文：<https://dev.to/kelleyvanevert/a-helper-hook-to-remember-values-by-deep-equality-c67>

当然，每个 React hook 爱好者都会有一个用例，在某个时间点对依赖关系参数进行深度(结构)等式检查。不用每次需要时都手工制作这些东西，或者导入一个助手库，这里有一个非常简单的助手钩子来帮你:

```
import { useRef } from "react";
import isEqual from "react-fast-compare";

export default function remember<T>(value: T): T {
  const ref = useRef<T>(value);
  if (!isEqual(value, ref.current)) {
    ref.current = value;
  }
  return ref.current;
} 
```

可以这样用:

```
const something = useMemo(expensiveComputation, [ remember(input) ]); 
```

那不是很可爱吗？:D