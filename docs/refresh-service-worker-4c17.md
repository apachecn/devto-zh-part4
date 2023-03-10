# 刷新服务人员

> 原文：<https://dev.to/andeersg/refresh-service-worker-4c17>

你有没有启用一个服务人员，然后意识到你的更新没有显示，因为缓存。我有。

根据 MDN 的消息，服务人员将每 24 小时左右或者当所有页面/实例关闭时更新一次。

如果您最近进行了更改或者网站被添加到主屏幕，这可能会是个问题。每当有新的服务人员可用时，可能有理由不强制更新服务人员，因此更好的解决方案是提示用户更新(如果他们想更新的话)。

## 如何检测何时有新员工

有两个部分来管理这一点。服务工作者必须监听来自浏览器的消息(客户端刷新)，并且浏览器必须监听新的服务工作者。

### 服务人员

为了监听服务工作者内部的消息，我们需要为消息事件添加一个事件监听器。

在我的解决方案中，我向服务人员发送一个带有 action 属性的对象。所以当服务工作者接收到`action: 'skipWaiting'`时，它知道激活挂起的工作者。

```
self.addEventListener('message', function(event) {
  if (event.data.action === 'skipWaiting') {
    self.skipWaiting();
  }
}); 
```

### 浏览器

首先你需要一个按钮来点击。

```
<button class="refresh-worker hidden">A new service worker is available, click to reload</button> 
```

我建议用 CSS 隐藏。如果不支持 JS 或服务人员，它就没有意义，可以隐藏起来。

```
const refreshButton = document.querySelector('.refresh-worker');
let newWorker = null;

refreshButton.addEventListener('click', (event) => {
  event.preventDefault();
  newWorker.postMessage({ action: 'skipWaiting' });
  window.location.reload();
}); 
```

首先，我们需要一个对按钮的引用(用于点击处理程序)和一个变量，以便在可用时将服务人员放入其中。

当按钮被点击时，我们向工作者发送消息，告诉它跳过等待并激活新的工作者。然后我们刷新页面。

接下来，我们必须监听服务人员的更新，这样我们就知道何时显示按钮并提示用户更新。

```
// Register service worker:
navigator.serviceWorker.register('service-worker.js')
  .then(function(registration) {
        // Listen for updates:
    registration.addEventListener('updatefound', () => {
      newWorker = registration.installing;
            // Listen for when the new worker is ready:
      newWorker.addEventListener('statechange', () => {
        switch (newWorker.state) {
          case 'installed':
            if (navigator.serviceWorker.controller) {
                            // Display button:
              refreshButton.classList.remove('hidden');
            }
            break;
        }
      });
    });
  });
} 
```

当 worker 准备好被激活时，我们可以显示按钮并通知用户有新版本可用。