# #CodepenChallenge 导航:反应灵敏的玻璃导航栏

> 原文：<https://dev.to/takaneichinose/codepenchallenge-navigation-responsive-glassy-navbar-1olg>

移动优先的响应导航菜单设计理念。

链接的结构与我的分叉方式完全一样。就像，我只是添加了一些类，和一些元素，比如图标，或者文本本身。

菜单和子菜单是用递归函数创建的。

* * *

# 我是怎么做到的

### 造型

首先，使用 HTML 中的默认布局，我尝试设计导航菜单的样式。样式没有决定性的部分，它只是你想要的导航外观。

### 渲染

老实说，因为我真的想使用 GSAP，我蛮力这个。因此，如果有人对此有更好的代码，请在下面评论。

```
// This is the recursive function that will create the elements
createMenuJSX(menu = this.props.menu) {
  // The array that will be rendered
  let link = [];

  for (let i in menu) {
    let m  = menu[i];
    let ic = <i className="cpc-icon cpc-hidden fas fa-caret-down"></i>; 
    if (typeof m.icon !== 'undefined') {
      ic = <i className={'cpc-icon ' + m.icon}></i>;
    }

    // if 'menu' object is undefined (if it doesn't have a sub menu),
    // just show content.
    if (typeof m.menu === 'undefined') {
      // Note the useless elements here, this is to maintain balance
      // between the texts and icons. I think there is a better way
      // here, but I ran out of ideas, so I kept it like this.
      link.push(
        <li>
          <a href={m.link}>
            {ic}
            <span>{i}</span>
            <i className="cpc-caret cpc-hidden fas fa-caret-down"></i>
          </a>
        </li>
      );
    } else if (typeof m.menu === 'object') {
      let tmpSubmenu = this.state.submenu;
      let tmpLength  = tmpSubmenu.length;

      // Create a temporary array. This will be used later for
      // rendering, as well as the 'ref' for GSAP animation.
      tmpSubmenu.push({
        'id': m.link,
        'active': false,
        'caret': React.createRef(),
        'sub': React.createRef()
      });

      // The click event handler is here.
      // The caret and sub menu ref is set here. As mentioned earlier
      // I need this to use the GSAP as animation. If I would not use
      // it, I can easily set the class, and will not use brute force
      // in rendering these elements. I can directly put this method
      // as rendering method in render() method.
      link.push(
        <li>
          <a
            href={m.link}
            onClick={this.menuClickEvent.bind(this, tmpLength)}
          >
            {ic}
            <span>{i}</span>
            <i
              className="cpc-caret fas fa-caret-down"
              ref={tmpSubmenu[tmpLength].caret}
            ></i>
          </a>
          <ul className="cpc-sub" ref={tmpSubmenu[tmpLength].sub}>
            {this.createMenuJSX(m.menu)}
          </ul>
        </li>
      );

      this.setState({submenu: tmpSubmenu});
    }
  }

  return link;
}

// I used the created jsxData state to display the elements.
render() {
  return (
    <nav className="cpc-menu">
      <ul className="cpc-main">
        {this.state.jsxData}
      </ul>
    </nav>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

### 改变状态

现在，我们已经呈现了元素，让我们转到点击事件，用插入符号显示菜单的子菜单。

```
menuClickEvent(i) {
  let submenu = this.state.submenu;
  let tmpmenu = submenu[i];
  // This is how you get the element that was set as a 'ref'
  let sub     = tmpmenu.sub.current;
  let caret   = tmpmenu.caret.current;

  if (tmpmenu.active === false) {
    tmpmenu.active = true;

    // GSAP animation for caret. Notice that I used the ref
    // for this one.
    TweenLite.to(caret, 1, {
      transform: 'rotate(180deg)',
      ease: Elastic.easeOut.config(1, 0.3)
    });

    // GSAP animation for sub menu. Notice that I used the ref
    // for this one.
    TweenLite.to(sub, 1, {
      height: sub.scrollHeight,
      visibility: 'visible',
      ease: Elastic.easeOut.config(1, 0.3)
    });
  } else {
    tmpmenu.active = false;

    // GSAP animation for caret. Notice that I used the ref
    // for this one.
    TweenLite.to(caret, 1, {
      transform: 'rotate(0deg)',
      ease: Elastic.easeOut.config(1, 0.3)
    });

    // GSAP animation for sub menu. Notice that I used the ref
    // for this one.
    TweenLite.to(sub, 0.5, {
      height: 0,
      ease: Bounce.easeOut
    }).eventCallback('onComplete', () => {
      // 'eventCallback' will be called after some events was called,
      // like, start, stop, complete, etc. In my case, I used complete.
      TweenLite.to(sub, 0, {
        visibility: 'hidden'
      })
    });
  }

  submenu[i] = tmpmenu;

  this.setState({submenu: submenu});
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是这个菜单和子菜单的工作方式。如果你对此有更好的建议，请在下面评论。

你会注意到我把它编码的很烂。那是因为我的反应还不够好。我还在练习。

# 结论

作为这次挑战的总结，我学到了很多关于 react 的东西，这很有趣。当然，当我编码的时候，我在阅读文档。这是我第一次在 ReactJS 中做递归。实际上，这和通常的递归是一样的。我也想知道更多关于 GSAP 动画，因为我一直在 CSS 只编码动画。

# 资源

资源/库/框架:
[谷歌字体](https://fonts.google.com/specimen/Montserrat):蒙塞拉特
[字体牛逼](https://fontawesome.com/):图标
[ReactJS](https://reactjs.org/) :功能和渲染
[GSAP](https://greensock.com/gsap/) :动画

[https://codepen.io/takaneichinose/embed/abovwoB?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/abovwoB?height=600&default-tab=result&embed-version=2)