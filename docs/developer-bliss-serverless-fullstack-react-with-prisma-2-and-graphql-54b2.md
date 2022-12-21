# Developer Bliss-无服务器 Fullstack 与 Prisma 2 和 GraphQL 进行交互

> 原文：<https://dev.to/codemochi/developer-bliss-serverless-fullstack-react-with-prisma-2-and-graphql-54b2>

在这篇文章中，我们将展示如何使用 Prisma 2 和 Next.js 部署完全无服务器的堆栈。这种类型的解决方案最近才推出，虽然它仍处于测试阶段，但它确实代表了全堆栈开发人员的天堂，因为你可以开发一个应用程序，部署它，不用担心任何 DevOps 细节，并相信它无论负载如何都会工作。

**好处:**

*   部署整个堆栈的一个命令(现在)
*   无限可扩展，按需付费(lambda 函数)
*   无需维护服务器(lambda 函数)
*   React 的所有优势(可组合性、可重用性和强大的社区支持)
*   SEO 的服务器端渲染(Next.js)
*   正确呈现脸书和 Twitter 中的社交媒体链接份额(Next.js)
*   易于发展的 api (GraphQL)
*   为整个堆栈维护一个模式(Prisma 2)
*   安全保密管理(现在)
*   通过热代码重载(Docker)轻松设置开发环境
*   尽可能自动生成的强类型(GraphQL 和 Typescript)(graph QL-gen)

