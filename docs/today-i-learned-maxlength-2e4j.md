# 今天我学习了 maxlength

> 原文：<https://dev.to/dhkamp/today-i-learned-maxlength-2e4j>

# [到-最大长度](#til-maxlength)

*2019 年 7 月 10 日*

我正在构建一个 vue 组件，它应该限制用户可以输入的字符(字母和数字)的数量。为了实现这个行为，我做了如下的事情

```
<template>
    <input type="text" @keydown="handleKeyDown" v-model="value"/>
</template>
<script>
export default {
    name: 'BaseInput',
    data() {
        return {
            valuie: 'Lorem ipsum'
        }
    },
    methods: {
        handleKeyDown(e) {
            if(this.value.length >= 50 && e.keyCode >= 48 && e.keyCode <= 90) {
                e.preventDefault();
                return;
            }
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这应该做的是启用所有的键 Aa-Zz 和 0-9，只要没有达到字符限制，在本例中是 50。像 DEL，ENTER，TAB…这样的系统键应该不会受此影响而一直工作。

乍一看，这似乎工作得很好，但是与用户输入一样，有许多在实现时未知的边缘情况，可能会导致错误。

感谢一位更有经验的同事，我已经了解了 **maxlength** 。这是一个限制输入字符数的浏览器实现，工作原理是这样的

```
<input type="text" maxlength="50" /> 
```

Enter fullscreen mode Exit fullscreen mode

与上面的效果相同，但是代码更少。

## 告诫

1.  以编程方式设置值

仍然可以通过编程方式设置有限字段的值，例如

```
<input type="text" id="testid" maxlength="10" />

<script>
    const el = document.getElementById('testid');
    el.value = 'Text with more than ten characters';
    // This will work just fine.
</script> 
```

Enter fullscreen mode Exit fullscreen mode

1.  maxlength(大部分)不是一个合适的验证器

如果您正在使用一个输入长度很重要的 API，maxlength 不能代替适当的验证，因为数据仍然可以被 AJAX 或类似的操作。

## 来源

*   [https://developer . Mozilla . org/de/docs/Web/HTML/Element/Input](https://developer.mozilla.org/de/docs/Web/HTML/Element/Input)

*   [https://html.com/attributes/input-maxlength/](https://html.com/attributes/input-maxlength/)