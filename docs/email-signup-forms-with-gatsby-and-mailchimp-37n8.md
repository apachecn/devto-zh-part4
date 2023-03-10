# 盖茨比和 Mailchimp 的电子邮件注册表单

> 原文：<https://dev.to/thetrevorharmon/email-signup-forms-with-gatsby-and-mailchimp-37n8>

如果你正在使用 Gatsby 来充实你的博客，那么你可能需要一种简单的方式来让其他人注册你的帖子。虽然有些人选择通过 RSS 或 Twitter 提供他们的内容，但确保你的内容出现在观众眼前的最可靠的方法之一是建立一个电子邮件列表。多亏了 benjaminhoffman(和其他贡献者)，在 Gatsby 内部建立一个邮件列表注册变得轻而易举。在文章中，我将一步一步地指导您(并提供示例代码！)为 Gatsby 创建一个 Mailchimp 注册表单。

*注意:本文假设你已经[注册了一个 Mailchimp 账户](https://login.mailchimp.com/signup/)。如果你还没有，你应该去做那件事，然后回到这里。*

## 找到你的 Mailchimp 表单动作

这一步的目标是从与 Mailchimp 列表相关联的嵌入式表单中获取表单操作。继续执行以下操作:

登录您的 Mailchimp 仪表盘，点击“观众”。

[![Mailchimp Form - 1\. Mailchimp Dashboard](img/0a6294d5a14c59e0632a6283048b2dd3.png)](//images.ctfassets.net/ovu8iofw2r00/gwUMehmUA5F5S6xIqzEmZ/02ff2ee548b9fc295fb526a08002d9ea/Mailchimp_Form_-_1._Mailchimp_Dashboard.png)

向下滚动并查找“创建登录页面”。

[![Mailchimp Form - 2\. Create a Landing Page](img/c9da6fa5c89303a88eff96e34b28b99b.png)](//images.ctfassets.net/ovu8iofw2r00/2H9I7mcfsjvW786s3smSxM/b979b6cab94056b02a32dfd1970887bf/Mailchimp_Form_-_2._Create_a_Landing_Page.png)

您将看到一个显示“登录页面”的模态，您可以忽略它。

[![Mailchimp Form - 3\. Landing Page Modal](img/b47307be0cc2ae8421bf7af81d98b9e8.png)](//images.ctfassets.net/ovu8iofw2r00/5xP43XE0YI894sFLdXLtzh/823bb4919b015a827884bc50a5788c18/Mailchimp_Form_-_3._Landing_Page_Modal.png)

点击“注册表单”。

[![Mailchimp Form - 4\. Audience Page (Click on Signup Forms)](img/dd1a17ed63d9f617233ccedda9f08356.png)](//images.ctfassets.net/ovu8iofw2r00/2mCqGGq4VNOfVHSOgDEeeD/37230ab5725a3d1972c0158d9c997af8/Mailchimp_Form_-_4._Audience_Page__Click_on_Signup_Forms_.png)

点击“嵌入式表单”

[![Mailchimp Form - 5\. Signup Forms Tab (Click on Embedded Forms)](img/221e0be59fcfbd414f9f98425f8ac341.png)](//images.ctfassets.net/ovu8iofw2r00/3BKmH21MZS4dwTMLbnPVnP/3f7509891f8c8c1411906588cac51758/Mailchimp_Form_-_5._Signup_Forms_Tab__Click_on_Embedded_Forms_.png)

向下滚动直到可以看到表单的代码，然后滚动表单寻找 post 操作。它看起来会像这样:

```
https://yourDomainHere.us18.list-manage.com/subscribe/post?u=1512315231251&amp;id=0asd21t12e1 
```

[![Mailchimp Form - 6\. Embedded Form (Highlighted Post Action)](img/6e1b4465ccec44b496eae4012ddda681.png)](//images.ctfassets.net/ovu8iofw2r00/3wZc3mm3sJe7GWVjw0mAts/77d95e026d5c51413115ddfef9c30c6a/Mailchimp_Form_-_6._Embedded_Form__Highlighted_Post_Action_.png)

复制 post 操作并将其粘贴到某个地方以便安全保管。

## 安装插件

安装插件，让它在你的机器上工作:

```
yarn add gatsby-plugin-mailchimp 
```

## 将插件添加到 Gatsby 配置文件中

在 gatsby-config 文件中，添加带有 Mailchimp 端点的插件。您之前复制的字符串是`endpoint`的值。

```
{
  resolve: 'gatsby-plugin-mailchimp',
  options: {
    endpoint: 'THE FORM ACTION WE JUST COPIED',
  },
}, 
```

## 创建组件

为了有一个功能注册表单，我们需要一个组件！我已经为你写了一个简单的开始。下面是代码:

```
import React, { useState } from 'react';
import * as styles from './EmailListForm.module.scss';

const EmailListForm: React.FunctionComponent<{}> = () => {

  const [email, setEmail] = useState('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
  };

  const handleEmailChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setEmail(event.currentTarget.value);
  };

  return (
    <form onSubmit={handleSubmit} className={styles.EmailListForm}>
      <h2>Subscribe to my email list!</h2>
      <div className={styles.Wrapper}>
        <input
          placeholder="Email address"
          name="email"
          type="text"
          onChange={handleEmailChange}
        />
        <button type="submit">Subscribe</button>
      </div>
    </form>
  );
};

export default EmailListForm; 
```

现在，表单看起来像这样:

[![Mailchimp Form - 7\. Signup Form (before styling)](img/9f8decd82789d753aeb89ce584f429fb.png)](//images.ctfassets.net/ovu8iofw2r00/3BFHH9JeDvvR2z2ZETlPAG/98a9fc37d70cdae66e767f8a5d986635/Mailchimp_Form_-_7._Signup_Form__before_styling_.png)

看起来有点平淡。让我们添加一些 CSS 使它看起来更有趣:

```
.EmailListForm {
  display: flex;
  flex-direction: column;

  background: #f2f2f2;
  color: #2a2a2a;

  font-family: -apple-system, Helvetica, Arial, sans-serif;
  padding: 2rem;

  h2 {
    margin-top: 0;
    margin-bottom: 1rem;
  }

  .Wrapper {
    display: flex;
    flex-direction: row;
  }

  input {
    color: #2a2a2a;
    width: 100%;
    border: none;
  }

  button,
  input {
    padding: 1rem 1.5rem;
  }

  button {
    display: inline-block;

    border: none;
    background-image: none;
    background-color: #DD0505;
    color: white;

    letter-spacing: 1px;
    transition: all 0.1s linear;

    &:hover {
      cursor: pointer;
      background: darken(#DD0505, 15%);
    }    
  }
} 
```

[![Mailchimp Form - 8\. Signup Form (after styling)](img/cac76ef9247a3aa8dd2095ae70baf2b4.png)](//images.ctfassets.net/ovu8iofw2r00/3aLEskOcsNa9uqxutIlKz/9885f8f0d0fbe4b04d200f089af6e5cb/Mailchimp_Form_-_8._Signup_Form__after_styling_.png)

完美！

## 类型定义

在撰写本文时，还没有关于`gatsby-plugin-mailchimp`的类型定义。好消息是我已经写了一些，你可以偷我的作品。如果你正在使用 typescript，创建`gatsby-plugin-mailchimp.d.ts`并使用下面的代码:

```
declare module 'gatsby-plugin-mailchimp' {
  import * as React from 'react';

  type MailchimpResult = 'success' | 'error';

  export interface MailchimpResponse {
    result: MailchimpResult;
    msg: string;
  }

  export interface MailchimpFields {
    [key: string]: string
  }

  function addToMailchimp(email: String, listFields?: MailchimpFields): Promise<MailchimpResponse>;
  export default addToMailchimp;
} 
```

## 开始使用插件

这个插件的伟大之处在于它使用起来非常简单。你只要导入函数:

```
import addToMailchimp from 'gatsby-plugin-mailchimp'; 
```

你要去比赛了！让我们将`addToMailchimp`添加到表单提交处理程序:

```
 const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    addToMailchimp(email)
      .then((data) => {
        alert(data.result);
      })
      .catch((error: Error) => {
        // Errors in here are client side
        // Mailchimp always returns a 200
      });
  }; 
```

在这个例子中，`addToMailchimp`返回一个您需要处理的承诺。只有当你根本没有联系到 Mailchimp 时，它才会进入承诺的`catch`。如果你到达 Mailchimp 但是遇到一个错误，他们将返回一个 200，由你来查看`data.result`的内部，看看它是成功还是失败。

现在我们已经把所有东西都连接好了，让我们来测试一下:

[![Mailchimp Form - 9\. Subscribe In Action (GIF)](img/db4d291968093b48748fe00e3677fa4a.png)](//images.ctfassets.net/ovu8iofw2r00/3UGjhFZjHFwdtjb7P1uRzK/5fd3ba81d7aacd1fc622a7f0d6ef9601/Mailchimp_Form_-_9._Subscribe_In_Action__GIF_.gif)

## 结论

这是一个简单的单列表注册表单的例子。这并没有全面地说明插件能做什么-[阅读文档](https://github.com/benjaminhoffman/gatsby-plugin-mailchimp),看看如何使用多个列表，或者了解关于插件的更多详细信息。对于完成的组件，查看[要点](https://gist.github.com/tdharmon/6f308dc9f97aaa050f6e1424dce0890d)。