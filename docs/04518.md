# 如何在 Javascript 中安全地访问深度嵌套的对象

> 原文：<https://dev.to/onmyway133/how-to-safely-access-deeply-nested-object-in-javascript-3n62>

对象的属性可以为空或未定义。

一步一步的访问很繁琐

```
props.user &&
props.user.posts &&
props.user.posts[0] &&
props.user.posts[0].comments 
```

动态解析路径太巧妙，最后涉及到字符串，这是一个 no no

```
const get = (p, o) =>
  p.reduce((xs, x) => (xs && xs[x]) ? xs[x] : null, o)

const getUserComments = get(['user', 'posts', 0, 'comments']) 
```

相反，让我们使用函数并显式捕捉错误，默认情况下使用回退

```
const get: (f, defaultValue) => {
    try {
        const value = f()
        if (isNotNullOrUndefined(value)) {
            return value
        } else {
            return defaultValue
        }
    } catch {
        return defaultValue
    }
}

const comments = get(() => { .user.posts[0].comments }, []) 
```

## 阅读更多

*   [https://medium . com/JavaScript-inside/safely-access-deep-nested-values-in-JavaScript-99bf 72 a 0855 a](https://medium.com/javascript-inside/safely-accessing-deeply-nested-values-in-javascript-99bf72a0855a)