在开始之前，您应该继续设置一个 RDS 实例，并像我们之前的博客文章那样配置[。](https://www.codemochi.com/blog/2019-08-07-setup-rds-for-zeit-now-deployments/)

**视频:**

*I .安装依赖关系*
[https://www.youtube.com/embed/wxUyz1obiAw](https://www.youtube.com/embed/wxUyz1obiAw)

*二。添加环境参数*
[https://www.youtube.com/embed/gsMgdMbRc_I](https://www.youtube.com/embed/gsMgdMbRc_I)

*三世。配置后端*
[https://www.youtube.com/embed/OzaRwi2_vSA](https://www.youtube.com/embed/OzaRwi2_vSA)

*四。配置现在服务*
[https://www.youtube.com/embed/Kac8RJFtl3A](https://www.youtube.com/embed/Kac8RJFtl3A)

*V .立即设置秘密并部署！*
[https://www.youtube.com/embed/5do8C4rJDp0](https://www.youtube.com/embed/5do8C4rJDp0)

我们将从我们的多部分博客系列[**【1】**](https://www.codemochi.com/blog/2019-07-08-prisma-2-nextjs-docker/)[**【2】**](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/)[**【3】**](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/)中选取例子。如果您对从头开始跟踪不感兴趣，您可以从从`now-serverless-start`标签:
查看回购开始

```
git clone https://github.com/CaptainChemist/blog-prisma2
git fetch && git fetch --tags
git checkout now-serverless-start 
```

一、安装并清理依赖关系

1.  升级到下一版本 9

在`frontend/package.json`中，确保 next 具有“^9.02”或更高版本。以前，我们使用 8.1.1 的 canary 版本来支持类型脚本，但由于 next 的后版本 9 已经发布，所以我们希望确保我们可以利用所有最新的好东西。

1.  将 webpack 安装到前端

作为预防措施，您应该将 webpack 安装到前端文件夹中。我见过与`now`不一致的行为，如果没有安装 webpack，有时部署会失败，说它需要 webpack。当我在网上阅读时，听起来不应该要求它，所以这可能是一个错误，但添加它不会有什么坏处:

```
npm install --save-dev webpack 
```

1.  从`package.json`和`frontend/package.json`上取下主滑车

当我们生成我们的`package.json`文件时，它会自动填充`main`字段。因为我们没有使用这个特性，而且两个文件夹中都没有一个`index.js`文件，所以我们应该删除它们。在`frontend/package.json`中，继续移除线 5。我们以前没有使用它，它有可能混淆`now`服务。

```
"main":  "index.js", 
```

同样，在根文件夹的`package.json`中进行同样的操作。

1.  将 Prisma2 安装到后端

尽管我们在 docker 容器中全局安装了 prisma2，但我们现在需要将它添加到我们的 backend package.json 文件中，这样当我们使用 now 服务时，它将在 AWS 的构建过程中可用。导航到`backend`文件夹并安装 prisma2:

```
npm install --save-dev prisma2 
```

1.  立即安装 Zeit

我们应该全局安装`now`，这样我们就能够从命令行运行它:

```
npm install -g now 
```

**二。添加环境变量**

1.  向项目的根目录添加一个`.env`文件。添加以下变量，我们将在 docker 环境中使用这些变量。

```
MYSQL_URL=mysql://root:prisma@mysql:3306/prisma
BACKEND_URL=http://backend:4000/graphql
FRONTEND_URL=http://localhost:3000 
```

1.  修改`docker-compose.yml`文件，将这些新变量注入我们的 docker 容器。更新后的文件如下所示:

`docker-compose.yml`

```
version: '3.7'
services:
  mysql:
    container_name: mysql
    ports:
      - '3306:3306'
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: prisma
      MYSQL_ROOT_PASSWORD: prisma
    volumes:
      - mysql:/var/lib/mysql
  prisma:
    links:
      - mysql
    depends_on:
      - mysql
    container_name: prisma
    ports:
      - '5555:5555'
    build:
      context: backend/prisma
      dockerfile: Dockerfile
    environment:
      MYSQL_URL: ${MYSQL_URL}
    volumes:
      - /app/prisma
  backend:
    links:
      - mysql
    depends_on:
      - mysql
      - prisma
    container_name: backend
    ports:
      - '4000:4000'
    build:
      context: backend
      dockerfile: Dockerfile
      args:
        - MYSQL_URL=${MYSQL_URL}
    environment:
      MYSQL_URL: ${MYSQL_URL}
      FRONTEND_URL: ${FRONTEND_URL}
    volumes:
      - ./backend:/app
      - /app/node_modules
      - /app/prisma
  frontend:
    container_name: frontend
    ports:
      - '3000:3000'
    build:
      context: frontend
      dockerfile: Dockerfile
    environment:
      BACKEND_URL: ${BACKEND_URL}
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next

volumes: #define our mysql volume used above
  mysql: 
```

让我们来看看被改变的部分，下面是我们添加到上面文件中的被剪掉的部分:

```
prisma:
  environment:
    MYSQL_URL: ${MYSQL_URL}
### ..more lines ###
backend:
  build:
    context: backend
    dockerfile: Dockerfile
    args:
      - MYSQL_URL=${MYSQL_URL}
  environment:
    MYSQL_URL: ${MYSQL_URL}
    FRONTEND_URL: ${FRONTEND_URL}
### ..more lines ###
frontend:
  environment:
    BACKEND_URL: ${BACKEND_URL} 
```

我们向 prisma studio、后端和前端容器添加了环境块。因为我们有了`.env`文件，我们在`.env`文件中定义的任何变量，比如`VAR1=my-variable`，我们可以在 yml 中以\${VAR1}的形式调用它，这就像我们在 yml 文件的那个位置直接使用了`my-variable`字符串一样。

1.  在前端动态设置后端 url

我们需要动态设置前端连接的 uri，而不是硬编码。在`frontend/utils/init-apollo.js`中，我们之前有这样一行代码，如果请求来自用户，它将连接到本地主机；如果请求来自 next.js 服务器，它将连接到后端:

```
uri: isBrowser ? 'http://localhost:4000' : 'http://backend:4000', // Server URL (must be absolute) 
```

在 docker 环境中，我们仍然需要跟踪我们是在浏览器中还是在服务器中。此外，我们需要检查我们是否在 docker 环境中，或者我们是否通过`now`被部署到 lambda 函数中。

我们可以通过使用`process.env.ENVIRONMENTAL_VARIABLE`来访问环境变量。我们检查 url 是否与我们的本地环境 url 匹配，如果匹配，我们知道我们处于 docker 环境中。现在我们的逻辑是，如果我们在 docker 环境中，浏览器发出请求，我们返回 localhost，否则我们传递作为 uri 的`BACKEND_URL`。

`frontend/utils/init-apollo.js`

```
function create(initialState) {
  // Check out https://github.com/zeit/next.js/pull/4611 if you want to use the AWSAppSyncClient
  const isBrowser = typeof window !== 'undefined'
  const isDocker = process.env.BACKEND_URL === 'http://backend:4000/graphql'
  return new ApolloClient({
    connectToDevTools: isBrowser,
    ssrMode: !isBrowser, // Disables forceFetch on the server (so queries are only run once)
    link: new HttpLink({
      uri:
        isDocker && isBrowser
          ? 'http://localhost:4000/graphql'
          : process.env.BACKEND_URL,
      credentials: 'same-origin', // Additional fetch() options like `credentials` or `headers`
      // Use fetch() polyfill on the server
      fetch: !isBrowser && fetch,
    }),
    cache: new InMemoryCache().restore(initialState || {}),
  })
} 
```

现在，这应该就是我们需要做的所有事情了，但是由于 Next.js 同时在服务器和客户机上呈现，我们将无法访问服务器环境变量，除非我们再多做一步。我们需要在我们的`frontend/next.config.js`文件中公开变量:

`frontend/next.config.js`

```
const withCSS = require('@zeit/next-css')

module.exports = withCSS({
  target: 'serverless',
  env: {
    BACKEND_URL: process.env.BACKEND_URL,
  },
}) 
```

注意，由于 Next.js 处理 process.env 的方式，您不能从它析构变量。所以下面的代码行*不会*起作用，我们需要使用整个`process.env.BACKEND_URL`变量。

```
const { BACKEND_URL } = process.env // NO! 
```

**三世。配置我们的后端服务器**

1.  将后端服务器更新到`/graphql`后端并配置 CORS

我们将上面的 url 更新为后端服务器的`/graphql`端点。我们这样做是因为在`now`我们将把我们的后端 graphql 服务器部署到`ourdomain.com/graphql`。我们需要在我们的`backend/src/index.ts`中做这个改变，这样服务器将运行在`/graphql`端点，而不是`/`。

此外，在此期间，我们将禁用订阅并启用 CORS。CORS 代表*跨源资源共享*，它告诉后端服务器应该接受来自哪些前端服务器的请求。这确保了如果其他人建立了指向我们后端服务器的前端下一个服务器，那么所有请求都将失败。我们需要这个，因为你可以想象如果有人买了一个域名`crazyamazondeals.com`(我只是瞎编的)并将他们的前端服务器指向亚马逊购物门户的真正后端服务器，这可能会造成多大的破坏。这将允许一个假的亚马逊前端收集各种客户信息，同时仍然向亚马逊实际的后端服务器发送真正的请求。呀！

为了启用 CORS，我们将通过我们的前端网址。我们还将为将来的身份验证相关目的启用凭据。

`backend/src/index.ts`

```
server.start(
  {
    endpoint: '/graphql',
    playground: '/graphql',
    subscriptions: false,
    cors: {
      credentials: true,
      origin: process.env.FRONTEND_URL,
    },
  },
  () => console.log(`🚀 Server ready`)
) 
```

1.  更新`backend/prisma/project.prisma`文件以使用环境变量并设置我们的平台。

我们可以使用`env("MYSQL_URL")`来获取我们的`MYSQL_URL`环境变量。从 prisma preview-3+开始，我们需要指定我们计划与 prisma2 一起使用的平台。我们的 docker 工作可以使用“native”，但是现在 Zeit 需要使用“linux-glibc-libssl1.0.2”。

`backend/prisma/project.prisma`

```
datasource db {
  provider = "mysql"
  url      = env("MYSQL_URL")
}

generator photon {
  provider  = "photonjs"
  platforms = ["native", "linux-glibc-libssl1.0.2"]
}
// Rest of file 
```

1.  更新`backend/Dockerfile`以将环境变量传递到 prisma2 生成中。我们首先必须使用名为`MYSQL_URL`的`ARG`定义一个 docker 参数。然后，我们将`MYSQL_URL`环境变量赋给这个新创建的`ARG`。

我们需要`MYSQL_URL`环境变量，以便我们来自 prisma 文件的 url 得到正确的评估。

`backend/Dockerfile`

```
FROM node:10.16.0
RUN npm install -g --unsafe-perm prisma2

RUN mkdir /app
WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/

ARG MYSQL_URL
ENV MYSQL_URL "$MYSQL_URL"

RUN npm install
RUN prisma2 generate

CMD ["npm", "start" ] 
```

请注意，我们能够访问这个 docker 文件中的`$MYSQL_URL`变量的唯一原因是我们之前添加到 docker-compose.yml 文件中的一个`args`块。向 docker-compose 的`environment`块添加变量只能在容器运行时访问，而不能在构建步骤访问，构建步骤是执行 Dockerfile 时我们所在的位置。

```
backend:
  build:
    context: backend
    dockerfile: Dockerfile
    args:
      - MYSQL_URL=${MYSQL_URL} 
```

**四。添加我们现在的配置**

1.  立即创建秘密

在本地，我们一直使用`.env`文件来存储我们的秘密。尽管我们将该文件提交给我们的回购，但我们能够这样做的唯一原因是因为那里没有敏感的环境变量。确保如果你在文件中添加了真正的秘密，比如一个 stripe key，你永远都不需要把它提交给 github，否则你就有被泄露的危险！

对于生产，我们需要一种更安全的方法来存储机密。`Now`提供了一个很好的方法来做到这一点:

```
now secret add my_secret my_value 
```

`Now`将加密这些秘密并存储在他们的服务器上，当我们上传我们的应用程序时，我们可以使用它们，但我们无法读取它们，即使我们试图偷偷摸摸地使用 console.logs 读取它们。我们需要为我们的`.env`文件中的以下变量创建变量:

```
MYSQL_URL=mysql://user:password@your-mysql-database-url:3306/prisma
BACKEND_URL=https://your-now-url.sh/graphql
FRONTEND_URL=https://your-now-url 
```

请注意，默认情况下`your-now-url`将是`yourProjecFoldername.yourNowUsername.now.sh`，但是您现在可以跳过这一步，进入本教程的步骤 V，部署您的站点，然后查看它部署到哪里，因为这将是控制台输出的最后一行。然后回到这一步，添加新的秘密并重新部署站点。

1.  向根目录添加一个`now.json`文件

我们需要创建一个`now.json`文件，该文件将规定我们应该如何部署我们的网站的细节。它的第一部分包含了构建和运行时的环境变量。我们将通过使用`@our-secret-name`来使用我们在上一步中创建的秘密。如果你忘记了你用了什么名字，你总是可以输入`now secrets ls`，你将得到秘密的名字(但关键是*而不是*秘密本身)。

接下来，我们必须定义我们的构建步骤。在我们的例子中，我们必须构建 nextjs 应用程序和 graphql-yoga 服务器。nextjs 是使用一个特别设计的`@now/next`构建器构建的，我们只需将它指向我们的`next.config.js`文件，该文件位于我们的`frontend`文件夹中。我们的另一个构建将使用我们的`backend/src`目录中的`index.ts`文件，这个构建器足够聪明，可以将代码编译成 javascript 并部署到 lambda 函数中。

最后，我们必须确定我们的路线。后端服务器将在`/graphql`端点结束，而前端目录将使用其他所有东西。这确保了我们在`ourdomain.com`下访问的任何页面都会被转发到 nextjs 服务器*上，除了*和`/graphql`端点。

`now.json`

```
{  "version":  2,  "build":  {  "env":  {  "MYSQL_URL":  "@mysql_url",  "BACKEND_URL":  "@backend_url",  "FRONTEND_URL":  "@frontend_url"  }  },  "env":  {  "MYSQL_URL":  "@mysql_url",  "BACKEND_URL":  "@backend_url",  "FRONTEND_URL":  "@frontend_url"  },  "builds":  [  {  "src":  "frontend/next.config.js",  "use":  "@now/next"  },  {  "src":  "backend/src/index.ts",  "use":  "@now/node",  "config":  {  "maxLambdaSize":  "20mb"  }  }  ],  "routes":  [  {  "src":  "/graphql",  "dest":  "/backend/src/index.ts"  },  {  "src":  "/(.*)",  "dest":  "/frontend/$1",  "headers":  {  "x-request-path":  "$1"  }  }  ]  } 
```

1.  向根目录添加一个`.nowignore`文件

最后，我们可以添加忽略文件，它会告诉你哪些东西不应该上传。

`.nowignore`

```
**/node_modules
.next
Dockerfile
README.MD 
```

**V .部署我们现在的完整堆栈站点**

这部分很简单。只需从根文件夹中键入`now`，让它飞起来！

从那里来的还有更多！
我创建了一个关于使用 Zeit Now + Next.js 构建食谱分享应用程序的完整课程，所以如果你喜欢这个就去看看吧！
[![Next Chop Image](img/21a03bf315e84ca303952d749ef4b443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAQzV3Rs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://courses.codemochi.com/static/banner-7fcb9baf6ae2814c06c77c89c83d8c59.png) 
[前端无服务器带 React 和 GraphQL](https://courses.codemochi.com/frontend-serverless-with-react-and-graphql)

点击这里给我们你的电子邮件，我们会在发布新内容时通知你。我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时取消订阅。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-08-12-prisma-2-now/) 。