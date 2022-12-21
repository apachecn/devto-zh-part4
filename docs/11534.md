# 反应钩 vs 流星反应 var

> 原文：<https://dev.to/saulsilver/react-hooks-and-meteor-reactive-var-1dcj>

我一直在大量使用 MeteorJS，所以 Reactive-var 几乎已经成为我的第二天性，我假设你已经很好地理解了如何在 Meteor 中运行模板化和反应性 var。与此同时，我最近更频繁地使用 React。现在，在我写这篇文章的时候，React `Hooks`已经发布了大约 5 个月了。我在读 Hooks docs，然后看了一个由在 React 工作的 [MPJ](https://twitter.com/mpjme) 和[丹](https://twitter.com/dan_abramov)制作的[视频](https://www.youtube.com/watch?v=G-aO5hzo1aw)。我推荐视频(30 分钟。如果以 2 倍的速度观看，时间会更长😉)

所以让我们跳到代码，看看不同/相似的反应`Hooks`与 Meteor 的`Reactive-var`相比如何工作。

流星版的一个简单的无功 var 创建/获取/设置。

```
import { Template } from 'meteor/templating';
import { ReactiveVar } from 'meteor/reactive-var';

import './my_cool_text_field.html';

const template = Template.my_cool_text_field;

template.onCreated(() => {
  const instance = Template.instance();
  // Suppose we have a text input field called "title"
  instance.titleVar = new ReactiveVar('');
});

template.helpers({
  getTitle() {
    return Template.instance().titleVar.get();
  }
});

template.events({
  'click .change-title': () => Template.instance().titleVar.set(newTitle)
}); 
```

Enter fullscreen mode Exit fullscreen mode

React 版本相同的流星进程

```
import React, { useState } from 'react';

export default MyCoolTextField = () => {
  // `useState()` returns the variable and a setter function for the variable
  // We set the default value of `title` as a parameter to `useState()`
  const [title, setTitle] = useState('');

  const handleChange = event => setTitle(event.target.value);

  return (
    <div>
      <input
        className="change-title"
        name="title"
        type="text"
        value={title}
        onChange={handleChange}
      />
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

还要注意，我还没有为 Meteor 片段放置模板 HTML。