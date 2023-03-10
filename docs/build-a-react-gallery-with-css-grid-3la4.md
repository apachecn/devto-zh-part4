# 用 CSS 网格构建一个反应库

> 原文：<https://dev.to/joelmturner/build-a-react-gallery-with-css-grid-3la4>

CSS Grid 超级强大，可以用来创建一些有创意的布局，这使得它成为创建画廊的一个很好的方式。在本指南中，我们将使用一个简单的网格来保持图像的大小不变。

> 这将是 CSS 网格的一个快速视图。如果你想了解更多，我强烈推荐[韦斯博斯](https://twitter.com/wesbos)的 [CSS 网格课程](https://cssgrid.io/)(免费)。

该组件的要求如下:

*   接受图像数组(使用 Gatsby 图像)
*   需要响应不同尺寸的屏幕(使用`minmax`)
*   拿一个`s`、`m`或者`l`的大小道具

太好了，让我们开始建造吧。我们基于对[盖茨比形象](https://www.gatsbyjs.org/packages/gatsby-image/)的查询来构建这个。您可以为您可能使用的任何图像组件或元素设置此选项。

```
import React from 'react';
import Img from 'gatsby-image';

function Gallery({images = []}) {
  const wrapperStyles = {
    display: 'grid',
    gridTemplateColumns: 'repeat(auto-fill, minmax(300px, 1fr))',
    gridGap: '1rem';
  }
  return (
    <div style={wrapperStyles}>
      {nodes.length > 0 && nodes.map(node => <Img fluid={node.localImage.childImageSharp.fluid} />)}
    </div>
  )
} 
```

包装器样式将根据网格和行声明为子元素提供大小。在这种情况下，我们有`gridTemplateColumns: ‘repeat(auto-fill, minmax(300px, 1fr))’`，它根据最小宽度`300px`和最大宽度`1fr`告诉图像被排列在尽可能多的列中。`gridGap`告诉他们图像之间有一个`1rem`的间隙。

我们将添加一个函数，该函数可以处理来自`size` prop 的不同大小，并将这些传递给包装器样式。

```
import React from 'react';
import Img from 'gatsby-image';

function Gallery({nodes = [], size = 'm'}) {

  function getStylesForSize() {
    switch (size) {
      case 's': 
        return {
          gridTemplateColumns: "repeat(auto-fill, minmax(142px, 1fr))",
          gridGap: "0.25rem",
        }
      case 'm':
      default:
        return {
          gridTemplateColumns: "repeat(auto-fill, minmax(300px, 1fr))",
          gridGap: "0.5rem",
        }
      case 'l': 
        return {
          gridTemplateColumns: "1fr",
          gridGap: ".75rem",
        }
    }
  }

  const wrapperStyles = {
    display: 'grid',
    ...getStylesForSize()
  }

  return (
    <div style={wrapperStyles}>
      {nodes.length > 0 && nodes.map(node => <Img fluid={node.localImage.childImageSharp.fluid} />)}
    </div>
  )
} 
```

现在，我们可以将我们的大小更改传递给 gallery 组件。酷！

## 打字稿

现在，让我们来打字。这将有助于我们和其他人知道我们需要什么形状才能被传到画廊。

```
import React from 'react';
import Img, { FluidObject } from 'gatsby-image';

type GallerySizes = "s" | "m" | "l"
type GalleryImage = {
  node: {
    localImage: {
      childImageSharp: {
        fluid: FluidObject;
      };
    };
    id: string;
  };
}

type GalleryProps = {
  imageNodes: GalleryImage[];
  size?: GallerySizes;
}

function Gallery({images, size = 'm'}: GalleryProps) {

  function getStylesForSize(): React.CSSProperties {
    switch (size) {
      case 's': 
        return {
          gridTemplateColumns: "repeat(auto-fill, minmax(142px, 1fr))",
          gridGap: "0.25rem",
        }
      case 'm':
      default:
        return {
          gridTemplateColumns: "repeat(auto-fill, minmax(300px, 1fr))",
          gridGap: "0.5rem",
        }
      case 'l': 
        return {
          gridTemplateColumns: "1fr",
          gridGap: ".75rem",
        }
    }
  }

  const wrapperStyles: React.CSSProperties = {
    display: 'grid',
    ...getStylesForSize(),
  }

  return (
    <div style={wrapperStyles}>
      {nodes.length > 0 && nodes.map(node => <Img fluid {node.localImage.childImageSharp.fluid} />)}
    </div>
  )
} 
```

不错！现在我们有了一个可以改变大小的功能性图库组件。你可以在我的[插图页面](https://joelmturner.com/illustration)上看到我的实现。