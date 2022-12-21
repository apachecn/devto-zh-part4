# 带 React 挂钩的简单动画

> 原文：<https://dev.to/spaciecat/video-simple-animations-with-react-hooks-5e29>

[https://www.youtube.com/embed/KT-qtuBgAZI](https://www.youtube.com/embed/KT-qtuBgAZI)

嗨 DevPeeps！👋

我刚刚发布了我的第一个视频教程*(也是我的第一个 youtube 视频)*！

它是关于使用 react 钩子来创建简单的动画！

我最近对钩子非常感兴趣(尤其是将它们链接到其他 web APIs)，所以期待不久的将来会有更多的钩子视频！

此外，如果有人对视频有任何反馈，我真的很想听听！我是视频制作的新手，需要所有我能得到的建议！

感谢阅读和/或观看！

* * *

```
function useAnimation(duration) {
    const [progress, setProgress] = useState(0)
    const [startTime, setStartTime] = useState(Date.now())
    const reset = () => setStartTime(Date.now())
    useEffect(() => {
        let queuedFrame
        const frame = () => {
            const now = Date.now() - startTime
            if (now < duration) queuedFrame = requestAnimationFrame(frame)
            setProgress(Math.min(1, now / duration))
        }
        frame()
        return () => cancelAnimationFrame(queuedFrame)
    }, [startTime, duration])
    return [progress, reset]
} 
```

Enter fullscreen mode Exit fullscreen mode