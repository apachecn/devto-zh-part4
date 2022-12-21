# 带提取的进度指示器

> 原文：<https://dev.to/samthor/progress-indicator-with-fetch-1loo>

快速提示:[在之前的演示](https://dev.to/samthor/pwas-that-download-like-apps-fd6)中，我展示了我们如何下载一个大文件来为服务人员播种内容。如果你看得够快，你会看到一个进度指示器。(虽然对于一个小文件来说，眨眼就错过了！)👀

代码非常简单。让我们从一个简单的异步`fetch` :
开始

```
async function downloadFile(url) {
  const response = await fetch(url);
  const arrayBuffer = await response.arrayBuffer();
  const bytes = new Uint8Array(arrayBuffer);
  // do something with bytes
} 
```

Enter fullscreen mode Exit fullscreen mode

在返回字节之前，`arrayBuffer`调用一直等到整个目标下载完毕。相反，我们可以一次消耗文件的“块”(因为随着时间的推移，我们将获得文件的一部分),以获得百分比的感觉。

## 检查表头

首先，我们读取响应的“Content-Length”头:这是服务器在数据之前发送给我们的，因此我们可以实际计算出我们已经进行了多远:

```
 const response = await fetch(url);
  const length = response.headers.get('Content-Length');
  if (!length) {
    // something was wrong with response, just give up
    return await response.arrayBuffer();
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果没有有效的头，那么要么是响应有问题，要么是服务器没有告诉我们它有多长。你可以回到之前的状态。

## 大块

当数据到达时，您的浏览器正在从远程服务器接收字节块。因为我们知道总响应时间有多长，所以我们可以为它准备一个缓冲:

```
 const array = new Uint8Array(length);
  let at = 0;  // to index into the array 
```

Enter fullscreen mode Exit fullscreen mode

然后抓住读者，这让我们得到大块:

```
 const reader = response.body.getReader(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以存储从*到*(在`at`中)的位置，并将每个新的块插入到输出:

```
 for (;;) {
    const {done, value} = await reader.read();
    if (done) {
      break;
    }
    array.set(value, at);
    at += value.length;
  }
  return array; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的循环中，我们可以用百分比来记录进度，类似于:

```
 progress.textContent = `${(at / length).toFixed(2)}%`; 
```

Enter fullscreen mode Exit fullscreen mode

然后如上，只需返回数组:我们完成了。

# 鳍

Twenty