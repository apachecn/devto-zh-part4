# 不知情:怀旧的反应和预测的简单形式

> 原文：<https://dev.to/chrisfrank/uninformed-nostalgically-simple-forms-for-react-preact-4mgb>

Rails 中的表单自动做了两件有用的事情，这是我在其他框架中工作时所错过的:

1.  他们无需重新加载页面即可提交数据。
2.  它们在提交时禁用自己，这样您就不会因为双击而意外地提交数据两次。

这些都是很棒的默认值！在 2019 年的一个 web 应用中，我应该不得不编写代码来*禁用*这种行为——但我发现自己在几个 React 项目中从头开始重新实现了它。

我在不知情的情况下写了[来将这些默认设置引入 React 生态系统。下面是如何在一个 app 中使用不知情:](https://github.com/chrisfrank/uninformed) 

```
import { Form } from 'uninformed';
import React from 'react';

const SignupForm = props => (
  <Form action="/api/signups" onSuccess={props.handleSuccess}>
    <input type="email" name="email" required />
    <input type="submit" value="Sign Up" />
  </Form>
) 
```

就是这样！没有`onChange`处理程序，没有`Input`组件，只是一个稍微增强的 HTML 表单，它在提交时禁用自己，通过`XMLHttpRequest`向服务器发送数据，并在服务器响应后重新启用自己。当您需要更多功能时——用于设置请求头、运行输入验证等——Uninformed 允许您通过将函数作为道具传递来定制其行为。更多信息参见[自述文件](https://github.com/chrisfrank/uninformed)。

不知情是全新的，我很想听听你对如何改善它的想法！请随时提交[问题](https://github.com/chrisfrank/uninformed/issues)、[拉动请求](https://github.com/chrisfrank/uninformed/pulls)，或者在下面的评论中提问。

(封面图片由 [Kelly Sikkema](https://unsplash.com/photos/tQQ4BwN_UFs) 通过 Unsplash 拍摄)