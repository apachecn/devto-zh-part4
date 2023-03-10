# 带 React 挂钩的键盘输入

> 原文：<https://dev.to/spaciecat/keyboard-input-with-react-hooks-3dkm>

[https://www.youtube.com/embed/hdBz7irTpyk](https://www.youtube.com/embed/hdBz7irTpyk)

猜猜谁回来了，*是我*！

我真的很喜欢制作我的第一个 youtube 视频，所以我决定开始制作一个定制 react 钩子的系列！

这个是关于创建一个`useKey`钩子，它允许你知道某个键盘键何时被按下。我还介绍了一些“钩子规则”，短路评估是如何违反规则的，以及如何绕过它..

再次，任何反馈都非常感谢，感谢阅读和/或观看！

* * *

```
function useKey(key) {
    // Keep track of key state
    const [pressed, setPressed] = useState(false)

    // Does an event match the key we're watching?
    const match = event => key.toLowerCase() == event.key.toLowerCase()

    // Event handlers
    const onDown = event => {
        if (match(event)) setPressed(true)
    }

    const onUp = event => {
        if (match(event)) setPressed(false)
    }

    // Bind and unbind events
    useEffect(() => {
        window.addEventListener("keydown", onDown)
        window.addEventListener("keyup", onUp)
        return () => {
            window.removeEventListener("keydown", onDown)
            window.removeEventListener("keyup", onUp)
        }
    }, [key])

    return pressed
} 
```

Enter fullscreen mode Exit fullscreen mode