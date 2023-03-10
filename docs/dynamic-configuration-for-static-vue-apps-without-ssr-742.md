# 无 SSR 的静态 Vue 应用的动态配置

> 原文：<https://dev.to/ilshidur/dynamic-configuration-for-static-vue-apps-without-ssr-742>

本文解释了如何使用 [Docker](https://www.docker.com) & [NGINX](https://www.nginx.com) 让我们的 Vue.js 应用在**运行时**而不是**构建时**接受配置。

**如果您想要解决方案，您可以跳到*解决方案*部分。**

## 为什么？

在 [Scalair](https://www.scalair.fr) ，我们使用 Docker 来部署我们的应用。我们的一些应用是 Vue.js **静态**前端，通过 NGINX 提供服务。我们使用 *Gitlab CI* 来测试&部署我们的代码到*亚马逊 EKS* 。每个 Gitlab 管道构建我们的应用程序，然后将 Docker 图像推送到 Amazon ECR。

我们的 Vue 应用有这样的配置，[感谢 Vue.js 客户端环境变量](https://cli.vuejs.org/guide/mode-and-env.html#using-env-variables-in-client-side-code) :

```
const API_URL = process.env.VUE_APP_API_URL; 
```

Enter fullscreen mode Exit fullscreen mode

由于我们的 Vue 应用程序中缺少[服务器端渲染](https://vuejs.org/v2/guide/ssr.html)，我们过去常常在构建时为 Vue CLI 提供环境变量，以便提供配置，例如:API URL。

我们还使用`docker build --build-arg API_URL=https://api.example.com .` :
构建了一个 Dockerfile 文件

```
# Build stage
FROM mhart/alpine-node:10 as build-stage

WORKDIR /app

COPY package.json package-lock.json .npmrc ./
RUN npm ci

COPY . .

ARG API_URL
ENV VUE_APP_API_URL $API_URL

RUN ./node_modules/.bin/vue-cli-service build --mode production

# Production stage
FROM nginx:1.17.0-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80

CMD nginx -g "daemon off;" 
```

Enter fullscreen mode Exit fullscreen mode

这个多阶段构建将构建静态文件，然后用 NGINX 服务它们。

**我们担心的一个问题是图像的一致性:由于`--build-arg`参数的原因，不同环境下的 Docker 图像会有所不同。`VUE_APP_`环境变量仅在* *构建时**起作用。**

## 解

让 NGINX 容器来完成这项工作吧！

我们用`'{{ API_URL }}'` :
代替了`process.env.VUE_APP_API_URL`

```
// This will act as a "template" that will be replaced with an actual URL.
const API_URL = '{{ API_URL }}'; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们删除了构建参数，并在运行 NGINX 之前替换了`{{ templates }}`。

**最终 Dockerfile :**

```
# Build stage
FROM mhart/alpine-node:10 as build-stage

WORKDIR /app

COPY package.json package-lock.json .npmrc ./
RUN npm ci

COPY . .

RUN ./node_modules/.bin/vue-cli-service build --mode production

# Production stage
FROM nginx:1.17.0-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80

# Using `sed` to replace {{ API_URL }} with the actual API URL,
# which is given to the container at RUN TIME !
CMD sed -i -e "s/{{ API_URL }}/$API_URL/g" /usr/share/nginx/html/js/app.*.js && \
    nginx -g "daemon off;" 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用这些命令运行容器:

```
docker build -t my-vue-app .
docker run -e API_URL=https://api.example.com my-vue-app 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这与其说是一个长期的解决方案，不如说是一个临时的解决方案。

如果你正面临着与我们相同的问题，你肯定应该研究一下[服务器端渲染](https://vuejs.org/v2/guide/ssr.html)。SSR 允许很多行为，这些行为是浏览器中呈现的单个静态应用程序无法完成的。这是动态改变你的前端的一个很好的方法，可以像我们一样防止蹩脚的黑客攻击。