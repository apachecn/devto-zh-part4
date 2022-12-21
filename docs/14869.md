# 快速 3:使用插槽构建组件

> 原文：<https://dev.to/oieduardorabelo/svelte-3-composicao-de-componentes-com-slots-4nlg>

## 网络增强的 web 应用

> 在短时间内，可以使用插槽将组件组合在一起。合成是指允许其组件包含其它 HTML 组件或元素。插槽可以通过在可接受组件或标记的组件内使用组件“`<slot>`”快速实现。

这是我们对 HTML 元素已经习以为常的事情。让我们演示如何通过构建组件`Card`来使用组件`<slot>`。

本部件`Card`与我用于[演示快捷方式](https://medium.com/@oieduardorabelo/svelte-3-utilizando-props-8ee440507166) :
中使用 props 的部件相似

```
<script>
  export let title;
  export let imageUrl;
</script>

<style>
  /* Algo para deixar isso bonito */
</style>

<section>
  <h1>
    <img src={imageUrl} alt="Avatar for {title}" />
     {title}
  </h1>

  <div>
    <slot />
  </div>
</section> 
```

如您所见，我们只是把‘t0’放在我们希望组件用户能够添加自定义组件或标记的地方。

我们可以用自动关闭的方式使用:

```
<slot /> 
```

或带有结束标记:

```
<slot></slot> 
```

## 预留空间和后备内容

您可以将后备内容放在“
”内，如果使用组件时没有内部元素，则使用该内容:

```
<script>
  export let title;
  export let imageUrl;
</script>

<style>
  /* Algo para deixar isso bonito */
</style>

<section>
  <h1>
    <img src={imageUrl} alt="Avatar for {title}" />
     {title}
  </h1>

  <div>
    <slot>
      <p>😢 Sem detalhes!</p>
    </slot>
  </div>
</section> 
```

我们可以按如下方式使用`Card`:

```
<script>
  import Card from "./Card.svelte";

  const items = [
    {
      title: "Pirate",
      description: "Argg!!",
      imageUrl: "https://alligator.img/pirate.svg"
    },
    {
      title: "Chef",
      description: "À la soupe!",
      imageUrl: "https://alligator.img/chef.svg"
    }
  ];
</script>

{#each items as item}
  <Card title={item.title} imageUrl={item.imageUrl}>
    <hr />
    <p>{item.description}</p>
  </Card>
{/each} 
```

这样一来，一条水平线和一段带有项目说明的文字将出现在我们的组件`Card`内，而不是`<slot>`。

## 命名插槽

您不仅限于每个组件只有一个插槽。您可以在组件“`<slot>`”中使用属性“`name`”命名插槽，然后在组件中可以拥有任意数量的插槽。

如下例所示，您还可以将命名插槽与标准/未命名插槽结合使用:

```
<style>
  /* Faça-o bonito! */
</style>

<section>
  <slot name="title" />

  <slot name="description">
    <p>😮 Sem descrição!</p>
  </slot>

  <footer>
    <slot />
  </footer>
</section> 
```

并通过在每个插槽位置的父元素中添加属性“`name`”来使用命名插槽:

```
<script>
  import Card from "./Card.svelte";

  const items = [
    {
      title: "Pirate",
      description: "Argg!!",
      imageUrl: "https://alligator.img/pirate.svg"
    },
    {
      title: "Chef",
      description: "À la soupe!",
      imageUrl: "https://alligator.img/chef.svg"
    }
  ];
</script>

{#each items as item}
  <Card>
    <h1 slot="title">
      <img src={item.imageUrl} alt="Avatar for {item.title}" />
       {item.title}
    </h1>

    <p slot="description">{item.description}</p>

    <p>Something else!</p>
  </Card>
{/each} 
```

**记住，只能有一个标准插槽！**

## 完成

现在去把你内心的内容写下来！查看[官方教程](https://svelte.dev/tutorial/slots)，了解更多快速插槽使用实例。另一件有趣的事情是[可以从教程中学习](https://svelte.dev/tutorial/slot-props)插槽可以使用 props，称为*插槽，以便将 props 切换回使用它的组件。*

 *# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [使用插槽](https://alligator.io/svelte/slots/)，escrito originate por[@ alligatorio](https://mobile.twitter.com/alligatorio)组装您的细长组件*