# 掌握:专注于内心

> 原文：<https://dev.to/ignore_you/mastering-focus-within-38e5>

悬停下拉模式在界面中已经使用了很长时间。大多数时候，这些下降并不完全可能发生。例如，如果你通过键盘导航，你就没有机会进入这个下拉菜单并选择隐藏在其中的东西。

但是当`:focus-within`登陆浏览器时，一切都变了。根据**caniuse.com**的说法，这段代码足以让 81%的用户开心一点:

```
<div class="dropdown" tabindex="0">
  <p class="dropdown__title">This is dropdown</p>
  <div class="dropdown__wrapper">
    <a href="#">Some hidden link</a>
  </div>
</div> 
```

```
.dropdown {
  position: relative;
}
.dropdown__wrapper {
  width: 0;
  height: 0;
  overflow: hidden;
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
}
.dropdown:hover .dropdown__wrapper,
.dropdown:focus-within .dropdown__wrapper {
  width: 100%;
  height: auto;
  overflow: visible;
} 
```

请注意，您必须将`tabindex="0"`添加到 dropdown 容器中，这样它才能成为焦点。

所以，我们结束了。但是那将近 19%的浏览器不支持`:focus-within`呢？Javascript 所有的东西！

```
// so here is module pattern begins
;(function () {
  // get all of dropdowns on page and define active class
  const dropdowns = Array.from(document.querySelectorAll('.dropdown'))
  const dropdownActiveClass = 'dropdown--active'

  // add event listeners to focusin and focusout to our dropdowns
  dropdowns.forEach(dropdown => {
    dropdown.addEventListener('focusin', focusinListener)
    dropdown.addEventListener('focusout', focusoutListener)
  })

  // if focus is inside dropdown, add active class
  function focusinListener (event) {
    event.target.closest('.dropdown').classList.add(dropdownActiveClass)
  }

  // if focused element is not dropdown, remove active class from all dropdowns
  function focusoutListener (event) {
    if (!document.activeElement.classList.contains('dropdown')) {
      dropdowns.forEach(dropdown => {
        dropdown.classList.remove(dropdownActiveClass)
      })
    }
  }
}()) 
```

而且我们还要更新 CSS:

```
.dropdown:hover .dropdown__wrapper,
.dropdown:focus-within .dropdown__wrapper,
.dropdown--active .dropdown__wrapper {
  width: 100%;
  height: auto;
  overflow: visible;
} 
```

然后享受这个可访问的下拉菜单！要播放的完整演示(使用您的 Tab 键):

[https://codepen.io/twhite96/embed/BgJEVy?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/BgJEVy?height=600&default-tab=result&embed-version=2)