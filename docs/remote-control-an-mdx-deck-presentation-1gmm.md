# 远程控制 MDX 甲板演示

> 原文：<https://dev.to/kevnz/remote-control-an-mdx-deck-presentation-1gmm>

# 遥控一个 MDX 甲板演示

MDX Deck 是创建演示幻灯片的一种很好的方式，但是我发现当我离开我的电脑时，能够推进幻灯片是很有用的。我查看这是否是一个已解决的问题，但是我什么也没找到。所以我开始研究如何做到这一点。

## MDX Deck API

首先，我必须确定 MDX Deck 是否提供了一种以编程方式移动幻灯片的方法，如果提供了，移动到哪里。

我的第一次尝试是尝试使用`@mdx-deck/components`中的`Head`组件，但是它不提供对

幸运的是，[定制提供商](https://github.com/jxnblk/mdx-deck/blob/master/docs/advanced.md#custom-provider-component)看起来是个不错的选择。

```
import React from 'react'
// custom provider with content that appears on every slide
const Provider = props => (
  <div>
    {props.children}
    <div
      css={{
        position: 'fixed',
        right: 0,
        bottom: 0,
        margin: 16,
      }}
    >
      Put your name here
    </div>
  </div>
)

export default {
  Provider,
}
// https://codesandbox.io/s/github/jxnblk/mdx-deck/tree/master/examples/provider 
```

最重要的部分是，传递给提供者的道具包括我控制幻灯片所需的`next`、`previous`和`goto`函数。

首先我添加了一个远程控制组件来完成这项工作

```
import React from 'react'

export default ({ next, previous, goto }) => {
  return null
} 
```

然后我将控件添加到提供程序中

```
import React from 'react'
import RemoteControl from './remote-control.js'
const Provider = props => (
  <div>
    {props.children}
    <RemoteControl 
        next={props.next} 
        previous={props.previous}
        goto={props.goto} />
  </div>
)
export default {
  Provider,
} 
```

但是，如何让远程控制组件知道何时调用 next 或 previous 呢？这就是 WebSockets 的用武之地。WebSockets 允许网页从服务器接收消息。所以是时候设置一个支持 WebSockets 的服务器了。在这种情况下，我将使用[哈比神](https://hapijs.com)和 [Nes](https://github.com/hapijs/nes) 。

在设置中，我们需要一个路由来调用下一个命令并支持 WebSocket 订阅。

```
const Hapi = require('@hapi/hapi')
const Nes = require('@hapi/nes')
const init = async () => {
  const server = Hapi.server({
    port: 8080,
  })
  await server.register(Nes)
  server.subscription('/slides/{id}')
  server.route({
    method: 'GET',
    path: '/',
    handler: (request, h) => {
      return 'Hello World!!!'
    },
  })
  server.route({
    method: 'GET',
    path: '/slide/{deck}/next',
    config: {
      handler: (r, h) => {
        r.server.publish(`/slides/${r.params.deck}`, {
          action: 'next',
        })
        return { result: 'SENT' }
      },
    },
  })
  await server.start()
  console.log('Server running on %s', server.info.uri)
}

process.on('unhandledRejection', err => {
  console.log(err)
  process.exit(1)
})

init() 
```

[在 CodeSandbox 上查看](https://codesandbox.io/embed/hapi-nes-next-route-4yeq0)

这将创建一个安装了 Nes 插件的哈比神 web 服务器、`/slides/{id}`端点上的订阅和一个路由`/slide/{deck}/next`,当点击该路由时，它调用订阅并传递一条带有 next 动作的消息。

有了这个设置，就回到甲板上连接到服务器，以获取信息和控制幻灯片。

为了做到这一点，我将使用来自 [@brightleaf/react-hooks](https://brightleaf.dev/hooks) 的 React 钩子，因为它包含了一个`useNes`钩子。

```
import React from 'react'
import { useNes } from '@brightleaf/react-hooks/lib/use-nes'
export default ({ next, previous, goto }) => {

  const { message, error, connecting, connected, client } = useNes(
    'wss://url-goes-here', false
  )

  const handler = function(update, flags) {
    if(update && update.action === 'next') {
      next()
    }
    if(update && update.action === 'previous') {
      previous()
    }
    if(update && update.action === 'goto') {
      goto(update.slide)
    }
  }
  client.subscribe('/slides/slide-deck-1', handler)

  return null
} 
```

它使用一个 react 钩子返回 nes 客户机，然后客户机订阅广播端点，当收到消息时，处理程序检查消息的 action 属性并执行请求的操作。

所以如果你去`https://4yeq0.sse.codesandbox.io/slide/slide-deck-1/next`你会看到甲板前进了一步。

你可以在这里看到幻灯片[和服务器](https://codesandbox.io/embed/mdx-deckbasic-template-5mh86)

现在，移动幻灯片的机制已经整理好了，是时候把可以使用的 UI 放在一起了。

```
import React, { useState } from "react";
import ReactDOM from "react-dom";
import { useGet } from "@brightleaf/react-hooks/lib/use-get";
import {
  Button, Column, Columns, Container, Control, Hero,
  HeroBody, Section, Title, SubTitle
} from "@brightleaf/elements";
import { Form, TextInput } from "react-form-elements";
import "./styles.css";

function App() {
  const [deck, setDeck] = useState("slide-deck-1");
  const { getUrl: getNext } = useGet(
    `https://4yeq0.sse.codesandbox.io/slide/${deck}/next`
  );
  const { getUrl: getPrevious } = useGet(
    `https://4yeq0.sse.codesandbox.io/slide/${deck}/previous`
  );
  return (
    <>
      <Hero isBold isInfo>
        <HeroBody>
          Remote Control
          <SubTitle>Press the buttons to see some magic happen!</SubTitle>
        </HeroBody>
      </Hero>
      <Section className="App">
        <Container>
          <Columns>
            <Column isHalf>
              <Button
                isPrimary
                isLarge
                isFullWidth
                className="is-tall"
                onClick={e => {
                  e.preventDefault();
                  getPrevious(
                    `https://4yeq0.sse.codesandbox.io/slide/${deck}/previous`
                  );
                }}
              >
                &lt;&lt; Previous
              </Button>
            </Column>
            <Column isHalf>
              <Button
                isPrimary
                isLarge
                isFullWidth
                className="is-tall"
                onClick={e => {
                  e.preventDefault();
                  console.log("click next");
                  getNext(
                    `https://4yeq0.sse.codesandbox.io/slide/${deck}/next`
                  );
                }}
              >
                Next &gt;&gt;
              </Button>
            </Column>
          </Columns>
          <hr />
          <Columns>
            <Column isFull>
              <Form
                name="slidepicker"
                onSubmit={data => {
                  setDeck(data.slides);
                }}
              >
                <TextInput
                  className="field control"
                  labelClassName="label is-large"
                  inputClassName="input is-large"
                  name="slides"
                  initialValue=""
                  label="Slide Deck"
                />
                <Control>
                  <Button isInfo>Connect Slide Deck</Button>
                </Control>
              </Form>
            </Column>
          </Columns>
        </Container>
      </Section>
    </>
  );
} 
```

在行动中

[https://www.youtube.com/embed/NtYc1XMkeBQ](https://www.youtube.com/embed/NtYc1XMkeBQ)

除了文章中的 codesandbox 链接，启发这篇文章的代码也可以在 GitHub 上找到。

*   [API 服务器](https://github.com/Kevnz/kpi)
*   [滑梯](https://github.com/Kevnz/intro-to-web-components)
*   [远程用户界面](https://github.com/Kevnz/remote-control-ui)
*   [亮叶反应钩](https://github.com/brightleaf/react-hooks)
*   [亮叶元素](https://github.com/brightleaf/elements)