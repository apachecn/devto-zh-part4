# 迷你反应战术-反应堆

> 原文：<https://dev.to/furkangulsen/mini-react-tactics-reactdom-44fe>

通常，这是 index.js 文档中的代码。

```
import ReactDOM from 'react-dom';

// ... codes

ReactDOM.render( app, document.getElementById( 'root' ) ); 
```

Enter fullscreen mode Exit fullscreen mode

但是这段代码也可以用这种方式编写，不需要使用 ReactDOM。

```
import {render} from 'react-dom';

// ... codes

render(<App />,document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode