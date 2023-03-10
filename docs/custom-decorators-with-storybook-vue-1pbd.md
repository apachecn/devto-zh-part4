# 用故事书和 Vue 定制装饰者

> 原文：<https://dev.to/khrome83/custom-decorators-with-storybook-vue-1pbd>

Storybook 拥有出色的 Vue 支持。虽然它在发布时不支持 Vue，但现在支持了。因此，它已经成为我的 goto 技术，同时充实基础组件。它对我的开发过程至关重要，我认为它也应该被考虑到你的过程中。

我不再需要删除页面或者拼凑一个测试页面。相反，我可以专注于我的设计语言。每一个故事都是一个基础组件，使其非常清晰，处理起来也更加舒适。它以意想不到的方式加速了我的发展。

Storybook 中的生态系统也涵盖了我的许多担忧。我喜欢“旋钮”插件。它允许我通过混合设置对每个组件进行压力测试。a11y 插件为我提供了组件当前状态的高级视图，以确保一切都是可访问的。为了模拟不同的视口，我使用了断点组件。这些工具确保我在过程中更早地考虑这些问题。而且，我的工作也因此更好。

## 问题

无论如何，我最近确实遇到了一个小问题。如何一次构建多个主题？我依赖的模式之一是使用背景色来修改前景中的托盘。我喜欢拆分页面的概念。此外，随着我的营销页面中内容的变化，我希望能够灵活地动态改变托盘。这是一个跨三个不同托盘的相同注册横幅的示例。

