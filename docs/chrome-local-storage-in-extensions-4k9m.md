# 扩展中的 Chrome 本地存储

> 原文：<https://dev.to/milandhar/chrome-local-storage-in-extensions-4k9m>

为了我在 Flatiron School 的 Web 开发课程中的 Javascript 项目，我和我的合作伙伴决定构建一个 Chrome 扩展。当我们得到项目需求时，我们想创造一些我们实际会用到的东西，并且了解一个我们还没有探索过的新领域。因为我们经常发现自己需要提醒自己休息一下，我们决定做一个休息计时器扩展！

虽然我们知道创建一个扩展将是一个不同于其他 Javascript 项目的挑战，但我们低估了让应用程序的数据持续足够长时间的难度。Chrome 扩展的美妙之处在于，它们允许用户在工具栏上访问一个最小的应用程序，随时打开和关闭它。然而，即使像中断计时器这样简单的挑战也很困难，因为单击扩展会有效地杀死 Javascript 会话，清除所有常规变量数据。

幸运的是，Chrome 提供了自己的 chrome.storage API 来帮助解决这个问题，使得开发扩展变得可行。

## 什么是本地存储？

到目前为止，在 Flatiron 学校，我们已经接触了一些浏览器存储方法，如 Rails 上下文中的 cookies 和会话。 [Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 包含两种机制:localStorage 和 sessionStorage。LocalStorage 是[类似于 sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) ，因为它们都是只读的，但是存储在 localStorage 中的数据没有到期时间，而 sessionStorage 在每次页面会话结束时都会被清除(选项卡关闭)。

由于 localStorage 在多个页面会话中是持久的，所以它是保存 Chrome 扩展数据的理想选择，允许用户点击关闭和打开扩展以满足他们的需求。

## 在你的扩展中使用 Chrome 的本地存储

谷歌开发了自己的 [chrome.storage API](https://developer.chrome.com/extensions/storage) ，提供与 Web 存储 API 相同的存储容量。在使用 chrome.storage 时需要记住的一点是，存储空间没有加密，所以不应该存储机密的客户数据。

在你的扩展中实现 Chrome 的存储时，有几个步骤是你必须采取的，还有一些事情要记住:

### 清单

每个 Chrome 扩展都有一个名为`manifest.json`的[清单文件](https://developer.chrome.com/extensions/manifest)，它描述了应用程序并提供了重要的元数据，如名称、脚本、权限和版本。如果你想使用 chrome 存储，你必须声明你的`manifest.json`文件的“存储”权限。下面是我们扩展的一个片段:

```
{
  "name": "break alarm",
  "version": "1.0",
  "description": "Be more productive.",
  "permissions": ["alarms", "declarativeContent", "storage", "<all_urls>"],
  "background": {
    "scripts": ["helpers.js", "background.js"],
    "persistent": false
  },
...
} 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】storage . sync](#storagesync)

Chrome 的存储 API 和 Web 存储 API 的一个关键区别是，Chrome 提供了一个`storage.sync`机制，允许用户数据与 Chrome sync 自动同步。这将允许用户在不同的设备上访问他们的数据，假设他们的帐户上启用了 Chrome Sync。
所以在开发你的扩展时，你需要决定是使用`storage.local`还是`storage.sync`。我们决定使用`storage.local`作为我们的休息计时器扩展。两者之间的主要区别是存储限制:`storage.local`提供大约 5.2MB 的数据，而`storage.sync`的限制是大约 102KB，最多存储 512 个项目。`storage.sync`的一个很好的特点是，即使用户禁用了 Chrome Sync，它仍然会工作，就像`storage.local`一样。

### 异步调用

chrome.storage 的另一个很酷的特性是它的所有调用都是异步的，而 Web 存储 API 中的 localStorage 是同步调用的。虽然在我们的项目中，我发现异步获取每个本地存储变量的过程有时很烦人，但这种机制允许扩展运行得更快。

在 chrome.storage 中设置一个新对象相对简单。在我们的 break timer 扩展中，当用户点击“resume”按钮时，我们将在本地存储中设置以下内容:

```
chrome.storage.local.set({ isPaused: false })
chrome.storage.local.set({'user_name': json.user_name})
chrome.storage.local.set({'phone_number': json.phone_number})
chrome.storage.local.set({'redirect_url': json.default_url})
chrome.storage.local.set({'break_time': json.default_break_time}); 
```

Enter fullscreen mode Exit fullscreen mode

当从 chrome.storage 调用保存的对象时，我们可以使用一个批量异步`get`函数，一次访问多个对象。下面是我们的中断计时器扩展的一个例子:

```
 chrome.storage.local.get(['user_name', 'phone_number', 'redirect_url',
 'break_time', 'isPaused'], function(data) {
    h1.textContent = data.user_name
    timerInput.value = data.break_time
    urlInput.value = data.redirect_url
    phoneInput.value = data.phone_number

if (!data.isPaused) {
      updateCountdown();
      countdownInterval = setInterval(updateCountdown, 100);
      isNotPausedDisplay();
    } else {
      chrome.storage.local.get('pausedCount', function(data) {
        counterElement.innerHTML = secToMin(data.pausedCount);
      });
      isPausedDisplay();
    }

  }); 
```

Enter fullscreen mode Exit fullscreen mode

### 从 Chrome 存储器中移除项目

为了充分利用 chrome.storage，了解如何以及何时删除存储的数据非常重要。在我们的 timer 扩展中，我们存储了用户数据，比如他们首选的休息时间、首选的“工作”url，以及他们用于定时提醒的电话号码。我们希望在用户登录时保存他们的数据，这样他们就可以设置几个计时器，如果他们返回到扩展，就不必重新输入所有设置。然而，当他们注销时，我们希望从 chrome.storage 中删除用户存储的数据，以便另一个用户可以在同一台机器上登录。

幸运的是，chrome.storage 让这一切变得很容易！Chrome 提供了一个`chrome.storage.local.remove`功能，可以让你指定想要移除哪个(哪些)键，还提供了一个`chrome.storage.local.clear`功能，可以清除存储在扩展本地存储器中的所有数据。这两个函数都有可选的回调函数参数，这些参数将在成功或失败时返回(然后将设置 runtime.lastError)。

因为在我们的扩展中，我们希望在用户退出应用程序后删除所有的数据，所以我们编写了一个方法，使用带有错误捕捉回调的 clear 函数:

```
function clearLocalStorage(){
  chrome.storage.local.clear(function() {
    var error = chrome.runtime.lastError;
      if (error) {
        console.error(error);
      }
   })
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

总的来说，chrome.storage 是一个不可或缺的工具，它可以确保我们的扩展正确运行，并保存我们想要的用户数据。虽然一开始构建一个扩展看起来令人生畏，但有这么多新文件和规范，学习如何使用 chrome.storage 使事情变得容易得多。我强烈推荐在开发你的第一个扩展时阅读 Chrome 的扩展文档,它非常有用而且简洁。

感谢阅读！