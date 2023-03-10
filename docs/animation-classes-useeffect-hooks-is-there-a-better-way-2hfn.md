# 动画类& useEffect 钩子-有更好的方法吗？

> 原文：<https://dev.to/avatarkaleb/animation-classes-useeffect-hooks-is-there-a-better-way-2hfn>

大家好！

## 快速上下文

我最近在做一个功能，包括一点动画，还有一些设计系统类。第一次想把 hooks 引入代码库，决定试一试！

### 挑战

在使用动画淡出时，我遇到了两个挑战:

1.  一个元素会在淡出动画后重新出现，而不需要应用第二个类来隐藏它
2.  当同时应用一个隐藏类和一个动画类时，动画并没有发生——只是消失了:)。

### 解

为了解决这个问题，我使用了一个 useEffect()钩子来设置 animation 类，后面跟着一个延迟 1 秒的 setTimeout，首先完成动画，然后成功隐藏我们正在制作动画的元素。

我利用钩子的返回功能来清除元素上的任何计时器，以防止内存泄漏。

下面你可以看到我写的解决挑战的代码(缩短版)，或者你可以看看这支[码笔](https://codepen.io/avatar-kaleb/pen/voBmzp)。

如果你想看到这个问题，注释掉 useEffect 钩子，你会看到它淡出，然后又重新出现！

`isHidden`道具是从更高的组件传递下来的，它基于点击/点击而改变。

## 代码

### 做出反应

```
export const SomeNavHeader = ({
  title = 'Some Title',
  isHidden
  planId
}) => {
  const TOPBAR_VISIBILITY_CLASSES = {
    hidden: 'hide',
    visible: ''
  }
  const ANIMATION_CLASSES = {
    fadeIn: 'fade-in',
    fadeOut: 'fade-out'
  }

  // set default state to use fade in and visible class
  const [animationClass, setAnimationClass] = useState(ANIMATION_CLASSES.fadeDownAndIn)
  const [topbarNavHiddenClass, setTopbarNavHiddenClass] = useState(TOPBAR_VISIBILITY_CLASSES.visible)

  // this will run everytime isHidden changes
  useEffect(() => {
    // set timer ids to null to help with clean up - null is OK here
    let hiddenClassTimer = null

    if (isHidden) {
      // fade out then hide once animation finishes
      setAnimationClass(ANIMATION_CLASSES.fadeOut)
      hiddenClassTimer = setTimeout(() => {
        setTopbarNavHiddenClass(TOPBAR_VISIBILITY_CLASSES.hidden)
      }, DELAYS_IN_MS.oneSecond)
    } else {
      // show topbar and animate it in
      setAnimationClass(ANIMATION_CLASSES.fadeIn)
      setTopbarNavHiddenClass(TOPBAR_VISIBILITY_CLASSES.visible)
    }

    // return function helps to clean up timeouts if they are happening when component is removed from dom
    return () => {
      clearTimeout(hiddenClassTimer)
    }
  }, [
    isHidden
  ])

  return (
    <header
      className={`some-header-component ${DESIGN_SYS.topBar}  ${
        DESIGN_SYS.color.neutral90
      }  ${animationClass}  ${topbarNavHiddenClass}`}
    >
      <p>{title}</p>
    </header>
  )
} 
```

### Scss

```
.some-header-component {
  &.fade-in {
    animation-duration: 1s;
    animation-name: fadeIn;
  }

  &.fade-out {
    animation-duration: 1s;
    animation-name: fadeOut;
  }

  &.hide {
    display: none;
  }
} 
```

## 有没有更好的办法？

我绝对喜欢反馈或其他关于处理特定挑战的最佳方式的建议！

如果您有任何问题，请告诉我！