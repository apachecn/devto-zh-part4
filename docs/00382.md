# Javascript 中的匿名&箭头函数

> 原文：<https://dev.to/timrodz/anonymous-arrow-functions-in-javascript-36fj>

我一直在探索 javascript anonymous & arrow 函数，发现这是一个非常有趣的难题:这些函数的返回类型是什么？对其中一些如何工作(或不工作)的解释加分

```
export const values = {
    key: 'value',
    getKey: function () {
        return function() {
            return this.key;
        }
    },
    getKeyArrow: function () {
        return () => this.key;
    },
    getKeyArrowCall: function () {
        return (() => this.key)();
    }
};

const v1 = values.getKey(); // function
v1(); // undefined
const v2 = values.getKeyArrow(); // function
const v3 = values.getKeyArrowCall(); // value 
```

Enter fullscreen mode Exit fullscreen mode

**编辑**:我把 getKey 改得更难一点。前一版本执行`return this.key;`