# 使用 TypeScript 第 1 部分在 Gatsby 中创建一个 Avatar 组件

> 原文：<https://dev.to/joelmturner/create-an-avatar-component-in-gatsby-with-typescript-part-1-17he>

在这个系列中，我们将看看如何创建一个简单的 avatar 组件，它是用 TypeScript 键入的，并且在 Gatsby land 中有一些特殊的好东西。

> 我通常从一开始就编写 TypeScript，但我想单独展示每一部分，以防其他人还不熟悉 TypeScript。

让我们首先确定我们对虚拟角色组件的需求。

*   [ ]应该显示图像
*   [ ]应该是圆形的
*   [ ]应该接受图像 URL
*   [ ]应根据名称显示图像(适用于小型器械包)

酷，现在我们可以开始建立我们的头像了。从一个 img 元素开始，使用占位符图像作为 src。加一点造型，让它变圆，给它一个尺寸。

```
function Avatar(props){

    const {url, altText, title} = props;

    const styles = {
        width: '75px',
        height: '75px',
        borderRadius: '50%'
    }

    return (
        <img
            style={styles}
            src={url}
            alt={altText}
            title={title} />
    );
}

export default Avatar; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以给它传递图像 URL 和 alt 文本。我们可以看到该组件现在正在它的基本实现中工作。

```
<Avatar
    url="https://res.cloudinary.com/joelmturner/monster-01.png"
    alText='Monster P. Whittington portrait'
    title='Monster P. Whittington' /> 
```

Enter fullscreen mode Exit fullscreen mode

*   [x]应该显示图像
*   [x]应该是圆形的
*   [x]应该接受图像 URL
*   [ ]应根据名称显示图像(适用于小型器械包)

看起来不错。我们已经满足了该组件的前三个标准。在第二部分，我们会用盖茨比的形象使它更强大。