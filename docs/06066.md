# Chrome:标签之间的通信

> 原文：<https://dev.to/rfornal/chrome-communication-between-tabs-12bg>

当把最近一次关于异步 JavaScript 的谈话放在一起时，我希望构建一个由 ***浏览器控制的*** 表示，它允许控制浏览器选项卡控制表示选项卡。特别是，我在考虑管理三件事:

1.  滑块位置
2.  幻灯片字体大小
3.  幻灯片操作

对于第三个，Slide Actions，我希望触发一些代码的显示(最好是在 Developer Tools > Console 中)以及潜在的运行代码。

作为一名长期的前端开发人员，我知道浏览器标签是沙盒化的，但随着时间的推移，我已经看到了这种类型的功能 ...但是记住在哪里是令人畏惧的。我还想做一些研究，而不是探究(感觉像是作弊)一些具有这种功能的展示工具(比如 [reveal.js](https://revealjs.com/) )。

我遇到的是 **BroadcastChannel** ，根据 caniuse.com[的说法，它在 Firefox 和 Chrome 中都得到支持。由于我无法想象用 IE 或 Edge 做演示，我认为这是很好的信息。](https://caniuse.com/#feat=broadcastchannel)

## 设置频道

这个功能的使用非常简单...这段代码在***【index.html】***JavaScript 代码(***_ functionality . js***)中初始化流程...

```
const pnChannel = new BroadcastChannel('le-slides-position');
const fsChannel = new BroadcastChannel('le-slides-font-size');
const anChannel = new BroadcastChannel('le-slides-actions'); 
```

Enter fullscreen mode Exit fullscreen mode

在 ***_navigation.js*** 、 ***_font-sizing.js*** 、 ***_code-examples*** 文件中，有匹配的声明...

```
// _navigation.js
const channel = new BroadcastChannel('le-slides-position');

// _font-sizing.js
const channel = new BroadcastChannel('le-slides-font-size');

// _code-examples.js
const channel = new BroadcastChannel('le-slides-actions'); 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**这些行中的每一行都在一个单独的文件中，因此在每一行上都使用了 ***常量通道*** 。

## 通道通信

这里，我们将只检查从控制**index.html**、**、*_ 功能、js* 、**代码发送数据...

```
const actions = {
  init: (force = false) => {
    if (!initFired || force) {
      fsChannel.postMessage('init');
      pnChannel.postMessage('init');
      anChannel.postMessage('init');
      initFired = true;
    }
  },

  up: () => {
    if (!upButton.hasClass('disabled')) {
      fsChannel.postMessage('trigger-up');              
    }
  },
  reset: () => {
    fsChannel.postMessage('trigger-reset');         
  },
  down: () => {
    if (!downButton.hasClass('disabled')) {
      fsChannel.postMessage('trigger-down');                
    }
  },

  previous: () => {
    if (!previousButton.hasClass('disabled')) {
      pnChannel.postMessage('trigger-previous');                
    }
  },
  next: () => {
    if (!nextButton.hasClass('disabled')) {
      pnChannel.postMessage('trigger-next');
    }
  },

  triggerAction: (action) => {
    anChannel.postMessage(action);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 位置通道

现在，看着**pn 通道**(位置通道)...我们可以看到 ***。on message***functionality 期望一个状态。发送的状态可以包括数据，在这种情况下，当前索引是什么...此外，还会发送额外的数据，例如上一次和下一次禁用状态，并且可以适当调整这些按钮。

```
pnChannel.onmessage = (states) => {
  cardIndex = states.data.currentIndex;
  updateContent();

  if (states.data.previousDisabled) {
    previousButton.addClass('disabled');
  } else {
    previousButton.removeClass('disabled');
  }

  if (states.data.nextDisabled) {
    nextButton.addClass('disabled');
  } else {
    nextButton.removeClass('disabled');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在 **_navigation.js** 文件中，它接收到一个 ***触发动作*** ，其数据实际上用于执行一些功能...

```
channel.onmessage = (triggerAction) => {
  actions[triggerAction.data]();
};

const actions = {
  init: () => {
    nextButton.hide();
    previousButton.hide();
  },

  'trigger-previous': () => {
    slideStateMachine.next('previous');
  },
  'trigger-next': () => {
    slideStateMachine.next('next');
  },

  'report-states': (index) => {
    channel.postMessage({
      currentIndex: index,
      previousDisabled: previousButton.hasClass('disabled'),
      nextDisabled: nextButton.hasClass('disabled')
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

有了这段代码，发送消息只需利用 ***就可以了。postMessage*** 一个频道的功能。

## 字体调整通道

看着 **fsChannel** 我们可以看到**T3。onmessage** 再次期待一个状态，允许分配按钮状态...

```
fsChannel.onmessage = (states) => {
  if(states.data.upDisabled) {
    upButton.addClass('disabled');
  } else {
    upButton.removeClass('disabled');
  }

  if(states.data.downDisabled) {
    downButton.addClass('disabled');
  } else {
    downButton.removeClass('disabled');
  }     
}; 
```

Enter fullscreen mode Exit fullscreen mode

这连接到 ***_font-sizing.js* *代码，再次触发各种动作...

```
channel.onmessage = (triggerAction) => {
  actions[triggerAction.data]();
};

const actions = {
  init: () => {
    upButton.hide();
    downButton.hide();
    resetButton.hide();
  },

  'trigger-up': () => {
    fontStateMachine.next('up');
  },
  'trigger-reset': () => {
    fontStateMachine.next('reset');      
  },
  'trigger-down': () => {
   fontStateMachine.next('down');
  },

  'report-states': () => {
    channel.postMessage({
      upDisabled: upButton.hasClass('disabled'),
      downDisabled: downButton.hasClass('disabled')
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 动作通道

查看 **anChannel** 我们可以看到，在这里，响应状态数据被简单地发送到 console.log...

```
anChannel.onmessage = (states) => {
  console.log('action reply:', states.data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

**_code-examples.js** 文件中的相关代码有点复杂...

```
channel.onmessage = (states) => {
  const cardAction = cardActions[states.data];
  if (states.data === 'init') {
    cardAction();
  } else {
    if (cardAction.showDisplay) {
      console.log(cardAction.display);      
    }
    cardAction.fn();        
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我会承认我为了一个特定的目的“欺骗”了一点...我用了一些 JSON 数据...

```
"fn":  "triggerImage('queues.png', false)" 
```

Enter fullscreen mode Exit fullscreen mode

...并且在**_ code-examples . js*****init***功能内，我将它们重新构建为可执行函数。因此，我能够使用 JSON 文件来控制每个屏幕上的元素，以及在显示屏幕上可以“执行”的内容...

```
const name = card.options[j].name;
const optionFn = new Function(card.options[j].fn);
cardActions[name] = {
  fn: optionFn,
  showDisplay: card.options[j].showFn,
  display: card.options[j].fn
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我从这个项目中学到了很多令人兴奋的东西，代码可以在我的 GitHub 账户上找到。我不想直接给出它，所以我不打算在这里链接它。

我的文章 [JavaScript 享受你的眼泪](https://dev.to/rfornal/javascript-enjoys-your-tears-4el)里的内容就是我用来呈现[单线程异步 JavaScript 的？](https://sessionize.com/s/bob-fornal/single-threaded_and_asynchronous_ja/22652)。

这是一个有趣的项目，从某种意义上来说，我可以看到自己将它融入到一个演示中。