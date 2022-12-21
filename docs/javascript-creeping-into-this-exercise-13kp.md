# JavaScript:悄悄进入这个(练习)

> 原文：<https://dev.to/valentinogagliardi/javascript-creeping-into-this-exercise-13kp>

对于每个练习，尝试猜测输出。指向什么，更重要的是，为什么？(假设代码在浏览器中运行)。

《出埃及记》#1:

```
function outer() {
  const arrow = () => console.log(this);
  arrow();
}

outer(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#2:

```
function outer() {
  const obj = {
    init: () => console.log(this)
  };

  obj.init();
}

outer(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#3:

```
const obj = {
  nested: {
    init: () => console.log(this)
  }
};

obj.nested.init(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#4:

```
const object = {
  init: function() {
    (() => console.log(this))();
  }
};

object.init(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#5:

```
const object = {
  init: function() {
    setTimeout(function() {
      const arrow = () => console.log(this);
      arrow();
    }, 5000);
  }
};

object.init(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#6:

```
const object = {
  init: function() {
    setTimeout(function() {
      fetch("https://jsonplaceholder.typicode.com/todos/").then(function() {
        const arrow = () => console.log(this);
        arrow();
      });
    }, 5000);
  }
};

object.init(); 
```

Enter fullscreen mode Exit fullscreen mode

《出埃及记》#7:

```
const object = {
  init: function() {
    setTimeout(function() {
      const object = {
        whoIsThis: function() {
          console.log(this);
        }
      };
      object.whoIsThis();
    }, 5000);
  }
};

object.init(); 
```

Enter fullscreen mode Exit fullscreen mode

把你的解决方案写在下面的评论里吧！