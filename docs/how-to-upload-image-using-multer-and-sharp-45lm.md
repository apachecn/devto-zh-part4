# 如何使用 multer 和 sharp 上传图片

> 原文：<https://dev.to/mkilmer/how-to-upload-image-using-multer-and-sharp-45lm>

当我开始我的开发之旅时，我遇到了一些困难，阅读文档就是其中之一。所以我有了写一些我已经学过的东西的想法，这些东西在开始时我有困难

* * *

**`NOTE`** :我知道你已经有了使用 [express.js](https://github.com/expressjs/express) 和节点开发构建服务器的经验

# **关于 multer**

[Multer](https://www.npmjs.com/package/multer) 是一个处理多部分/表单数据的 node.js 中间件，主要用于上传文件。 **`IMPORTANT`** : Multer 将*而非*处理任何非多部分`(multipart/form-data)`的表单。

## 安装

`npm install multer`

# **关于锐**

[Sharp](https://www.npmjs.com/package/sharp) 的典型用例是将普通格式的大图像转换成尺寸不同的更小的、**网页友好的** JPEG、PNG 和 WebP 图像。

## 安装

`npm install sharp`

# **项目结构**

├── index.js | **主应用程序文件**
├──uploads | **图片上传文件夹**
└──调整大小| **图片调整大小上传文件夹**
├──上传-config.js | **multer 配置**
├── node_modules | **应用程序的模块**
├──package-lock . JSON |
├──package . JSON |**列出您的项目的包**

# **手上的代码**

对于测试，建议您使用一些 API 测试程序，如 Insomia 或 Postman。

**`IMPORTANT`** :不要忘记在 API 测试程序的请求体中设置**多部分形式**架构。

index . js〔t0〕

```
 const express = require('express')
const multer = require('multer')
const sharp = require('sharp')
const storage = require('./upload-config')
const upload = multer(storage)
const path = require('path')
const fs = require('fs')
const app = express()

const router = new express.Router
app.use(router)

router.get('/', (req, res) => {
    res.send('ok')
})
router.post('/upload',upload.single('image') ,async (req, res) => {
   const { filename: image } = req.file 

   await sharp(req.file.path)
    .resize(500)
    .jpeg({quality: 50})
    .toFile(
        path.resolve(req.file.destination,'resized',image)
    )
    fs.unlinkSync(req.file.path)

    return res.send('SUCCESS!')
})
app.listen(3333, () => {
    console.log('server on!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

├──上传-config.js

```
const multer = require('multer')
const path = require('path')

module.exports = {
    storage : new multer.diskStorage({
        destination : path.resolve(__dirname, ".","uploads"),
        filename : function(req, file, callback) {
            callback(null, file.originalname)
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

# **结论**📣

我非常感谢能够分享我获得的一些知识，因为教学是学习的最好方式。任何提示或问题，请通过脸书联系我: [Kilmer](https://www.facebook.com/bacon.sleep) 。
**`PS: forgive me for something this is my first post`**
**储存在 GitHub:[https://github.com/mkilmerr/multer-sharp-upload-image](https://github.com/mkilmerr/multer-sharp-upload-image)**