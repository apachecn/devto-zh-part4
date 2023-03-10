# 使用 Cloudinary 在 Adonis.js 中上传图像或文件

> 原文：<https://dev.to/massivebrains/image-or-file-uploads-in-adonis-js-with-cloudinary-1b1g>

我刚开始和 adonis JS 合作两个多月。我最近不得不在我的项目中使用 Cloudinary，我寻找最好的方式来松散地使用它，以保持 Adonis 的可读性、表达性和平易近人的结构。

本教程假设您已经熟悉 Adonis 和 Cloudinary。我将在下面的步骤中解释我的方法。所以让我们开始吧！

## 第一步:新建一个 AdonisJS 项目

运行

```
adonis new cloudinary-integration 
```

创建新的 adonis 项目。你应该得到一个新的 adonis 项目(在撰写本文时，我使用的是版本 *4.1* )

## 第二步:设置上传控制器

通过运行
创建一个新的 HTTP 控制器

```
adonis make:controller Upload --type http 
```

并设置如下所示的简单索引方法

```
 'use strict'

class UploadController {

    async index ({ request, response }) {

        return response.json({status: true, data: 'It Works '})
    }
}

module.exports = UploadController 
```

当然，通过添加新路线
来更新`/start/routes.js`

```
'use strict'

const Route = use('Route')

Route.on('/').render('welcome')

//Add this Route
Route.get('/upload', 'UploadController.index') 
```

## 第三步:实现 Cloudinary

此时，如果你还没有 cloudinary 帐户，请前往[https://cloudinary.com](https://cloudinary.com)创建一个免费帐户。我们需要 cloudinary 的三个主要环境变量。

*   云名
*   CLOUDINARY_API_KEY
*   CLOUDINARY_API_SECRET

更新您的`.env`文件，使其包含这三个`environment variables`。请参见下面的屏幕截图，了解如何从您的 cloudinary 控制台获取它们。

[![](img/9bee1ecd7f2095203d52cfa2ac129d45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rb-Ztz3k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jnoeuznjy43dfw8m6cxy.jpg)

所以你的。env 文件现在看起来有点像这个

```
HOST=127.0.0.1
PORT=3333
NODE_ENV=development
APP_URL=http://${HOST}:${PORT}
CACHE_VIEWS=false
APP_KEY=iBRP92LhBPRKh0rTIcIo2OuyQNbWCY6C
DB_CONNECTION=sqlite
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USER=root
DB_PASSWORD=
DB_DATABASE=adonis
SESSION_DRIVER=cookie
HASH_DRIVER=bcrypt

#Cloudinary Credentials
CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret 
```

接下来，让我们从 npm(或 yarn)拉入 cloudinary JS SDK。

```
npm i cloudinary --save 
```

然后，用下面的代码片段创建一个文件`/app/Services/Cloudinary.js`。

```
'use strict'

const cloudinary = use('cloudinary')
const Env = use('Env')

cloudinary.config({

    cloud_name: Env.get('CLOUDINARY_CLOUD_NAME'),
    api_key: Env.get('CLOUDINARY_API_KEY'),
    api_secret: Env.get('CLOUDINARY_API_SECRET'),
})

module.exports = {

    upload: async (file) => {

        return new Promise(async (resolve, reject) => {

            try{

                let response = await cloudinary.uploader.upload(file.tmpPath, { folder: 'test'})

                resolve({status: true, url: response.secure_url })

            }catch(error){

                reject({status: false, url: error.message })
            }
        })
    }
} 
```

我们从名为`upload`的`async`函数返回一个承诺，我们将传递一个`file`对象给它。

## 第四步:使用云端服务

现在我们已经有了 Cloudinary 服务设置。让我们在`app/Http/Controllers/UploadController.js`中创建一个方法来处理上传。

我们的`UploadController.js`现在将看起来像这样

```
'use strict'

//Import Cloudinary
const Cloudinary = use('App/Services/Cloudinary')

class UploadController {

    async index ({ request, response }) {

        return response.json({status: true, data: 'It Works '})
    }

    async upload ({ request, response }) {

        //TODO: Validate. Input.

        try{

            if(request.file('image')){

                let cloudinary_response = await Cloudinary.upload(request.file('image'))

                return response.json(cloudinary_response)
            }

            return response.json({status: false, data: 'Please upload an Image.'})

        }catch(error){

            //console.log(error)
            return response.status(500).json({status: false, error: error.message })
        }

    }
}

module.exports = UploadController 
```

还有一件事，让我们为我们的新方法添加一条路线。我们的 routes 文件现在看起来像这样

```
'use strict'

const Route = use('Route')

Route.on('/').render('welcome')

//Add this Route
Route.get('/upload', 'UploadController.index')
Route.post('/upload', 'UploadController.upload') 
```

## 第五步:邮递员测试

出于测试目的，我们现在不想使用 CSRF 保护。所以在你的`/config/shield.js`中更新`csrf`键如下，所以必须排除`/upload`路线

```
csrf: {
    enable: true,
    methods: ['POST', 'PUT', 'DELETE'],
    filterUris: ['/upload'],
    cookieOptions: {
      httpOnly: false,
      sameSite: true,
      path: '/',
      maxAge: 7200
    }
  } 
```

现在来测试我们的上传路径，并确保我们的实现按预期工作。尝试从 postman 上传文件，如下所示

[![](img/8f88f81db90ed4a761b814747f3ede1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NDme1g7a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oqctjuony55mlanhrgl0.jpg)

您应该能够从您的 cloudinary 媒体库中看到您新上传的文件。

[![](img/bd0a2e624a9331158723b04c2136e0e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ppuJAVHF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6556g3rllzaknc5zj69y.jpg)

那都是乡亲们！