[![](img/4fa62a2f9d11e0deb48699f2cff10b95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jvv4Fd7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://khrome.dev/uploads/multiple_themes.png)

所以我想用一个简单的`<base-badge>`组件来演示一下。该组件在其默认插槽中接受一个标签，并在一个彩色标记中显示它。它非常适合通知计数和标记内容。这是故事书里的一个例子。

[![](img/993e2cdcdc8231985dec225d8b3052c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NMl4LmCG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://khrome.dev/uploads/badge-on-single-background.png)

### BaseBadge 细分

这里是`BaseBadge.vue`文件。

> *注意*——这里使用了打字稿和`nuxt-property-decorator`，我是它们的忠实粉丝。

```
<template>
  <span class="badge" :class="{ secondary, small, large }">
    <slot></slot>
  </span>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "nuxt-property-decorator";

@Component({})
export default class BaseBadge extends Vue {
  @Prop(Boolean) private secondary!: boolean;
  @Prop(Boolean) private small!: boolean;
  @Prop(Boolean) private large!: boolean;
}
</script>

<style scoped>
.badge {
  display: inline-block;
  text-align: center;
  color: #fff;
  fill: #fff;
  background-color: #ee0028;
  border-radius: 2px;
  padding: 4px 6px;
  text-decoration: none;
  font-size: 16px;
  font-weight: 400;
  line-height: 1.1;
  letter-spacing: 0.025rem;
  text-transform: uppercase;
}

/* Grey Modifications - Badge */
.__bg-grey .badge {
  background-color: #da0629;
  color: #fdfcfb;
  fill: #fdfcfb;
}

/* Dark Modifications - Badge */
.__bg-dark .badge {
  background-color: #f32144;
  color: #010b19;
  fill: #010b19;
}

.secondary {
  background-color: #010b19;
}

/* Grey Modifications - Secondary */
.__bg-grey .secondary {
  background-color: #010b19;
  color: #fdfcfb;
  fill: #fdfcfb;
}

/* Dark Modifications - Secondary */
.__bg-dark .secondary {
  background-color: #ffffff;
  color: #010b19;
  fill: #010b19;
}

.small {
  font-size: 14px;
  padding: 4px 8px;
  border-radius: 16px;
  line-height: 1;
  letter-spacing: 1.25;
}

.large {
  font-size: 20px;
  padding: 6px 12px;
  letter-spacing: 0.1rem;
  line-height: 1;
}
</style> 
```

对于那些刚接触 Vue 和 TypeScript 的人来说，我将很快对此进行分解。

```
<template>
  <span class="badge" :class="{ secondary, small, large }">
    <slot></slot>
  </span>
</template> 
```

模板部分是相对标准和简单的 Vue。我们正在创建一个包含传递到默认位置的文本的 span。该接口接受尺寸和调色板。默认为标准尺寸和原色调色板。

```
<script lang="ts">
import { Component, Prop, Vue } from "nuxt-property-decorator";

@Component({})
export default class BaseBadge extends Vue {
  @Prop(Boolean) private secondary!: boolean;
  @Prop(Boolean) private small!: boolean;
  @Prop(Boolean) private large!: boolean;
}
</script> 
```

注意`<script lang=" ts">`，我们告诉 Vue 将它作为类型脚本处理。

`import`行用于从 [nuxt-property-decorator](https://github.com/nuxt-community/nuxt-property-decorator) 引入我们的装饰器和类，它是围绕其他四个模块的一个很好的包装器。这只是清理了接口，而不是必须分别引入 [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) 、 [vue-class-component](https://github.com/vuejs/vue-class-component) 、 [vuex-class](https://github.com/ktsn/vuex-class/) 和 [nuxt-class-component](https://github.com/nuxt-community/nuxt-class-component) 。

装饰器将类定义为一个组件。`@Prop(Boolean)`定义了 Vue 组件的道具。请注意，`Boolean` / `boolean`在属性声明期间重复出现，这同时设置了类型脚本类型检查和 Vue 属性类型检查。请注意，我们在 Vue 组件内部没有任何状态或逻辑要处理。我想把重点放在 CSS 上。

### CSS 样式

```
.secondary {
  background-color: #010b19;
}

/* Grey Modifications - Secondary */
.__bg-grey .secondary {
  background-color: #010b19;
  color: #fdfcfb;
  fill: #fdfcfb;
}

/* Dark Modifications - Secondary */
.__bg-dark .secondary {
  background-color: #ffffff;
  color: #010b19;
  fill: #010b19;
}
</style> 
```

只看 CSS 的一小部分，你可以看到我们修改了`.secondary`三次。默认调色板被认为是“主要”主题，并设置背景颜色。当组件位于应用了`.__bg-grey`或`.__bg-dark`类的元素中时，它会被修改。

我在我的网站中为不同的部分使用的结构是让一个部分定义背景颜色。然后里面的内容对此作出回应。

```
<section class="__bg-dark">
    <base-badge secondary/>
</section> 
```

目标是确保当父部分改变主题时，所有子部分都相应地改变它们的调色板。

## 故事书故事

故事书的设置相对简单。它使用 Storybook 中使用 Vue 的标准模式，以及一些“旋钮”

### 基础故事书故事

首先，我们从`@storybook/vue`模块导入`storiesOf`方法。方法允许我们创建一个故事，并在名称空间中定义它。我们也进口我们的`BaseBadge.vue`组件。

> 提示-我将所有基本组件放在一个名为" Components "的文件夹中目的是让其他人清楚哪些组件可以组合起来创建更重要的组件集。基本组件通常没有状态，是 Vue 中最低级别的组件。

```
import { storiesOf } from "@storybook/vue";
import BaseBadge from "../components/BaseBadge.vue";

const stories = storiesOf("Components/Base Badge", module); 
```

接下来，我们将在页面上显示`BaseBadge`。我们使用`stories.add`方法将故事添加到故事书中。我将组件的所有根实例的页面命名为“Default ”,除非我有不同的实现设置。

该模板只是一个简单的 ES6 模板字符串，它导出的内容与 Vue 模板中的内容相同。

```
stories.add(
  "Default",
  () =>
    ({
      components: { BaseBadge },
      },
      template: `
        <base-badge>
          New
        </base-badge>
      `
    } as object)
); 
```

### 添加旋钮

我们有一个基本的渲染，但我们不能测试任何压力情况。我们希望能够改变文本，修改大小和调色板。首先，我们想从`@storybook/addon-knobs`导入我们需要的接口类型。

```
import { radios, text } from "@storybook/addon-knobs"; 
```

然后我们想要扩展故事定义对象，以包含一个 props 对象，它告诉 Storybook 要启用哪些“旋钮”以及每个旋钮的规则和标签。在这种情况下，我们使用`text`旋钮来定义徽章内的内容。`radio`旋钮用于选择徽章的主题和大小。

```
 {
      components: { BaseBadge },
      props: {
        text: {
          default: text("Text", "NEW")
        },
        theme: {
          default: radios(
            "Theme",
            {
              Primary: "primary",
              Secondary: "secondary"
            },
            "primary"
          )
        },
        size: {
          default: radios(
            "Size",
            {
              Small: "small",
              Normal: "normal",
              Large: "large"
            },
            "normal"
          )
        }
      },
      template: ``,
    } 
```

最后，我们想要修改模板以使用这些旋钮的值。

```
 <base-badge v-bind="{
              secondary: theme === 'secondary',
              small: size === 'small',
              large: size === 'large',
            }">
              {{text}}
            </base-badge> 
```

这里是完整的故事。虽然我们还没有解决多重背景颜色的问题，但是我们已经为它构建了 Vue 组件和故事书故事。

```
import { storiesOf } from "@storybook/vue";
import { radios, text, boolean } from "@storybook/addon-knobs";
import BaseBadge from "../components/BaseBadge.vue";

const stories = storiesOf("Components/Base Badge", module);

stories.add(
  "Default",
  () =>
    ({
      components: { BaseBadge },
      props: {
        text: {
          default: text("Text", "NEW")
        },
        theme: {
          default: radios(
            "Theme",
            {
              Primary: "primary",
              Secondary: "secondary"
            },
            "primary"
          )
        },
        size: {
          default: radios(
            "Size",
            {
              Small: "small",
              Normal: "normal",
              Large: "large"
            },
            "normal"
          )
        }
      },
      template: `
        <base-badge v-bind="{
          secondary: theme === 'secondary',
          small: size === 'small',
          large: size === 'large',
        }">
          {{text}}
        </base-badge>
      `
    } as object)
); 
```

到目前为止，我们已经建立了这个。我一开始给你看的东西。

[![](img/993e2cdcdc8231985dec225d8b3052c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NMl4LmCG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://khrome.dev/uploads/badge-on-single-background.png)

### 建筑装饰师

Storybook 使用 decorators 来扩展故事的功能。这些装饰器看起来与我们在 typescript 中看到的 ES7 装饰器略有不同，但概念是相似的。我们希望扩展核心对象的功能并引入新的行为。

在 Storybook 中构建一个装饰器相当简单。它只是从一个方法中导出一个对象。对象中的`data`方法用于返回模板的属性。然后`template`可以访问数据返回的任何东西。

在这种情况下，数据方法返回 CSS 样式的对象。这个对象遵循在 JS 中为 Vue 使用样式的规则。所以`box-shadow`变成了`boxShadow`并且是键，而值是 CSS 属性的内容字符串。

然后，我们使用`:style="wrapper"`绑定将这些样式应用于 HTML 元素。

最后，模板中的`<story />`组件告诉 Storybook 在哪里注入我们的根故事。对于`<story />`的每个实例，Storybook 都会复制一份应用了装饰器的模板。我们希望这适用于我们的`<base-badge>`模板。

```
const sectionStates = () => ({
  data: () => ({
    wrapper: {
      margin: "0 2rem 2rem",
      border: "thin solid transparent",
      boxShadow: "rgba(0, 0, 0, 0.15) 0rem 0.125rem 0.3125rem 0rem",
      borderRadius: "0.3125rem",
      padding: "2rem"
    },
    light: {
      backgroundColor: "#ffffff"
    },
    grey: {
      backgroundColor: "#fdfcfb",
      boxShadow: "rgba(0, 0, 0, 0.2) 0rem 0.125rem 0.3125rem 0rem"
    },
    dark: {
      backgroundColor: "#010b19",
      boxShadow: "rgba(0, 0, 0, 0.5) 0rem 0.125rem 0.3125rem 0rem"
    },
    heading: {
      fontSize: "0.75rem",
      margin: "0",
      padding: "0.5rem 0 0.5rem 2rem",
      color: "#737373",
      textTransform: "uppercase"
    }
  }),
  template: `
    <div>
      <div :style="heading">On Light Background</div>
      <div class="__bg-light" :style="[wrapper, light]"><story/></div>
      <div :style="heading">On Gray Background</div>
      <div class="__bg-grey" :style="[wrapper, grey]"><story/></div>
      <div :style="heading">On Dark Background</div>
      <div class="__bg-dark" :style="[wrapper, dark]"><story/></div>
    </div>
    `
});

export { sectionStates as default }; 
```

让它在多种背景下工作的是包含了`.__bg-light`、`.__bg-grey`和`.__bg-dark` CSS 类。这些正在我的全球风格中使用，以增加任何孩子。

> 注意——我称这个 **sectionStates** ,因为在我的项目中定义背景颜色的标准 Vue 组件是一个 section。所以它有几个颜色状态，子组件也必须做出反应。

### 包括我们的装潢师

下一步是在我们之前构建的故事中使用这个装饰器。首先，我们希望将`addDecorator`方法添加到我们的导入中。该方法用于将自定义装饰器应用于故事书故事。

```
import sectionStates from "../utils/sectionStates.ts";
import { storiesOf, addDecorator } from "@storybook/vue"; 
```

最后，我们链接`storesOf`方法，并在`sectionStates`中调用`addDecorator`方法。

```
const stories = storiesOf("Components/Base Badge", module).addDecorator(
  sectionStates
); 
```

输出是三个实例，而不是一个。每个实例都有不同的背景颜色。每个实例中的每个子实例都尊重其父容器。结果完美地模仿了`BaseSection.vue`组件的行为。

[![](img/a7d47576d38c8f057317115d2910ef4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RT067MjS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://khrome.dev/uploads/badge-on-multiple-backgrounds.png)

额外的好处是，这允许我们验证每个变更的可访问性。我们看到了所有背景托盘的所有可能性。

[![](img/98753e7d9a9725028d753e07a2b0a0bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ExCWdo9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://khrome.dev/uploads/badge-with-a11y.png)

### 结论

这种模式在很多情况下是有益的-

*   构建多个主题，如下例所示
*   支持跨多个品牌的共享组件
*   使用通过 CSS 命名空间工作的其他类型的外部修饰符。

一般来说，这很容易做到，只要你熟悉 JS 中的 CSS，并且你遵循严格的类命名结构。

*最初发布于[KH Rome . dev](https://khrome.dev/custom-decorators-with-storybook-and-vue)T3】*