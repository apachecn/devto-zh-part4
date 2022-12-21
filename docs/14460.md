# 异步冒险:中止 react-apollo 中的查询和突变

> 原文：<https://dev.to/evilmartians/asynchronous-adventures-aborting-queries-and-mutations-in-react-apollo-4ei2>

*TLDR:如果你只是想取消 *react-apollo* 中的一个查询或突变，你可以跳过一个介绍，直接跳到一个[食谱](#howto)。*

## 为什么我需要在 React Apollo 中取消一个请求？

让我们以一个发送一系列连续请求的接口为例，其中只有最后一个请求是重要的。它可以是一个自动建议输入，也可以是一个每次修改都自动保存的表单。为了正确工作，应用程序必须使用上一个请求的响应并忽略先前的结果(即使先前的请求可能在最后一个请求的结果之后产生结果**)。**

在正常情况下，react-apollo 会自动为您完成。例如，设想在电子商务网站上有一个邮政编码字段。其内容被保存并自动检查，以确定是否可以运送到给定的目的地:

```
import * as React from "react";
import { Mutation } from "react-apollo";
import gql from 'graphql-tag';

const saveZipCode = gql`
  mutation SaveZipCode($input: String) {
    save(input: $input) {
      hasShipping
    }
  }
`;

function ZipCodeField(props) {
  return (
    <Mutation mutation={saveZipCode}>
      {(save, { data }) => (
        <div>
          <input
            onChange={({ target: { value } }) =>
              save({ variables: { input: value } })
            }
          />
          {data.hasShipping && <div>Shipping is available!</div>}          
        </div>
      )}
    </Mutation>
  );
} 
```

在上面的例子中，输入字段的每一次改变都将调用 save 变异，并接收`hasShipping`标志，该标志告知是否可以发货。我们想要的是忽略用户输入代码时发生的所有先前突变的结果。

幸运的是，Apollo 为我们做到了这一点:如果`<Mutation>`组件之前有一个正在进行的突变——一旦新的突变发生，它将被自动取消。

## 去抖突变

对每一个变化都进行突变通常不是一个好主意，因为这会给网络和后端都带来额外的负载。更好的方法是去抖用户的输入，并在用户停止输入后*发出请求。* 

```
// There are plenty of 'debounce' implementations out there. We can use any.
import debounce from "lodash-es/debounce";

// ....

function ZipCodeField(props) {
  const debouncedSave = React.useRef(
    debounce((save, input) => save({ variables: { input } }), 500 )
  );

  return (
    <Mutation mutation={saveZipCode}>
      {(save, { data }) => (
        <div>
          <input
            onChange={({ target: { value } }) => debouncedSave.current(save, value)}
          />
        </div>
        {data.hasShipping && <div>Shipping is available!</div>}          
      )}
    </Mutation>
  );
} 
```

此代码将在最后一次更改后延迟 500 毫秒保存突变。任何中间变化都不会引发突变。

然而，这个解决方案有一个缺陷。如果两个更改事件之间的间隔略大于 500 毫秒，两个突变都将被触发，但 Apollo 将无法在第二个去抖间隔的至少 500 毫秒内取消第一个，因为实际的突变尚未被调用。以下是事件的可能时间表:

*000ms* :第 1 次`onChange`—去抖突变 500ms。

*500ms* :发射第一次变异的请求。

*501 毫秒*:第二次`onChange`—500 毫秒去抖第二次突变(阿波罗不知道第二次请求，因此不能取消第一次)

*600ms* :第一次突变的反应。现在接口用第一个变异的结果更新了，但是输入字段有更多的文本要发送给第二个变异。我们界面的不同部分现在不同步了。

*1000ms* :发射第二次变异的请求(取消第一次请求为时已晚)

*未来某处*:第二次突变反应。现在，该系统再次获得了一致性

在第一次和第二次突变的反应之间有一个间隙，在此期间，我们的界面不同步。输入字段具有在第二次变异中发送的邮政编码，但界面显示的是先前邮政编码的检查结果。这些可能会导致不愉快的 UX，甚至一些严重的竞态错误。

最好(也是最容易)的解决方法之一是在第二个`onChange`事件之后立即手动取消第一个突变。幸运的是，在 Apollo 中有一种方法可以做到这一点，尽管没有很好的文档记录。

## 使用 AbortController API 取消 Apollo 请求

*警告！根据[的说法，这个问题](https://github.com/apollographql/apollo-client/issues/4150)使用中止控制器对 GraphQL 查询不起作用。它对突变有效，但在某些配置中可能有意想不到的副作用。有一个[公关](https://github.com/apollographql/apollo-link/pull/880)正在修复这个问题，还没有合并。*

在其标准配置中，Apollo 使用浏览器的`fetch` API 来处理实际的网络请求，并且可以向其传递任意选项。所以我们可以使用[中止信号](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal)来中止任何突变:

```
// Create abort controller
const controller = new window.AbortController();

// Fire mutation
save({ options: { context: { fetchOptions: { signal: controller.signal } } } });

// ...

// Abort mutation anytime later
controller.abort() 
```

*[AbortController API](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 还在试验阶段，所以如果你关心老浏览器的话，别忘了多填一下。*

## 增强了去抖动和中止先前请求的示例

借助中止信号，我们可以在每个`onChange`取消一个旧的请求，以确保我们总是只显示最后一个请求的结果:

```
function ZipCodeField(props) {
  const abortController = React.useRef();
  const debouncedSave = React.useRef(
    debounce((save, input) => {
      const controller = new window.AbortController();
      abortController.current = controller;

      save({
        variables: { input },
        options: {
          context: { fetchOptions: { signal: controller.signal } }
        }
      });
    }, 500)
  );

  const abortLatest = () =>
    abortController.current && abortController.current.abort();

  return (
    <Mutation mutation={saveZipCode}>
      {(save, { data }) => (
        <div>
          <input
            onChange={({ target: { value } }) => {
              abortLatest();
              debouncedSave.current(save, value);
            }}
          />
          {data.hasShipping && <div>Shipping is available!</div>}          
        </div>
      )}
    </Mutation>
  );
} 
```

在这里，我们为每个突变创建一个`AbortController`，并将其保存到`abortController` ref。现在，当邮政编码改变时，我们可以通过打电话`abortController.current.abort()`来手动取消正在进行的变异

对于像这样的简单情况，定制的 Apollo 链接可能是更好的选择。但是如果你需要对你的请求进行细粒度的控制，中止信号是一个很好的方法。

感谢您的阅读！

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！