# 在 Elixir 和 Phoenix 中加载特定的 JS 文件

> 原文：<https://dev.to/ricardoruwer/load-specific-js-files-in-elixir-and-phoenix-o57>

首先我想说这篇帖子是基于那篇:
[http://codeloveandboards . com/blog/2016/04/26/page-specific-JavaScript-in-phoenix-framework-pt-2/](http://codeloveandboards.com/blog/2016/04/26/page-specific-javascript-in-phoenix-framework-pt-2/)T3】但是我做了一些改动(IMO)简化了一下...

那么，让我们开始吧:

首先，我们可以在文件`assets/js/app.js`中添加代码:

```
// Import specific page views
import './views/init'; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将在`assets/js/views/init.js`中创建这个`init.js`文件，并添加代码:

```
import loadView from './loader';

function handleDOMContentLoaded() {
  const viewName = document.body.dataset.jsViewPath;

  const view = loadView(viewName);
  view.mount();

  window.currentView = view;
}

function handleDocumentUnload() {
  window.currentView.unmount();
}

window.addEventListener('DOMContentLoaded', handleDOMContentLoaded, false);
window.addEventListener('unload', handleDocumentUnload, false); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将创建在`import`中提到的这个`loader.js`文件，因此创建文件`assets/js/views/loader.js`并添加代码:

```
import MainView from './main';

export default function loadView(viewPath) {
  if (!viewPath) return new MainView();

  const ViewClass = require('./' + viewPath);
  return new ViewClass.default();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在这个在`assets/js/views/main.js`中被称为`main.js`的文件的代码是:

```
export default class MainView {
  // It will be executed when the document loads...
  mount() { }

  // It will be executed when the document unloads...
  unmount() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！:)
现在在我们的控制器中，我们可以向视图返回一个新值，通知我们的 js 文件的目录，就像这样:

```
conn
|> assign(:js_view, "posts/index")
|> render("index.html") 
```

Enter fullscreen mode Exit fullscreen mode

现在在我们的布局文件中，可能在`templates/layout/app.html.eex`中，我们可以在`<body>`标签中添加这个代码:

```
<body data-js-view-path="<%= assigns[:js_view] %>"> 
```

Enter fullscreen mode Exit fullscreen mode

准备好了！:)
所以，如果我们在控制器上设置了`js_view`，我们将只尝试获取 JS 文件。

现在，要使用它，您可以在`assets/js/views/posts/index.js`中创建一个文件，并添加如下代码:

```
import MainView from '../main';

export default class View extends MainView {
  mount() {
    // Add your JS code here...
    console.log('UHULL! It works!')

    super.mount();
  }

  unmount() {
    super.unmount();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

фг