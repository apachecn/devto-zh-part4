# 苗条+顺风+故事书入门模板

> 原文：<https://dev.to/jerriclynsjohn/svelte-tailwind-storybook-starter-template-2nih>

首先，这里是到 [Github repo](https://github.com/jerriclynsjohn/svelte-storybook-tailwind) 、**的链接，继续开始你的项目，而不是摆弄配置**。相信我，那要花很多时间。

> 访问这个网站看看结果:[苗条+尾巴+故事书](https://svelte-tailwindcss-storybook.netlify.com/)

```
// Quickstart

npx degit jerriclynsjohn/svelte-storybook-tailwind my-svelte-project
cd my-svelte-project

yarn
yarn dev
yarn stories 
```

Enter fullscreen mode Exit fullscreen mode

Svelte 和 TailwindCSS 对于前端开发来说是一个很棒的组合，但是有时候设置看起来有点不直观，尤其是在尝试这个很棒的组合的时候。当集成 Storybook 时，它是 UI 组件开发和文档编制的另一个很棒的工具，没有明显的地方可以知道它是如何完成的。这个回购就是为了解决这个问题！

> 您可以使用这个模板轻松地开始您的项目，而不是浪费时间为每个集成计算配置。

## 在这次回购中你得到了什么

[![Storybook UI](img/1d27dc30870119a558c637ed129f70cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_SyUwBko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggwa8rz330r3h4os2gkj.PNG)

1.  一个全功能的 Svelte + TailwindCSS 集成，并行实现独立的故事书
2.  有 5 个基本附件的故事书
3.  故事书填充了苗条+尾巴的基本例子

### 插件

*   辅助功能插件

[![Accessibility Addon](img/1b8f60c1aba18762dd18d49ac3241ce4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tsmQUPAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9yxyu0eagh2jhyx1n67e.PNG)

*   辅助功能插件-色盲模拟

[![Accessibility Addon - Colorblindness Emulation](img/1d30c52ad99ef65153d8cd898023246c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AvMJ3cr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v3j5tjgtoeg0enebvn0a.PNG)

*   动作插件

[![Actions Addon](img/6b9018a6a8b61f5d393ec7a5a3b6b7e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7tOn_aQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzadsqjy82v1pe4chrxc.PNG)

*   附注插件

[![Notes Addon](img/ee79c42f740d9eb82be4bbe8058e0417.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q5zpDGZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/60h6xjxd6s7m3p3wciul.PNG)

*   源插件

[![Source Addon](img/225ff51f05cd089cab2f2ac65c90b568.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aZn-PxNZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9b1phx4ebu1r0egqfhs5.PNG)

*   视口插件

[![Source Addon](img/5c3c6d2da508bcfd21dc9c1e1546cb1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j0WbykQF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5tehf5gzekuufteizlx7.PNG)

## 苗条+顺风+故事书

故事书是一个开源工具，用于独立开发 JavaScript UI
组件

Svelte 是一个组件框架，它允许你编写高效的、
命令式代码，通过外科手术更新 DOM 来保持性能。

[TailwindCSS](https://tailwindcss.com) 是一个高度可定制的低级 CSS 框架，它为
提供了构建定制设计所需的所有构件，而没有任何令人讨厌的固执己见的
风格，你必须努力克服这些风格。

## 步骤构建

1.  克隆此回购`git clone https://github.com/jerriclynsjohn/svelte-storybook-tailwind.git`
2.  转到目录`cd svelte-storybook-tailwind`
3.  安装依赖关系`yarn`
4.  开发你的苗条应用:`yarn dev`
5.  开发独立于你的应用的 UI 组件:`yarn stories`

### 单据

1.  苗条- [API](https://svelte.dev/docs) 和[教程](https://svelte.dev/tutorial/)
2.  TailwindCSS - [文档](https://tailwindcss.com/docs)和[教程](https://tailwindcss.com/screencasts/)
3.  故事书- [文档](https://storybook.js.org/docs/basics/introduction/)和[教程(还没有苗条！)](https://www.learnstorybook.com/)

## 自己动手搭建的步骤和一些小技巧【警告:太长了】

### 实例化苗条 App

*   使用`npx degit sveltejs/template svelte-storybook-tailwind`启动模板文件
*   转到目录`cd svelte-storybook-tailwind`
*   安装依赖关系`yarn`
*   尝试运行苗条的应用程序`yarn dev`

### 将顺风添加到项目中

*   安装依赖项:`yarn add -D tailwindcss @fullhuman/postcss-purgecss autoprefixer postcss postcss-import svelte-preprocess rollup-plugin-postcss`
*   在`src`中添加`utils.css`，并添加:

```
 /* Import Tailwind as Global Utils */
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities'; 
```

Enter fullscreen mode Exit fullscreen mode

*   将此导入到`main.js`中`import './utils.css'`

*   如图所示更改汇总配置:

```
import svelte from 'rollup-plugin-svelte';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import livereload from 'rollup-plugin-livereload';
import { terser } from 'rollup-plugin-terser';
import postcss from 'rollup-plugin-postcss';
import autoPreprocess from 'svelte-preprocess';

const production = !process.env.ROLLUP_WATCH;

export default {
    input: 'src/main.js',
    output: {
        sourcemap: true,
        format: 'iife',
        name: 'app',
        file: 'public/bundle.js',
    },
    plugins: [
        svelte({
            preprocess: autoPreprocess({
                postcss: true,
            }),
            // enable run-time checks when not in production
            dev: !production,
            // we'll extract any component CSS out into
            // a separate file — better for performance
            css: css => {
                css.write('public/bundle.css');
            },
        }),
        postcss({
            extract: 'public/utils.css',
        }),

        // If you have external dependencies installed from
        // npm, you'll most likely need these plugins. In
        // some cases you'll need additional configuration —
        // consult the documentation for details:
        // https://github.com/rollup/rollup-plugin-commonjs
        resolve({
            browser: true,
            dedupe: importee => importee === 'svelte' || importee.startsWith('svelte/'),
        }),
        commonjs(),

        // Watch the `public` directory and refresh the
        // browser on changes when not in production
        !production && livereload('public'),

        // If we're building for production (npm run build
        // instead of npm run dev), minify
        production && terser(),
    ],
    watch: {
        clearScreen: false,
    },
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   使用命令`npx tailwind init`添加顺风配置

*   添加 PostCSS 配置`./postcss.config.js`如下:

```
const production = !process.env.ROLLUP_WATCH;
const purgecss = require('@fullhuman/postcss-purgecss');

module.exports = {
    plugins: [
        require('postcss-import')(),
        require('tailwindcss'),
        require('autoprefixer'),
        production &&
            purgecss({
                content: ['./**/*.html', './**/*.svelte'],
                defaultExtractor: content => {
                    const regExp = new RegExp(/[A-Za-z0-9-_:/]+/g);

                    const matchedTokens = [];

                    let match = regExp.exec(content);
                    // To make sure that you do not lose any tailwind classes used in class directive.
                    // https://github.com/tailwindcss/discuss/issues/254#issuecomment-517918397
                    while (match) {
                        if (match[0].startsWith('class:')) {
                            matchedTokens.push(match[0].substring(6));
                        } else {
                            matchedTokens.push(match[0]);
                        }

                        match = regExp.exec(content);
                    }

                    return matchedTokens;
                },
            }),
    ],
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   从`public`文件夹中删除`global.css`，然后从`index.html`中删除引用

*   使用一些 TailwindCSS 实用程序构建项目`yarn dev`

### 将故事书添加到瘦项目中

*   添加故事书依赖关系`yarn add -D @storybook/svelte`
*   添加 5 个常用故事书[插件](https://storybook.js.org/addons/):

    *   [来源](https://github.com/storybookjs/storybook/tree/master/addons/storysource) : `yarn add -D @storybook/addon-storysource`
    *   [动作](https://github.com/storybookjs/storybook/tree/master/addons/actions) : `yarn add -D @storybook/addon-actions`
    *   [备注](https://github.com/storybookjs/storybook/tree/master/addons/notes) : `yarn add -D @storybook/addon-notes`
    *   [视口](https://github.com/storybookjs/storybook/tree/master/addons/viewport) : `yarn add -D @storybook/addon-viewport`
    *   [无障碍](https://github.com/storybookjs/storybook/tree/master/addons/a11y) : `yarn add @storybook/addon-a11y --dev`
*   在根目录`.storybook/addons.js`创建一个附加文件，内容如下，并让

    在这个文件中添加附加文件。

```
import '@storybook/addon-storysource/register';
import '@storybook/addon-actions/register';
import '@storybook/addon-notes/register';
import '@storybook/addon-viewport/register';
import '@storybook/addon-a11y/register'; 
```

Enter fullscreen mode Exit fullscreen mode

*   在根目录`.storybook/config.js`下创建一个配置文件，内容如下:

```
import { configure, addParameters, addDecorator } from '@storybook/svelte';
import { withA11y } from '@storybook/addon-a11y';

// automatically import all files ending in *.stories.js
const req = require.context('../storybook/stories', true, /\.stories\.js$/);
function loadStories() {
    req.keys().forEach(filename => req(filename));
}

configure(loadStories, module);
addDecorator(withA11y);
addParameters({ viewport: { viewports: newViewports } }); 
```

Enter fullscreen mode Exit fullscreen mode

*   在`.storybook`下的`webpack.config.js`中添加顺风配置，并适应源插件:

```
const path = require('path');

module.exports = ({ config, mode }) => {
    config.module.rules.push(
        {
            test: /\.css$/,
            loaders: [
                {
                    loader: 'postcss-loader',
                    options: {
                        sourceMap: true,
                        config: {
                            path: './.storybook/',
                        },
                    },
                },
            ],

            include: path.resolve(__dirname, '../storybook/'),
        },
        //This is the new block for the addon
        {
            test: /\.stories\.js?$/,
            loaders: [require.resolve('@storybook/addon-storysource/loader')],
            include: [path.resolve(__dirname, '../storybook')],
            enforce: 'pre',
        },
    );

    return config;
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   在`.storybook`下创建`postcss.config.js`:

```
var tailwindcss = require('tailwindcss');

module.exports = {
    plugins: [
        require('postcss-import')(),
        tailwindcss('./tailwind.config.js'),
        require('autoprefixer'),
    ],
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   确保你有 babel 和 svelite-loader 依赖关系`yarn add -D babel-loader @babel/core svelte-loader`
*   在您的`package.json`中添加 npm 脚本

```
{
    "scripts": {
        // Rest of the scripts
        "stories": "start-storybook",
        "build-stories": "build-storybook"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在`storybook/css/`下添加一个 utils.css 文件，并确保`import 'utils.css'`在你的`stories.js`文件中:

```
/* Import Tailwind as Global Utils */

@import 'tailwindcss/base';

@import 'tailwindcss/components';

@import 'tailwindcss/utilities'; 
```

Enter fullscreen mode Exit fullscreen mode

*   在`storybook\components`中写下你苗条的部分，是的，你可以使用你的常规`.svelte`文件。唯一的问题是你不能在故事中使用模板，还不支持，但是你可以把其他组件组合在一起。对于 starter pack，我们只需创建一个可点击的按钮。

```
<script>
    import { createEventDispatcher } from 'svelte';
    export let text = '';
    const dispatch = createEventDispatcher();
    function onClick(event) {
      dispatch('click', event);
    }
</script>

<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        on:click={onClick}>
  {text}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

*   在`storybook/stories`中写下你的故事，你可以用`<anything>.stories.js`命名任意数量的故事文件，对于初始包，我们可以在`<anything>.stories.md`用自述文件创建`Button`的故事。注意:在这里引用 css 以确保 tailwind 被 postcss 调用:

```
import '../../css/utils.css';

import { storiesOf } from '@storybook/svelte';
import ButtonSimple from '../../components/buttons/button-simple.svelte';
import markdownNotes from './buttons.stories.md';

storiesOf('Buttons | Buttons', module)
    //Simple Button
    .add(
        'Simple',
        () => ({
            Component: ButtonSimple,
            props: { text: 'Button' },
            on: {
                click: action('I am logging in the actions tab too'),
            },
        }),
        { notes: { markdown: markdownNotes } },
    ) 
```

Enter fullscreen mode Exit fullscreen mode

*   为组件编写自己的文档，这将`<anything>.stories.md`:

```
# Buttons

_Examples of building buttons with Tailwind CSS._
 --- 
Tailwind doesn't include pre-designed button styles out of the box, but they're easy to build using
existing utilities.

Here are a few examples to help you get an idea of how to build components like this using Tailwind. 
```

Enter fullscreen mode Exit fullscreen mode

*   运行你的故事板`yarn stories`，你会看到这个:

[![Storybook UI](img/e7a1e34e6aac0389e239056f74112e26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aYB2yTtz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydx2poo243wi4cyvbdxm.PNG)

你可以添加更多的插件和他们一起玩。

这是一个总结！