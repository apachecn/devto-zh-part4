# 创建我的第一个 React 包

> 原文：<https://dev.to/tarunmangukiya/creating-my-first-react-package-react-unicons-29a9>

嗨伙计们，

很久以来，我一直想在 React 中工作并创建包。我大部分时间在 VueJS 工作，因此这对我来说是一个新的经历。

最近，我们推出了[unicon-一个图标库](https://iconscout.com/unicons)包含 1000+个独特线条风格的矢量图标作为开源。从那时起，我希望有一个反应包。上周日，我有点空闲&想试试看！我在这里写这篇文章。

## 如何

我脑子里几乎没有关于如何让它可用的想法。其中一些包含:

1.  每个人都容易使用。
2.  基于组件
3.  人们应该能够只加载他们需要的图标，保持包的大小尽可能的小。

我编写了一个脚本，将 SVG 转换成 React 组件。我使用了像`lodash`这样的基于文件夹的方法来保持包的大小更小。每个组件都被编译成不同的 React 组件文件，并被导入到 common `index.js`中，供那些想要使用所有图标的人使用。

下面是`comment`图标的一个示例组件:

```
import React from 'react';
import PropTypes from 'prop-types';

const UilComment = (props) => {
  const { color, size, ...otherProps } = props
  return React.createElement('svg', {
    xmlns: 'http://www.w3.org/2000/svg',
    width: size,
    height: size,
    viewBox: '0 0 24 24',
    fill: color,
    ...otherProps
  }, React.createElement('path', {
    d: 'M12,2A10,10,0,0,0,2,12a9.89,9.89,0,0,0,2.26,6.33l-2,2a1,1,0,0,0-.21,1.09A1,1,0,0,0,3,22h9A10,10,0,0,0,12,2Zm0,18H5.41l.93-.93a1,1,0,0,0,0-1.41A8,8,0,1,1,12,20Z'
  }));
};

UilComment.propTypes = {
  color: PropTypes.string,
  size: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
};

UilComment.defaultProps = {
  color: 'currentColor',
  size: '24',
};

export default UilComment; 
```

我写的构建脚本看起来像这样:

```
const path = require('path')
const fs = require('fs-plus')
const cheerio = require('cheerio')
const upperCamelCase = require('uppercamelcase')

const iconsComponentPath = path.join(process.cwd(), 'icons')
const iconsIndexPath = path.join(process.cwd(), 'index.js')
const uniconsConfig = require('@iconscout/unicons/icons.json')

// Clear Directories
fs.removeSync(iconsComponentPath)
fs.mkdirSync(iconsComponentPath)

const indexJs = []

uniconsConfig.forEach(icon => {
  const baseName = `uil-${icon.name}`
  const location = path.join(iconsComponentPath, `${baseName}.js`)
  const name = upperCamelCase(baseName)
  const svgFile = fs.readFileSync(path.resolve('node_modules/@iconscout/unicons', icon.svg), 'utf-8')

  let data = svgFile.replace(/]+>/gi, '').replace(/<\/svg>/gi, '')
  // Get Path Content from SVG
  const $ = cheerio.load(data, {
    xmlMode: true
  })
  const svgPath = $('path').attr('d')

  const template = `import React from 'react';
import PropTypes from 'prop-types';
const ${name} = (props) => {
  const { color, size, ...otherProps } = props
  return React.createElement('svg', {
    xmlns: 'http://www.w3.org/2000/svg',
    width: size,
    height: size,
    viewBox: '0 0 24 24',
    fill: color,
    ...otherProps
  }, React.createElement('path', {
    d: '${svgPath}'
  }));
}; ${name}.propTypes = {
  color: PropTypes.string,
  size: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
}; ${name}.defaultProps = {
  color: 'currentColor',
  size: '24',
};
export default ${name};`

  fs.writeFileSync(location, template, 'utf-8')

  // Add it to index.js
  indexJs.push(`export { default as ${name} } from './icons/${baseName}'`)
})

fs.writeFileSync(iconsIndexPath, indexJs.join('\n'), 'utf-8')

console.log(`Generated ${uniconsConfig.length} icon components.`) 
```

早些时候我试图使用`webpack`,但由于它将它们编译成一个文件`index.js`,所以不能正常工作。

## 发布为 npm 包

我学会了如何将它发布为 npm 包，这样每个人都可以通过简单的`npm install`来使用它。我已经在 https://www.npmjs.com/package/@iconscout/react-unicons 发表了它

## 使用

您可以通过简单安装
`npm i -s @iconscout/react-unicons`来使用这些图标

然后在组件中导入单个图标。

```
import React from 'react';
import UilReact from '@iconscout/react-unicons/icons/uil-react'

const App = () => {
  return <UilReact size="140" color="#61DAFB" />
};

export default App; 
```

我还添加了道具来定制颜色和尺寸。

我希望开发者社区能给我一些如何改进的建议。:)