# 对弹出窗口使用承诺

> 原文：<https://dev.to/prasannavijayan/use-promise-for-popup-6m2>

有了承诺，你可以做很多事情。今天，我将分享我最近学到的使用 promise for modal 的经验。

如果你不太了解 EmberJs 或者 Promise。请访问 [EmberJS](http://emberjs.com) 和
T3】Promise 来获得你的基础知识

## 为什么要答应？

[https://medium . com/JavaScript-scene/master-the-JavaScript-interview-what-a-promise-27 fc 71 e 77261](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)

#### 先决条件

```
ember install ember-bootstrap
ember generate ember-bootstrap --preprocessor=sass 
```

Enter fullscreen mode Exit fullscreen mode

如果你想用 less 代替 sass，使用`--preprocesor=less`。对于其他配置，请检查[这里](https://www.ember-bootstrap.com/#/getting-started/setup)

#### 步骤

1.  使用创建您的组件

```
ember generate component promise 
```

Enter fullscreen mode Exit fullscreen mode

1.  诺言. js

```
import Component from '@ember/component';

export default Component.extend({

  // It's important to have response undefined instead of boolean
  response : undefined,
  show     : false,

  actions: {
    promiseClicked() {
      new Promise( respond => {
        this.set( 'show', true );

        this.addObserver( 'response', function observer() {
          this.removeObserver( 'response', observer );
          respond( this.get( 'response' ) )
        });

      }).then( proceed => {
        if ( proceed ) {
          console.log( proceed );
        } else {
          console.log( proceed );
        }
        this.setProperties({ 'show': false, 'response': undefined });
      });
    },

    response( bool ) {
      this.set( 'response', bool );
    }
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

确保移除观察器后变量响应设置为未定义。

1.  promise.hbs

```
<BsButton @onClick={{action 'promiseClicked'}}> Promise </BsButton> 
<BsModal
  @open={{show}}
  @onSubmit={{action "response" true}}
  @onHidden={{action "response" false}} as |modal|>
  <modal.header>
    <h4 class="modal-title">
      Custom Dialog
      <div class="badge">2</div>
    </h4>
  </modal.header>
  <modal.body>
    Promise Example?
    Check console when you click "Cancel or Save".
  </modal.body>
  <modal.footer>
    <BsButton @onClick={{action "response" false}}>Cancel</BsButton>
    <BsButton @type="success" @onClick={{action "response" true}}>Save</BsButton>
  </modal.footer> </BsModal> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。谢谢！