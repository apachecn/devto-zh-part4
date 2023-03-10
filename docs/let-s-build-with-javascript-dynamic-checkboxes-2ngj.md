# 让我们构建:使用 JavaScript–动态复选框

> 原文：<https://dev.to/justalever/let-s-build-with-javascript-dynamic-checkboxes-2ngj>

欢迎来到我的“让我们构建:用 JavaScript”系列的另一部分。这个视频教程教你如何使用普通 JavaScript 制作一个动态 HTML 复选框集合。

正在进行的 Let's Build: With JavaScript 系列是我编写的一系列教程，旨在为该语言的新手或希望最终学习核心组件的人提供一个解决常见问题的全新视角。在我的日常工作中，我会面临各种需要 JavaScript 来解决的复杂问题和场景。这些问题最终激发了这些教程和未来的教程。

[查看密码本](https://codepen.io/webcrunchblog/pen/mdbWWJP)

### 入门

[https://www.youtube.com/embed/znxoeUbBwi4](https://www.youtube.com/embed/znxoeUbBwi4)

在 Dropbox、Asana、Google Drive 等应用程序中，通常会有一个文件/任务/等列表。每个列表项通常允许您独立或批量执行某些操作。在这些模式中，我经常看到允许您缩小特定列表项上的操作的复选框。有时，您可能希望对几个操作执行批量操作。这样做会变得更加复杂，但这是非常可能的。

我将在本教程中解决其中的一些模式，包括:

*   一次选中所有复选框
*   按住 Shift 键并单击以选择多个复选框
*   一次清除所有复选框选择
*   向选中的项目添加替代状态

这一切都发生在不到 90 行的 JavaScript 代码中。

### HTML

HTML 是带有一些类的基本标记，用于通过 JavaScript 和 CSS 定位元素。

```
<div class="container">
<h1 class="h1">Dynamic Checkboxes with JavaScript</h1>
  <button class="js-clear">Clear <span></span> checked</button>
<table class="table">
  <thead>
    <tr>
      <th align="left"><input type="checkbox" class="js-select-all" />
      </th>
      <th align="left">Publish?</th>
      <th align="left">Author</th>
      <th align="left">Created at</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>
        <div class="published">
          <input type="checkbox" id="1"/>
          <label class="title" for="1">My first blog post</label>
        </div>
      </td>
      <td>Andy</td>
      <td>August 23, 2019</td>
    </tr>
    <tr>
      <td>2</td>
      <td>
        <div class="published">
          <input type="checkbox" id="2"/>
          <label class="title" for="2">My second blog post</label>
        </div>
      </td>
      <td>Randy</td>
      <td>August 22, 2019</td>
    </tr>
    <tr>
      <td>3</td>
      <td>
        <div class="published">
          <input type="checkbox" id="3" />
          <label class="title" for="3"> My third blog post</label>
        </div>
      </td>
      <td>John</td>
      <td>August 21, 2019</td>
    </tr>
    <tr>
      <td>4</td>
      <td>
        <div class="published">
          <input type="checkbox" id="4"/>
          <label class="title" for="4">My fourth blog post</label>
        </div>
      </td>
      <td>Jane</td>
      <td>August 20, 2019</td>
    </tr>
    <tr>
      <td>5</td>
      <td>
        <div class="published">
          <input type="checkbox" id="5"/>
          <label class="title" for="5">My fifth blog post</label>
        </div>
      </td>
      <td>Ryan</td>
      <td>August 19, 2019</td>
    </tr>
    <tr>
      <td>6</td>
      <td>
        <div class="published">
          <input type="checkbox" id="6"/>
          <label class="title" for="6">My sixth blog post</label>
        </div>
      </td>
      <td>Nicole</td>
      <td>August 18, 2019</td>
    </tr>
  </tbody>
</table>
</div><div class="container">
<h1 class="h1">Dynamic Checkboxes with JavaScript</h1>
  <button class="js-clear">Clear <span></span> checked</button>
<table class="table">
  <thead>
    <tr>
      <th align="left"><input type="checkbox" class="js-select-all" />
      </th>
      <th align="left">Publish?</th>
      <th align="left">Author</th>
      <th align="left">Created at</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>
        <div class="published">
          <input type="checkbox" id="1"/>
          <label class="title" for="1">My first blog post</label>
        </div>
      </td>
      <td>Andy</td>
      <td>August 23, 2019</td>
    </tr>
    <tr>
      <td>2</td>
      <td>
        <div class="published">
          <input type="checkbox" id="2"/>
          <label class="title" for="2">My second blog post</label>
        </div>
      </td>
      <td>Randy</td>
      <td>August 22, 2019</td>
    </tr>
    <tr>
      <td>3</td>
      <td>
        <div class="published">
          <input type="checkbox" id="3" />
          <label class="title" for="3"> My third blog post</label>
        </div>
      </td>
      <td>John</td>
      <td>August 21, 2019</td>
    </tr>
    <tr>
      <td>4</td>
      <td>
        <div class="published">
          <input type="checkbox" id="4"/>
          <label class="title" for="4">My fourth blog post</label>
        </div>
      </td>
      <td>Jane</td>
      <td>August 20, 2019</td>
    </tr>
    <tr>
      <td>5</td>
      <td>
        <div class="published">
          <input type="checkbox" id="5"/>
          <label class="title" for="5">My fifth blog post</label>
        </div>
      </td>
      <td>Ryan</td>
      <td>August 19, 2019</td>
    </tr>
    <tr>
      <td>6</td>
      <td>
        <div class="published">
          <input type="checkbox" id="6"/>
          <label class="title" for="6">My sixth blog post</label>
        </div>
      </td>
      <td>Nicole</td>
      <td>August 18, 2019</td>
    </tr>
  </tbody>
</table>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### CSS

CSS 是使我们的表格看起来更好看的基本标记。我还为那些处于选中状态的项目添加了一些自定义样式。

```
@import url("https://fonts.googleapis.com/css?family=Montserrat:400,400i,700");

body {
  background: peachpuff;
  font-family: 'Montserrat';
}

.container {
  max-width: 700px;
  margin: 2rem auto;
  background: #fff;
  padding: 40px;
  border-radius: 10px;
}

.table {
  width: 100%;
  margin-bottom: 1rem;
  color: #212529;
  border-collapse: collapse;
}

.table thead th {
  border-bottom: 2px solid #dee2e6;
  border-top: 0;
  vertical-align: bottom;
  user-select: none;
}

.table td, 
.table th {
  padding: 0.75rem;
  border-top: 1px solid #dee2e6;
  user-select: none;
}

td.title {
  font-family: 'Georgia', serif;
  font-style: italic;
}

button {
  background: #f4f4f4;
  padding: 10px 16px;
  margin-bottom: 10px;
  border-radius: 3px;
  appearance: none;
  border: 0;
  border-radius: 8px;
  line-height: normal;

  &:hover {
    background: #f0f0f0;
    cursor: pointer;
  }
}

.published {
  display: flex;
  align-items: center;

  label {
    margin-left: 16px;
    font-family: "Georgia", serif;
    font-size: 16px;
    font-style: italic;
  }
}

input[type=checkbox]:checked + label {
  text-decoration: line-through;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 最后，JavaScript

我对 JavaScript 使用面向对象的方法。一切都存在于我创建的描述特性的全局对象中。有很多其他的方法来编写你的 JavaScript 代码，所以我不希望你认为这是唯一的方法。我发现这种方式在可重用性方面对我很有帮助。我可以很容易地在全局对象中的函数和方法之间共享逻辑。这也适用于全局对象，这意味着与其他地方编写的 JavaScript 冲突更少。

```
const DynamicCheckboxes = {
  checkboxes: document.querySelectorAll('.table td input[type="checkbox"]'),
  selectAllTarget: document.querySelector('.js-select-all'),
  clearBtn: document.querySelector('.js-clear'),

  initialize() {
    this.shiftToSelect();
    this.selectAll();
    this.clearChecked();
    this.showRemoveCheckedButton();
  },

  shiftToSelect() {
    const checkboxes = this.checkboxes;
    let lastChecked;

    function handleCheck(event) {
      // Check if shift key is down and check if checkbox is checked
      let inBetween = false;
      if (event.shiftKey && this.checked) {
        checkboxes.forEach(checkbox => {
          if (checkbox === this || checkbox === lastChecked) {
            inBetween = !inBetween;
          }

          if (inBetween) {
            checkbox.checked = true;
          }
        });
      }
      lastChecked = this;
    }

    checkboxes.forEach(checkbox => checkbox.addEventListener('click', handleCheck, false));
  },

  selectAll() {
    function handleSelectAll(event) {
      this.checkboxes.forEach(checkbox => {
        checkbox.checked ? (checkbox.checked = false) : (checkbox.checked = true)
      })
    }

    this.selectAllTarget.addEventListener('click', handleSelectAll.bind(this), false)
  },

  showRemoveCheckedButton() {
    this.clearBtnDisplay('none')
    document.addEventListener('change', this.showBtn.bind(this))
  },

  showBtn(event) {
    const checkboxesChecked = document.querySelectorAll('.table td input[type=checkbox]:checked').length

    if (checkboxesChecked > 0) {
      this.clearBtn.querySelector('span').textContent = checkboxesChecked;
      this.clearBtnDisplay('block');
    } else {
      this.clearBtn.querySelector('span').textContent = '';
      this.clearBtnDisplay('none');
    }
  },

  clearBtnDisplay(state) {
    this.clearBtn.style.display = state;
  },

  clearChecked() {
    this.clearBtn.addEventListener('click', removeChecked.bind(this), false);

    function removeChecked() {
      this.checkboxes.forEach(checkbox => (checkbox.checked = false));
      this.selectAllTarget.checked = false;
      this.clearBtnDisplay('none');
    }
  }

};

DynamicCheckboxes.initialize(); 
```

Enter fullscreen mode Exit fullscreen mode

我建议观看视频，看看这是如何实现的，并听听我的想法，因为我正在把所有这些放在一起。乍一看，代码看起来有点复杂，但是从理论上讲，`DynamicCheckboxes`对象中的每个函数都是我们首先想要添加的功能的构建块。一些功能共享逻辑，而其他功能独立运行。你大概可以猜到，像这样的规模是相当困难的。这可能就是为什么框架的兴起是目前的热门话题。

### 关东西出去

希望你在这里学到了一点东西！我是以一种无知的态度接触 JavaScript 的。我想用它来构建一切，但后来意识到我需要后退一步，专注于理解我们作为开发人员日常遇到的问题的较小方面。总会有更好的方法来编写代码，但是我发现首先解决问题可以让你重新审视你已经完成的东西，然后再重构成更清晰、更有性能的东西。有时候，编写更少的代码实际上会使内容更难阅读，所以如果你看到被最大程度重构的代码，你会自责不已。

#### 到目前为止的级数

*   [让我们构建:使用 JavaScript–DIY 下拉菜单和响应菜单](https://web-crunch.com/lets-build-with-javascript-diy-dropdowns-responsive-menus/)
*   [让我们用 JavaScript 构建——带 Cookies 的广播栏](https://web-crunch.com/lets-build-with-javascript-broadcast-bar-cookies/)
*   [让我们用 JavaScript 构建:粘性导航](https://web-crunch.com/lets-build-with-javascript-sticky-nav/)
*   [让我们构建:用 JavaScript 动态标签](https://web-crunch.com/how-to-create-tabs-with-vanilla-javascript)
*   [让我们构建:用 JavaScript–Modals](https://web-crunch.com/lets-build-with-javascript-how-to-code-modal-vanilla-javascript/)
*   [让我们构建:使用 JavaScript–html 5 视频播放器](https://web-crunch.com/lets-build-with-javascript-html5-video-player/)
*   [让我们用 JavaScript 构建:手风琴](https://web-crunch.com/how-to-create-accordion-vanilla-javascript/)
*   [让我们构建:用 JavaScript 骨架屏幕效果](https://dev.tohow-to-create-skeleton-screen-loading-effect)
*   [如何编写一个非画布菜单——让我们用 JavaScript 来构建吧](https://web-crunch.com/off-canvas-menu)
*   [显示更多–使用 JavaScript 切换显示更少](https://dev.to/justalever/how-to-code-a-show-more--show-less-toggle-with-javascript-3c7m)
*   [如何通过 JavaScript 使用本地存储](https://web-crunch.com/how-to-use-local-storage-with-javascript)

帖子[让我们用 JavaScript 构建:动态复选框](https://web-crunch.com/javascript-dynamic-checkboxes/)首先出现在[网站](https://web-crunch.com)上。