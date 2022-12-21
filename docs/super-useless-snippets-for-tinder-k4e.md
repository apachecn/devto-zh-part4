# 😛Tinder 的无用代码😛

> 原文：<https://dev.to/0xkoji/super-useless-snippets-for-tinder-k4e>

正如你们中的一些人所知道的，有时真的需要为完全无用的东西编写代码😛

### 1。打开谷歌浏览器和`Developer Tools`

### 2。点击`Toggle device toolbar`按钮(您可以选择任何想要使用的设备)

### 3。去 https://tinder.com/app/recs[的](https://tinder.com/app/recs)

### 4。复制并粘贴以下内容之一

对于`not like`按钮

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        ele[1].click();
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode

对于`like`按钮

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        ele[3].click();
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode

#### 随机版本

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        const rand = Math.random();
        if(rand >= 0.5) {
          ele[1].click();
        } else {
          ele[3].click();
        }
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode