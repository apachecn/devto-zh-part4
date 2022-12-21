# JavaScript 生成器的用例

> 原文：<https://dev.to/rfornal/use-cases-for-javascript-generators-1npc>

在我对 JavaScript 的一次深入研究中，我遇到了 ***生成器*** 。他们看起来很有趣。

然后，我寻找一些生成器的用例。看了看。看了看。

最终，我找到了一个简单的发电机油门示例。在所有这些研究之后，我决定看看如何使用它们。因为我在做一个异步的 JavaScript 演讲( [JavaScript 享受你的眼泪](https://dev.to/rfornal/javascript-enjoys-your-tears-4el))，所以我写了一个状态机来帮助在幻灯片中定位和管理演示端的字体大小。

我的发现记录在这里...

> 生成器是可以退出并在以后重新进入的函数。它们的上下文(变量绑定)将在重入时被保存。MDN。

功能暂停然后再恢复的能力。生成器返回一个迭代器。创建时，不执行生成器内部的代码。

*   解决“推理”问题。
*   允许非“运行到完成”行为。仅限局部阻塞。
*   状态机的语法形式。
*   协作并发与抢先并发。

## 发电机的优点

### 懒评

这是一个求值模型，它将表达式的求值延迟到需要它的值时。也就是说，如果不需要该值，它就不会存在。它是按需计算的。

### 记忆高效

惰性求值的一个直接后果是生成器是内存高效的。唯一生成的值是那些需要的值。对于普通函数，所有的值都必须预先生成并保存起来，以备以后需要使用。然而，有了生成器，计算就推迟了。

## 用例

下面是一些生成器用例...

### 无限重复排列

这篇文章(作者 Shawn Reisner)首先让我对这个话题产生了兴趣。

*   [ES6 发电机的快速实用用例](https://itnext.io/a-quick-practical-use-case-for-es6-generators-building-an-infinitely-repeating-array-49d74f555666?gi=e003deb863db)

### 生成唯一标识符

这是来自一个帖子(作者 Nick Scialli [@nas5w](https://dev.to/nas5w) ): [推特](https://twitter.com/nas5w/status/1154466559051669504?s=20)。

> javascript 生成器的一个有趣用例:生成无限数量的唯一标识符！

```
function * idCreator() {
  let i = 0;
  while (true) yield i++;
}

const ids = idCreator();

console.log(ids.next().value); // 0
console.log(ids.next().value); // 1
console.log(ids.next().value); // 2
// etc ... 
```

Enter fullscreen mode Exit fullscreen mode

### 油门发电机

这个生成器将在一段时间内(以毫秒为单位)限制一个函数。

```
export function * throttle(func, time) {
  let timerID = null;
  function throttled(arg) {
    clearTimeout(timerID);
    timerID = setTimeout(func.bind(window, arg), time);
  }
  while(true) throttled(yield);
}

export class GeneratorThrottle {

  constuctor() {};

  start = () => {
    thr = throttle(console.log, 3000);
    thr.next('');
  };

  toString = () => {
    console.log(throttle);
    console.log('start =', this.start);
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 内容状态机

```
export class ContentStateMachine {
  _content;
  _default;
  _statePatterns;
  _returnState;
  _changeAlgorithm;

  _machine;

  constructor(settings) {
    this._content = settings.content;
    this._default = settings.defaultIndex;
    this._statePatterns = settings.statePatterns;
    this._returnState = settings.returnState;
    this._changeAlgorithm = settings.changeAlgorithm;

    const machineSettings = {
      'content': this._content,
      'defaultIndex': this._default,
      'statePatterns': this._statePatterns,
      'returnState': this._returnState
    };
    this._machine = this.stateMachine(machineSettings);
    return this._machine;    
  };

  stateMachine = function * stateMachine(settings) {
    const content = settings.content;
    const defaultIndex = settings.defaultIndex;
    const statePatterns = settings.statePatterns;
    const returnState = settings.returnState;

    let currentIndex = defaultIndex;
    while (currentIndex >= 0 && currentIndex < content.length) {
      if (this._changeAlgorithm) {
        const states = returnState(content, currentIndex);
        this._changeAlgorithm(states, currentIndex);
      }
      const changeType = yield returnState(content, currentIndex);
      currentIndex = statePatterns[changeType](content, currentIndex);
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

用作字体状态机...

```
import { ContentStateMachine } from '/scripts/presentation/_content-state-machine.js';

$(document).ready(() => {

  const main = $('.main');
  const upButton = $('.up');
  const downButton = $('.down');
  const resetButton = $('.reset');

  const channel = new BroadcastChannel('le-slides-font-size');
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
  channel.onmessage = (triggerAction) => {
    actions[triggerAction.data]();
  };

  const sizes = [
    'fsm05', 'fsm04', 'fsm03', 'fsm02', 'fsm01',
    'fs00',
    'fsp01', 'fsp02', 'fsp03', 'fsp04', 'fsp05'
  ];
  const defaultIndex = Math.floor(sizes.length / 2);
  const changeFont = (classes, currentIndex) => {
    for (var i = 0, len = classes.length; i < len; i++) {
      if (i === currentIndex) {
        main.addClass(classes[i]);
      } else {
        main.removeClass(classes[i]);
      }
    }

    if (currentIndex === 0) {
      downButton.addClass('disabled');
    } else {
      downButton.removeClass('disabled');
    }

    if (currentIndex === classes.length - 1) {
      upButton.addClass('disabled');
    } else {
      upButton.removeClass('disabled');
    }

    actions['report-states']();
  };
  const statePatterns = {
    'up': (content, index) => {
      const max = content.length - 1;
      return (index + 1 <= max) ? index + 1 : index;
    },
    'down': (content, index) => {
      return (index - 1 > 0) ? index - 1 : 0;
    },
    'reset': (content, index) => {
      return defaultIndex;
    }
  };
  const returnState = (content, currentIndex) => {
    return content;
  };

  const settings = {
    'content': sizes,
    'defaultIndex': defaultIndex,
    'statePatterns': statePatterns,
    'returnState': returnState,
    'changeAlgorithm': changeFont
  };

  const fontStateMachine = new ContentStateMachine(settings);

  fontStateMachine.next('reset');

  upButton.on('click', () => {
    actions['trigger-up']();
  });

  resetButton.on('click', () => {
    actions['trigger-reset']();
  });

  downButton.on('click', () => {
    actions['trigger-down']();
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

用作导航状态机...

```
import { ContentStateMachine } from '/scripts/presentation/_content-state-machine.js';

$(document).ready(() => {

  $('.notes').load('/templates/cards.html', function() {
    let slideStateMachine;

    const nextButton = $('.next');
    const previousButton = $('.previous');

    const channel = new BroadcastChannel('le-slides-position');
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
    channel.onmessage = (triggerAction) => {
      actions[triggerAction.data]();
    };

    let cardData = [];
    let cardTitles = [];

    $.getJSON('/data/card-data.json')
    .done((data) => {
      cardData = data;
    })
    .fail((data) => {
      console.log('fail', data);
      if (data.status!==200) {
        const error = $('<div/>').text('Error loading JSON file');
        content.append(error);
      }
    })
    .always(() => {
      if (cardData.length > 0) {
        initTitles();      
      }
    });

    function initTitles() {
      for (let i = 0, len = cardData.length; i < len; i++) {
        cardTitles.push(cardData[i].id);
      }

      init();
    }

    function init() {
      const changeCurrentCard = (cards, currentIndex) => {
        const title = cards[currentIndex];
        const currentCard = $(`.note[card="${title}"]`);
        const previousTitle = (currentIndex - 1 < 0) 
          ? '' : cardTitles[currentIndex - 1];
        const nextTitle = (currentIndex + 1 > maxCards - 1) 
          ? '' : cardTitles[currentIndex + 1];
        const keep = [title];

        currentCard.addClass('slide');
        currentCard.attr('style', 'left:0;');

        if (previousTitle.length > 0) {
          keep.push(previousTitle);

          previousButton.removeClass('disabled');
          $(`[card="${previousTitle}"]`)
            .attr('style', 'left:-100%;')
            .removeClass('slide');
        } else {
          previousButton.addClass('disabled');
        }

        if (nextTitle.length > 0) {
          keep.push(nextTitle);

          nextButton.removeClass('disabled');
          $(`[card="${nextTitle}"]`)
            .attr('style', 'left:100%;')
            .removeClass('slide');
        } else {
          nextButton.addClass('disabled');
        }

        $('.n').text(currentIndex + 1);

        actions['report-states'](currentIndex);

        for (let i = 0, len = cards.length; i < len; i++) {
          const element = $(`[card="${cards[i]}"`);
          if (!keep.includes(cards[i])) {
            element.attr('style', 'display:none;');
          }
        }
      };

      const statePatterns = {
        'previous': (content, index) => {
          return (index - 1 > 0) ? index - 1 : 0;
        },
        'next': (content, index) => {
          const max = content.length - 1;
          return (index + 1 <= max) ? index + 1 : index;
        },
        'reset': (content, index) => {
          return 0;
        }
      };

      const returnState = (content, currentIndex) => {
        return content;
      };

      const settings = {
        'content': cardTitles,
        'defaultIndex': 0,
        'statePatterns': statePatterns,
        'returnState': returnState,
        'changeAlgorithm': changeCurrentCard
      };

      const maxCards = cardTitles.length;
      $('.max').text(maxCards);

      slideStateMachine = new ContentStateMachine(settings);

      slideStateMachine.next('reset');

      nextButton.on('click', (event) => {
        actions['trigger-next']();
      });

      previousButton.on('click', (event) => {
        actions['trigger-previous']();
      });
    }
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

经过大量的研究，我发现 JavaScript 生成器的实际例子很少。我想找到使用它们的方法。在与他们进行了一次异步 JavaScript 对话( [JavaScript 享受你的眼泪](https://dev.to/rfornal/javascript-enjoys-your-tears-4el))后，我发现了一个状态机来帮助在幻灯片中定位和管理演示端的字体大小，这是一个很好的例子。

我可以用其他方式管理状态吗？当然可以。但是，我从上面的代码中并没有学到多少东西。