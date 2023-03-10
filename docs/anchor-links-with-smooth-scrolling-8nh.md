# 平滑滚动的锚定链接

> 原文：<https://dev.to/piotrpog/anchor-links-with-smooth-scrolling-8nh>

# CSS 解决方案

锚链接将我们带到他们即时链接到的页面部分。我们可以使用单个 CSS 属性:
将这种“传送”替换为平滑滚动的动画

```
html {
  scroll-behavior: smooth;
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这个[不能在 Safari](https://caniuse.com/#search=scroll-behavior) 上运行。真的，safari 是一个新的 IE6。

# Javascript 方案

幸运的是，我们可以用一点 jQuery 代码实现流畅的滚动效果。

下面的代码片段模拟了平稳的锚点击及其各个方面。由于历史 API 的使用，后退和前进浏览器按钮的工作。如果用户在动画仍在运行时开始滚动，它会停止滚动以避免与动画运动“冲突”。

```
 $('a[href^="#"]').on('click', function(event) {
    var hash = '#' + $(this).attr('href').split('#')[1]
    var element = $(hash)
    if (element.length) {
      event.preventDefault();
      history.pushState(hash, undefined, hash)
      $('html, body').animate({scrollTop: element.offset().top}, 500)
    }
  });   

  window.addEventListener('popstate', function(e) {
    if(e.state && e.state.startsWith('#') && $(e.state).length){
      $('html, body').animate({scrollTop: $(e.state).offset().top}, 500)
    }
  });

  $('html, body').on("scroll mousedown wheel DOMMouseScroll mousewheel keyup touchmove", function(){
    $('html, body').stop();
  }); 
```

Enter fullscreen mode Exit fullscreen mode