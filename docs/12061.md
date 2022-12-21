# 用 NodeJS 和 Puppeteer 在 AWS Lambda 中生成 PDF

> 原文：<https://dev.to/akirautio/generate-a-pdf-in-aws-lambda-with-nodejs-and-puppeteer-2b93>

最近，我需要解决一个基于数据库内容生成 PDF 文件的问题。由于这些 pdf 不是经常生成的，所以对 24/7 运行的服务没有意义。幸运的是，Google (Functions)和 AWS (Lambda)都有一个事件驱动的服务，只在请求时运行。

最初，我计划使用 Python 和 Reportlab 来完成这个项目，但是到 PostgreSQL 数据库的连接由于[过于复杂而无法配置](https://stackoverflow.com/questions/36607952/using-psycopg2-with-lambda-to-update-redshift-python/36608956#36608956)。对于 NodeJS，我已经做了一个数据库连接的小项目，所以我知道它会工作。

对于 NodeJS，我仍然需要一个包来生成 PDF，我发现了以下选项:

*   [pdf 包](http://pdfkit.org)
*   [PDFMake](https://pdfmake.github.io/docs/)
*   [放松了](https://github.com/RelaxedJS/ReLaXed)
*   [木偶师](https://github.com/GoogleChrome/puppeteer)

我最终为这个项目选择了木偶师。这对于当前的用例来说有点大材小用，但同时，由于 html+css 的基础结构，它更适合未来。

为了让我的生活更轻松，我使用一个无服务器包来处理 AWS Lambda 和 chrome-aws-lambda 的部署，以帮助将 puppeteer 部署到 AWS Lambda。所需依赖项的完整列表如下:

```
"dependencies":  {  "chrome-aws-lambda":  "1.18.1",  "knex":  "0.18.3",  "pg":  "7.11.0",  "pg-hstore":  "2.3.2",  "pug":  "2.0.4",  "puppeteer-core":  "1.18.1",  }  "devDependencies":  {  "serverless":  "1.40.0",  "serverless-apigw-binary":  "0.4.4",  "serverless-offline":  "4.9.4",  } 
```

Enter fullscreen mode Exit fullscreen mode

除了主要需求，我使用 knex、pg 和 pg-hstore 来处理数据库连接，并将 pug 作为模板引擎。对于本地测试，我使用 serverless-offline，为了帮助 lambda 增加二进制代码，我使用 serverless-apigw-binary。

## 创建 lambda 函数

创建 pdf 的过程如下:

1.  获取我们将用来创建报告的数据(在我的例子中，是用 knex 从 db 中获取的)
2.  创建一个包含数据的 html 模板(我在这里使用的是 pug)。
3.  加载木偶师，用木偶师打开 html 文件。
4.  用木偶师生成一个 pdf 页面。
5.  以 base64 字符串形式返回 PDF。

```
'use strict'
const chromium = require('chrome-aws-lambda')
const pug = require('pug')
const fs = require('fs')
const path = require('path')

const knex = require('./src/db')

module.exports.pdf = async (event, context) => {
  const yearMonth = ((event || {}).pathParameters || {}).yearMonth || ''
  const year = yearMonth.length == 7 && yearMonth.substring(0, 4)
  const month = yearMonth.length == 7 && yearMonth.substring(5, 6)

  // Select a date
  const selDate = new Date(year, month)
  const filter = {
    month: selDate.toLocaleString('en', { month: 'long' }),
    year: selDate.getFullYear()
  }

  // 1\. Load database data wiht Knex TODO
  const result = await knex
    .select()
    .from('sales')
    .where({
      year: filter.year,
      month: selDate.getMonth() + 1
    })

  // 2\. Create html
  const template = pug.compileFile('./src/template.pug')
  const html = template({ ...filter, result })

  // 3\. Open puppeteer
  let browser = null
  try {
    browser = await chromium.puppeteer.launch({
      args: chromium.args,
      defaultViewport: chromium.defaultViewport,
      executablePath: await chromium.executablePath,
      headless: chromium.headless
    })

    const page = await browser.newPage()
    page.setContent(html)

    // 4\. Create pdf file with puppeteer
    const pdf = await page.pdf({
      format: 'A4',
      printBackground: true,
      margin: { top: '1cm', right: '1cm', bottom: '1cm', left: '1cm' }
    })

    // 5\. Return PDf as base64 string
    const response = {
      headers: {
        'Content-type': 'application/pdf',
        'content-disposition': 'attachment; filename=test.pdf'
      },
      statusCode: 200,
      body: pdf.toString('base64'),
      isBase64Encoded: true
    }
    context.succeed(response)
  } catch (error) {
    return context.fail(error)
  } finally {
    if (browser !== null) {
      await browser.close()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 部署到 AWS lambda

如前所述，我们使用[无服务器](https://serverless.com/)进行部署，这样配置就不会太重。

```
service:
  name: PDF

plugins:
  - serverless-offline
  - serverless-apigw-binary

provider:
  name: aws
  runtime: nodejs8.10
  region: eu-central-1
  stage: ${opt:stage, 'development'}
  environment:
    ENV: ${self:provider.stage}

custom:
  apigwBinary:
    types:
      - '*/*'

functions:
  pdf:
    handler: pdf.pdf
    events:
      - http:
          path: pdf
          method: get
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是我们为 apigwBinary 启用了 */* ，以便 PDF 以正确的格式通过。

这里我们有在 AWS lambda 中生成 PDF 的所有东西。在我看来，生成 1024 MB 的 pdf 需要大约 4000 毫秒，这意味着在免费层之后，总价格将接近每 20000 代 PDF 1 欧元。

如果你想自己尝试一下，我已经为 Github 创建了一个[库](https://github.com/ARautio/aws-lambda-pdf-generator-puppeteer)。