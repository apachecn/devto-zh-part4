# 如何为 Gutenberg 修改你的插件:第 1 部分(Block API)

> 原文：<https://dev.to/codeinwp/how-to-adapt-your-plugin-for-gutenberg-part-1-block-api-94d>

“我有一个插件，”你说，“我如何让它为古腾堡做好准备？”

故事很简单； [Gutenberg](https://www.codeinwp.com/blog/wordpress-gutenberg-guide/) 是 WordPress 中新的编辑器体验，将在下一个主要版本中合并到 core 中。很多跟不上这个**的插件会变得过时**。这使得你必须在为时已晚之前为古腾堡修改你的插件。

谁受到了影响？

**几乎所有跟帖子编辑器有关系的插件**都会受到古腾堡的影响。例如，如果你的插件在 TinyMCE 中添加一个按钮，然后在编辑器中放置一个短代码，这是个坏消息；这将是它的结束。

### **“我需要为古腾堡改编我的插件吗？”**

那么古腾堡需要更新哪些插件呢？任何可用于以下项目的插件:

*   自定义元盒
*   短代码
*   TinyMCE 钮扣
*   或者任何编辑器特性

虽然有些插件仍然适用于 Gutenberg，比如增加了一个简单的元盒的插件，但是对于你的用户来说，它不会是一个流畅的体验。

即使短代码也会像以前一样工作，但它只是编辑器中的纯文本节点，而 Gutenberg 的所有短代码插件都将遵循其块 UI，并且更易于用户使用。

所以，是的，用户会更喜欢为古腾堡体验制作的插件。留下来的将被竞争对手的插件所取代。

为了给你一个概念，这里有一个例子，展示了用户使用我们的 **(a)** 插件时的标准老编辑器体验，以及在古腾堡 **(b)** 插件优化后的体验。

[![Alt text of image](img/1099c5056bc9428583d5de0d8c46ee83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--agCJvM9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2018/07/Feedzy-Visual-Editor.gif)

[![Alt text of image](img/a3337d28b0d7930d033290a8baaaacf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gO9-qm3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2018/07/Feedzy-Gutenberg-Editor.gif)

不要害怕！我们在这里帮助你使你的插件 Gutenberg-ready。有很多方法可以将你的插件集成到 Gutenberg，这取决于你的插件是如何工作的，我们将在本文中讨论。

**值得事先知道的事情**

古腾堡是写在 React 上的。Gutenberg 插件是用 JavaScript 编写的，对于只使用 PHP 编写代码的开发人员来说，这也是一个艰难的过渡。虽然你不需要了解 React 来为 Gutenberg 制作插件，但是你需要对 JavaScript 有一些基本的了解。如果你以前在 React 和 JSX 工作过，那么对你来说会有类似的理由。

虽然 Gutenberg 没有足够的官方文档，但它的 Github 知识库为开发者提供了大量有价值的信息。如果你想深入了解 Gutenberg 的开发，你必须关注 Gutenberg 的 GitHub 库中发生的一切，因为项目进展非常快，每天都在发生变化。

### **如何让你的插件适应古腾堡**

Gutenberg 的 API 为我们提供了许多扩展编辑器的方法，比如 Block API、Shortcode API 等等。使用哪种 API 取决于我们正在构建的插件的类型。

如果你的插件是一个简单的短代码插件，那么 Block API 可以用来为编辑器制作一个漂亮的块。但是如果你的插件使用复杂的元盒，一个块是不够的，我们可以使用侧边栏 API。

此外，我们将使用现代的 JavaScript 开发工具，如 NodeJS、NPM、webpack 和 ESNext。我们将为您提供示例文件，因此您不必担心如何设置您的开发环境。

在这篇文章中，我们将会看到**如何通过使用 Block API** 使你的插件 Gutenberg 兼容。如果需要，我们将在第二部分讨论其他方法(侧栏 API、发布面板、状态栏&类似的 API)。

你可以在 GitHub 库中找到所有提到的例子。

### **古腾堡开发环境**

为古腾堡开发需要你安装一堆工具，比如 NPM、网络包、巴别塔和 JSX。这需要很多时间和精力，所以我们为你准备了环境。

Gutenberg Boilerplate 是一个带有最少 Gutenberg 开发设置和示例的插件。它包含一个块和侧边栏示例。您可以使用它来扩展到您的自定义块中。

[![Alt text of image](img/270a3dceaf1a59f92c84c736a12eced3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rdBflXJi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2028/07/Gutenberg-Boilerplate.png)

您可以将 [Gutenberg 样板库](https://github.com/HardeepAsrani/gutenberg-boilerplate)派生或克隆到您的 **/wp-content/plugins/** 中，并将其用作您的开发环境。
之后，你需要在你的机器上安装 NodeJS 来开始。导航到 Gutenberg 样板文件夹，运行 *npm install*

从现在开始，您需要知道在开发过程中将使用的三个命令:

*   *npm 安装*–在克隆项目时安装项目依赖关系。
*   *npm 运行开发*–在开发过程中编译代码。您需要运行一次，它会一直观察变化。
*   *npm 运行构建*–开发流程完成后，编译代码以优化构建。

### **屏蔽 API**

块是 Gutenberg 的基本元素，它是一个基于块的编辑器。块 API 允许你为古腾堡制造块。你可以制作能够显示基本 HTML、短代码的块，甚至可以制作动态块来显示，例如，你最近的文章。

### **基于现有插件的流程**

在我们的例子中，我们将采用一次点击向 Gutenberg 块发送短代码。点击 Tweet shortcode 将呈现一个包含您的文本的 Tweet 框，以及一个用于 Tweet 的按钮。像这样:

[![Alt text of image](img/b64a614e72545136b7ba7e96972a99d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oQCmi5L0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2028/07/Click-to-Tweet.png)

我们的短代码看起来像这样:

```
[clicktotweet tweet="Text to be displayed" tweetsent="Text to be tweeted" button="Tweet" theme="click-to-tweet"] 
```

我们的短码有四个参数:

*   tweet :出现在你网站上的文字。
*   *tweetsent* :发送到 Twitter 的文本。
*   *按钮* : Tweet 按钮文本。
*   *主题*:或者*点击发微博*或者*点击发微博*作为盒子主题。

### **用 Block API 为古腾堡改编插件**

使用 Gutenberg 有两种方法，要么我们可以将 HTML 渲染到前端，要么我们可以使用 Gutenberg 块将短代码渲染到前端。对于本文，我们将采用后者。

所有代码都可以在 GitHub 上的 [Hello Gutenberg 插件库中找到。您可以克隆存储库来查看运行中的插件，或者修改代码。](https://github.com/HardeepAsrani/hello-gutenberg)

#### **为古腾堡入队脚本/样式**

首先，我们需要使用 enqueue_block_assets:
将我们的脚本和样式排入古腾堡编辑器

```
/**
 * Enqueue front end and editor JavaScript and CSS
 */
function hello_gutenberg_scripts() {
    $blockPath = '/dist/block.js';
    $stylePath = '/dist/block.css';

    // Enqueue the bundled block JS file
    wp_enqueue_script(
        'hello-gutenberg-block-js',
        plugins_url( $blockPath, __FILE__ ),
        [ 'wp-i18n', 'wp-blocks', 'wp-editor', 'wp-components' ],
        filemtime( plugin_dir_path(__FILE__) . $blockPath )
    );

    // Enqueue frontend and editor block styles
    wp_enqueue_style(
        'hello-gutenberg-block-css',
        plugins_url( $stylePath, __FILE__ ),
        '',
        filemtime( plugin_dir_path(__FILE__) . $stylePath )
    );

}

// Hook scripts function into block editor hook
add_action( 'enqueue_block_assets', 'hello_gutenberg_scripts' ); 
```

我们在脚本中添加了四个依赖项，将在我们的块中使用。先来了解一下这些依赖:
wp-i18n 是古腾堡版本的国际化函数，比如 [__()](https://developer.wordpress.org/reference/functions/__/) 。wp-blocks 用于注册你的块的 registerBlockType 函数。我们为我们的块中的各种组件使用 wp-editor 和 wp-components 脚本。

现在我们已经将您的资产排队，我们可以开始在/ **src/block.js** 文件中编写我们的块。

**导入依赖关系**

如果你正在使用古腾堡样板文件，那么你的 **block.js** 文件应该有一个基本的块结构，你可以用它来为古腾堡:
构建插件

```
/**
 * Internal block libraries
 */
const { __ } = wp.i18n;

const { registerBlockType } = wp.blocks;

/**
 * Register block
 */
export default registerBlockType( 'gutenberg-boilerplate/block', {
    // Block Title
    title: __( 'Gutenberg Boilerplate' ),
    // Block Description
    description: __( 'An example block.' ),
    // Block Category
    category: 'common',
    // Block Icon
    icon: 'admin-site',
    // Block Keywords
    keywords: [
        __( 'Boilerplate' ),
        __( 'Hello World' ),
        __( 'Example' ),
    ],
    attributes: {
    },
    // Defining the edit interface
    edit: props => {
        return (
            <h2>{ __( 'Hello Backend' ) }</h2>
        );
    },
    // Defining the front-end interface
    save: props => {
        return (
            <h2>{ __( 'Hello Frontend' ) }</h2>
        );
    },
}); 
```

您可以运行 *npm run dev* 来开始实时编译我们的代码。

首先，我们将导入顶层块所需的所有组件和库:

```
/**
 * Block dependencies
 */

import classnames from 'classnames';

/**
 * Internal block libraries
 */
const { __ } = wp.i18n;

const { registerBlockType } = wp.blocks;

const {
    RichText,
    InspectorControls,
    BlockControls,
} = wp.editor;

const {
    PanelBody,
    TextareaControl,
    TextControl,
    Dashicon,
    Toolbar,
    Button,
    Tooltip,
} = wp.components; 
```

第一个导入了我们使用 npm 安装的 *classnames* ，以便在我们的代码中使用多个类，因为 JSX 不允许元素中有多个类。

我们将在使用时了解我们导入的其他组件。

**定义属性**

现在我们将为古腾堡块定义四个属性，和我们的短代码一样:

```
attributes: {
    tweet: {
        type: 'string',
    },
    tweetsent: {
        type: 'string',
    },
    button: {
        type: 'string',
        default: __( 'Tweet' ),
    },
    theme: {
        type: 'boolean',
        default: false,
    },
}, 
```

如您所见，所有属性都与我们的短代码相同。所有属性都有一个类型键，它告诉 Gutenberg 它的数据类型。您可以使用字符串、数字、布尔和对象作为可接受的类型。

有些属性还包含默认值。属性也可以有其他属性，比如 source 和选择器，我们在例子中不会用到，但是你可以在这里了解更多。

**编辑界面**

现在，我们将构建我们的块的编辑界面，这将是显示-用户在 Gutenberg 中编辑块时会看到它。

为了得到一个基本的概念，我们可以首先对我们的块进行硬编码，并通过替换代码中的以下区域来构建它:

```
// Defining the edit interface
edit: props => {
    return (
        <h2>{ __( 'Hello Backend' ) }</h2>
    );
}, 
```

用下面的代码:

```
// Defining the edit interface
edit: props => {
    return [
        <div className={ props.className }>
            <div className="click-to-tweet">
                <div className="ctt-text">Pumpkins and Penguins</div>
                <p><a href="https://twitter.com/intent/tweet?text=Pumpkins%20and%20Penguins" className="ctt-btn">Tweet</a></p>
            </div>
        </div>
    ];
}, 
```

这应该会让你对我们的最终结果有所了解。它看起来会像这样:

[![Alt text of image](img/d2fdf74d31f548137796a18b14e8f424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OTCnHyGy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2018/07/Gutenberg-Click-to-Tweet.gif)

该块的第一个元素是 tweet 文本区域。我们将用一个可编辑的文本字段替换它，类似于古腾堡的标题块。

我们将使用之前导入的 **RichText** 组件来替换我们的硬编码文本。

```
<div className="ctt-text">
    <RichText
        format="string"
        formattingControls={ [] }
        placeholder={ __( 'Tweet, tweet!' ) }
        onChange={ onChangeTweet }
        value={ props.attributes.tweet }
    />
</div> 
```

我们的 RichText 组件有五个参数。 *format* 是一个字符串，因为我们将使用我们的 shortcode 来显示前端的元素。如果我们想在属性中使用一个选择器，那么 format 应该是一个数组。

默认情况下，RichText 有一些格式选项，如粗体和斜体，我们通过在 *formattingControls* 参数中传递一个空数组来禁用这些选项。

placeholder 是字段中没有文本时的占位符文本，当 Change 事件发生时，onChange 会触发 onChangeTweet 函数。

最后，value 将是我们字段的值，它将从我们之前定义的 tweet 属性中获取。

一旦我们定义了我们的 RichText 区域，我们需要构建 onChangeTweet 函数，它将在我们的文本字段中的值发生变化时触发。

```
// Defining the edit interface
edit: props => {
    const onChangeTweet = value => {
        props.setAttributes( { tweet: value } );
    };
    return [
    ...rest of the code 
```

我们将 RichText 字段的值传递给*onchangeweet*函数，该函数使用 **props.setAttributes** 函数来更新 Tweet 属性的值。

当你使用你的积木时，你将会看到古腾堡的力量。

[![Alt text of image](img/ab2e8e484bcd7c4e14d66633af687008.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LM3q-SvR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2018/07/RichField-in-Gutenberg.gif)

是不是很牛逼？

#### **闭塞检查员**

在为古腾堡构建插件时，你不需要每次都重新发明轮子来为你的插件制作选项面板。Gutenberg 为我们提供了一种简化的方式来允许块定制，并确保每个用户对内置 UI 模式有一致的体验。

与 RichText 组件类似， *InspectorControls* 组件在块被选中时添加一个侧边栏。大概是这样的:

[![Alt text of image](img/6991908b281d68cf9b589ff30efb7db9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e3wuZdDZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2028/07/Gutenberg-Inspector-Controls.png)

我们还将使用 *TextareaControl* 和 *TextControl* 向我们的检查器区域添加一个文本区域和文本输入字段。

我们将在您的退货单中添加以下代码:

```
!! props.isSelected && (
    <InspectorControls key="inspector">
        <PanelBody title={ __( 'Tweet Settings' ) } >
            <TextareaControl
                label={ __( 'Tweet Text' ) }
                value={ props.attributes.tweetsent }
                onChange={ onChangeTweetSent }
                help={ __( 'You can add hashtags and mentions here that will be part of the actual tweet, but not of the display on your post.' ) }
            />
            <TextControl
                label={ __( 'Button Text' ) }
                value={ props.attributes.button }
                onChange={ onChangeButton }
            />
        </PanelBody>
    </InspectorControls>
), 
```

*props.isSelected* 检查以确保检查员仅在该块被选中时出现。
TextareaControl 和 TextControl 组件与 RichText 类似，都有一个 value 和 onChange 方法。

我们还需要更改您的块显示代码，以适应新的变化。在我们的例子中，我们只需要将按钮文本添加到我们的块中，因为 Tweet 文本将被添加到链接中，所以我们不需要在后端显示它。

你可以用下面的代码替换我们初始代码中的超链接:

```
<a className="ctt-btn">
    { props.attributes.button }
</a> 
```

如前所述，我们从代码中删除了超链接，因为我们不需要在后端显示它。这将使我们的块看起来像这样:

[![Alt text of image](img/95c163a17af2c91aed80d4c8c932f945.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hy-NTt52--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2028/07/Gutenberg-Click-to-Tweet-Inspector-Controls.png)

街区检查员可以成为你的街区的有力工具。如果你的插件有一些高级选项对编辑区来说太复杂了，那么你可以把它们放在检查区。

我们将在我们的块中添加最后一个选项，以完成下一节中的 JavaScript 部分。

### **图块工具栏**

块工具栏是另一个预建的 UI 组件，我们可以使用它来为我们的块添加更多的选项。当你选择它时，它会出现在你的区块上方。示例:

[![Alt text of image](img/fa0b914c314560b51e418c00cadfeee4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Oa0kcJj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2028/07/Gutenberg-Block-Toolbar.png)

理想情况下，块工具栏应该包含块的主要控件，由 Inspector 托管次要控件。然而，这是有争议的，取决于你的区块。

我们将使用块工具栏区域来托管我们的替代样式控件。

类似于 Block Inspector，我们需要将以下代码添加到我们的 return 语句中，以便将块工具栏添加到我们的块中:

```
!! props.isSelected && (
    <BlockControls key="custom-controls">
        <Toolbar
            className='components-toolbar'
        >
            <Tooltip text={ __( 'Alternative Design' ) }>
                <Button
                    className={ classnames(
                        'components-icon-button',
                        'components-toolbar__control',
                        { 'is-active': props.attributes.theme },
                    ) }
                    onClick={ toggletheme }
                >
                    <Dashicon icon="tablet" />
                </Button>
            </Tooltip>
        </Toolbar>
    </BlockControls>
), 
```

我们使用*块控件*和*工具栏*组件给我们的块添加一个工具栏。类似于块检查器， *props.isSelected* 确保我们的工具栏只在我们把焦点放在我们的块上时出现。我们还使用*工具提示*和*按钮*组件进行控制。工具提示组件包裹在按钮组件周围，以确保当您悬停在控件上时有工具提示，从而为您提供更多上下文。

按钮组件使用我们在本文前面导入的类名模块。我们使用 classnames 函数为控件提供了三个类。第三类，*是-active* ，只有当我们的*主题*属性值为真时才会出现。

它的 *onChange* 函数将*主题*属性切换为真/假。最后， *Dashicon* 组件被用来显示我们控件的图标。

我们还将不得不改变我们的块代码，使其与这些变化一起工作。我们需要替换下面一行:

```
<div className="click-to-tweet"> 
```

同:

```
<div className={ ( props.attributes.theme ? 'click-to-tweet-alt' : 'click-to-tweet' ) }> 
```

我们正在检查*主题*属性是真还是假，并相应地给我们的块分配一个类。

现在，您的块应该看起来像这样:

[![Alt text of image](img/2d01c4e79ad3d6de9b491a321e1a3ec5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQ-D9CxR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.codeinwp.com/wp-content/uploads/2018/07/Click-to-Tweet-Toolbar.gif)

我们已经完成了 Gutenberg 块的 JavaScript 端部分。你可以在这里找到文件[的完整源代码。](https://github.com/HardeepAsrani/hello-gutenberg/blob/master/src/block.js)

现在，我们将通过处理服务器端输出来结束我们的代码块。

#### **服务器端渲染**

Gutenberg 允许您使用服务器端渲染在前端显示您的块。这种服务器端呈现使我们有可能为短代码创建块。

首先，我们将用下面的代码替换我们的 save 方法来返回 null:

```
// Defining the front-end interface
save() {
    // Rendering in PHP
    return null;
}, 
```

我们将使用 *register_block_type* PHP 函数在 PHP:
中注册我们的块类型

```
if ( function_exists( 'register_block_type' ) ) {
    // Hook server side rendering into render callback
    register_block_type(
        'hello-gutenberg/click-to-tweet', [
            'render_callback' => 'hello_gutenberg_block_callback',
            'attributes'      => array(
                'tweet'  => array(
                    'type' => 'string',
                ),
                'tweetsent' => array(
                    'type' => 'string',
                ),
                'button'    => array(
                    'type'  => 'string',
                    'default' => 'Tweet',
                ),
                'theme'  => array(
                    'type'  => 'boolean',
                    'default' => false,
                ),
            ),
        ]
    );
} 
```

我们的 *register_block_type* 函数。我们首先向它传递我们的块名，以及一组参数。
第一个参数是 *render_callback* 函数，它调用我们的*hello _ Gutenberg _ block _ callback*函数进行服务器端渲染。

在我们的渲染回调之后，我们将属性作为数组传递，类似于 block.js 文件，我们在渲染回调函数中使用它。

我们的渲染回调函数是这样的:

```
function hello_gutenberg_block_callback( $attr ) {
    extract( $attr );
    if ( isset( $tweet ) ) {
        $theme = ( $theme === true ? 'click-to-tweet-alt' : 'click-to-tweet' );
        $shortcode_string = '[clicktotweet tweet="%s" tweetsent="%s" button="%s" theme="%s"]';
        return sprintf( $shortcode_string, $tweet, $tweetsent, $button, $theme );
    }
} 
```

我们提取所有的属性，然后在短代码中返回。这就是为古登堡改编你的短代码插件所需要的全部。

你可以在这个 [hello-gutenberg](https://github.com/HardeepAsrani/hello-gutenberg) 资源库中找到本文使用的所有代码。

在下一部分中，我们将看看为 Gutenberg 修改现有插件的其他方法，包括侧边栏 API。

如果你有任何关于如何为古腾堡改编你的插件的问题，请在评论中提问！T3】

*文章最初发表于[CodeinWP.com](https://www.codeinwp.com/blog/adapt-your-plugin-for-gutenberg-block-api/)T3】*