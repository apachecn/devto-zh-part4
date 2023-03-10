# 在 GitLab CI 中使用 Cypress 与 Django 和 Vue 进行集成测试

> 原文：<https://dev.to/briancaffey/using-cypress-with-django-and-vue-for-integration-testing-in-gitlab-ci-461>

本文提供了我在一个使用 GitLab CI 的 a Django + VueJS 应用程序中使用 Cypress 进行集成测试的尝试的高级描述。[这里是 GitLab repo](https://gitlab.com/verbose-equals-true/django-postgres-vue-gitlab-ecs) ，我将参考和复制下面的代码样本。

我最近在一个项目中使用 GitLab CI 开发 CI/CD 管道，该项目使用 Django REST 框架、Celery、Celery Beat 和 Django 通道作为后端，并有一个用 [Quasar](https://quasar.dev) 制作的独立静态前端站点，这是一个非常棒的 Vue 框架和组件库。以下是我的管道阶段的概述:

*   **文档**:将一个静态 VuePress 文档站点部署到我的 GitLab 组下的 GitLab 页面。
*   **测试**:使用 flake8、pytest、eslint 和 jest 对 python 和 javascript 进行林挺和单元测试。
*   **Build** :在**测试**阶段通过所有测试后，为 Vue 前端构建静态资产，为后端应用构建镜像。这使用了稍后讨论的多阶段 docker 文件。
*   **集成**:使用静态文件、后端 docker 镜像、postgres 和 redis 运行集成测试。这使用 Cypress 来无标题地运行，但是每个测试的视频都被记录并存储在 GitLab 中作为作业工件。
*   **Release** :用 commit SHA 标记图像，并将其从 GitLab 的注册中心推送到生产注册中心(弹性容器注册中心或 ECR)
*   **部署到 staging** :将静态文件从构建阶段同步到 CloudFront 发行版上提供的 S3 存储桶，并使用提交 SHA 更新 CloudFormation 堆栈，以触发 ECS 进行滚动更新，该更新将在 ECS 集群中运行的不同服务(django、celery、beat 和 channels)中使用新的 docker 映像。如果迁移文件夹中有更改，可以选择在 ECS 中运行数据库迁移任务；如果静态目录中有更改，可以选择运行 collectstatic。
*   **部署到生产**(尚未实现):与前一阶段相同，但应用于生产云架构堆栈。

## 足下有柏

Cypress 允许你用 [`cy.server()`](https://docs.cypress.io/api/commands/server.html) 轻松模拟服务器调用。我第一次尝试使用 Cypress 时嘲笑了所有后端调用，只测试了 Vue 应用。如果您有一个经过良好测试的简单后端，这种方法可能就足够了。下面是 GitLab CI 工作的样子:

```
.test e2e:
  image: cypress/base:10
  stage: test
  script:
    - cd frontend
    - npm install
    - apt install httping
    - npm run serve &
    - while ! httping -qc1 http://localhost:8080/login ; do sleep 1 ; done
    - $(npm bin)/cypress run 
```

Enter fullscreen mode Exit fullscreen mode

这利用了包含所有依赖项的官方 Cypress 基础映像。`npm run serve &`在后台启动开发服务器，然后在开始测试之前等待它与`httping`一起可用。

## 使用坞站-与坞站复合

一种流行的集成测试方法是使用 docker-compose。这里有一个来自 testdriven.io 的例子[，它被用在一个 Flask/React 应用程序中:](https://github.com/testdrivenio/testdriven-app-2.4/blob/master/test-ci.sh#L33) 

```
# run e2e tests
e2e() {
  docker-compose -f docker-compose-stage.yml up -d --build
  docker-compose -f docker-compose-stage.yml exec users python manage.py recreate_db
  ./node_modules/.bin/cypress run --config baseUrl=http://localhost --env REACT_APP_API_GATEWAY_URL=$REACT_APP_API_GATEWAY_URL,LOAD_BALANCER_DNS_NAME=$LOAD_BALANCER_DNS_NAME
  inspect $? e2e
  docker-compose -f docker-compose-$1.yml down
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的方法是:

1.  启动服务
2.  种子数据库
3.  对 docker-compose 堆栈运行 cypress 测试

这种方法允许我们将应用程序的每个部分分离到它自己的容器中。它还允许我们使用 docker-compose 轻松地在本地运行我们的集成测试。

我采用了类似的方法。我是这样把它组合起来的。首先，GitLab CI 作业定义如下:

```
e2e cypress tests with docker-compose:
  stage: integration
  image: docker:stable
  variables:
    DOCKER_HOST: tcp://docker:2375
    DOCKER_DRIVER: overlay2
  services:
    - docker:dind
  before_script:
    - apk add --update py-pip
    - pip install docker-compose~=1.23.0
  script:
    - sh integration-tests.sh
  artifacts:
    paths:
      - cypress/videos/
      - tests/screenshots/
    expire_in: 7 days 
```

Enter fullscreen mode Exit fullscreen mode

在这个工作定义中有很多设置，但是`script`阶段是所有事情发生的地方。这里是`integration-tests.sh` :

```
#!/bin/bash

set -e

echo "Starting services"
docker-compose -f docker-compose.ci.yml up -d --build

echo "Running tests"
docker-compose -f docker-compose.ci.yml -f cypress.yml up --exit-code-from cypress

echo "Tests passed. Stopping docker compose..."
docker-compose -f docker-compose.ci.yml -f cypress.yml down 
```

Enter fullscreen mode Exit fullscreen mode

使用`--exit-code-from`是一个有用的标志，它允许我们在一个单独的 docker-compose 文件中定义的单独容器中运行 Cypress，并根据来自`cypress`容器的退出代码从 docker-compose 命令中退出，如果测试成功通过，这个退出代码应该是`0`。如果 Cypress 失败，由于`set -e`，这个脚本将以非零退出代码退出。

下面是`cypress.yml`文件:

```
version: '3.7'
services:
  cypress:
    image: "cypress/included:3.4.0"
    container_name: cypress
    networks:
      - main
    depends_on:
      - nginx
    environment:
      - CYPRESS_baseUrl=http://nginx
    working_dir: /e2e
    volumes:
      - ./:/e2e 
```

Enter fullscreen mode Exit fullscreen mode

`cypress/included:3.4.0`镜像已经安装了 Cypress，它的默认命令是运行 Cypress，所以我们不需要定义`command`。

我们使用`http://nginx`作为 Cypress 的`baseUrl`,因为我们要接触服务于我们的 Vue 应用程序的`nginx`容器。nginx 应用程序然后通过向`http://backend`发出请求来达到`backend`容器。

下面是`docker-compose.ci.yml`文件:

```
version: '3.7'
services:
  postgres:
    container_name: postgres
    image: postgres
    networks:
      - main
    volumes:
      - pg-data:/var/lib/postgresql/data

  backend: &backend
    container_name: backend
    build:
      context: ./backend
      dockerfile: scripts/prod/Dockerfile
    command: /start_ci.sh
    networks:
      - main
    volumes:
      - ./backend:/code
      - django-static:/code/static
    depends_on:
      - postgres
    environment:
      - SECRET_KEY='secret'
      - DEBUG=True
      - DJANGO_SETTINGS_MODULE=backend.settings.gitlab-ci

  asgiserver:
    <<: *backend
    container_name: asgiserver
    entrypoint: /start_asgi.sh
    volumes:
      - ./backend:/code

  nginx:
    container_name: nginx
    build:
      context: .
      dockerfile: nginx/ci/Dockerfile
    ports:
      - 80:80
    networks:
      - main
    volumes:
      - django-static:/usr/src/app/static
    depends_on:
      - backend

  redis:
    image: redis:alpine
    container_name: redis
    volumes:
      - redis-data:/data
    networks:
      - main

volumes:
  django-static:
  portainer-data:
  pg-data:
  redis-data:

networks:
  main:
    driver: bridge 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上不需要将`asgiserver`和`backend`作为单独的容器运行，但是我想这样测试，因为它非常类似于我计划在生产中使用的设置。在`asgiserver`容器中启动的服务器`daphne`，能够服务常规的 http 请求

## GitLab 服务

这允许我通过简单地运行`./integration-tests.sh`在本地运行测试。虽然一切都在本地通过了，但是 websocket 测试没有通过 GitLab CI，尽管在 Cypress 中进行了大量的调试、手动等待和其他努力。虽然这可能适用于大多数情况，但我对寻找另一种不使用`docker-in-docker` ( `dind`)或`docker-compose`的解决方案很感兴趣。

GitLab 有一个`services`特性，允许您定义在 CI 作业中运行的容器，主容器可以访问这些容器。例如，主容器中的`redis://redis:6379/0`可以访问`redis`服务，类似于 docker-compose 中的网络工作方式。下面是我定义的 GitLab 作业，尝试使用与 docker-compose 设置类似的方法，但不使用 docker-compose，而是使用`services` :

```
.e2e: &e2e
  image: cypress/base:8
  stage: integration
  variables:
    # variables passed as env vars to *all services*
    SECRET_KEY: 'secret'
    DEBUG: ''
    DJANGO_SETTINGS_MODULE: 'backend.settings.gitlab-ci'
    CELERY_TASK_ALWAYS_EAGER: 'True'
  services:
    - name: postgres
    - name: $CI_REGISTRY_IMAGE/backend:latest
      alias: backend
      command: ["/start_ci.sh"]
    - name: redis
    - name: $CI_REGISTRY_IMAGE/frontend:latest
      alias: frontend
  before_script:
    - npm install --save-dev cypress
    - $(npm bin)/cypress verify
  script:
    - $(npm bin)/cypress run --config baseUrl=http://frontend
  after_script:
    - echo "Cypress tests complete"
  artifacts:
    paths:
      - cypress/videos/
      - cypress/screenshots/
    expire_in: 7 days 
```

Enter fullscreen mode Exit fullscreen mode

这个不管用。在 Cypress 和 GitLab 中进行了大量的调试和提出问题后，我遇到了[这个合并请求](https://gitlab.com/gitlab-org/gitlab-runner/merge_requests/1041)，并发现其他用户面临同样的问题。这种方法的问题是服务对于 GitLab CI 作业中定义的其他服务不可用。如果他们在未来的版本中，这样的东西可能会工作，但现在我需要另一种方法。

此时，我开始搜索在 GitLab 上进行 Cypress 测试的其他项目。Gitter 就是一个很酷的例子。是 GitLab 收购的一家公司，而且是开源的。[e2e CI 的工作](https://gitlab.com/gitlab-org/gitter/webapp/blob/develop/.gitlab-ci.yml#L73)激发了我在 e2e 赛普拉斯测试的下一次尝试:

```
.test_e2e_job: &test_e2e_job
  <<: *test_job
  variables:
    <<: *test_variables
    ENABLE_FIXTURE_ENDPOINTS: 1
    DISABLE_GITHUB_API: 1
    NODE_ENV: test-docker
  script:
    # Cypress dependencies https://docs.cypress.io/guides/guides/continuous-integration.html#Dependencies
    - apt-get update -q -y
    - apt-get --yes install xvfb libgtk2.0-0 libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2
    # Create `output/assets/js/vue-ssr-server-bundle.json`
    - npm run task-js
    # Start the server and wait for it to come up
    - mkdir -p logs
    - npm start > logs/server-output.txt 2>&1 & node test/e2e/support/wait-for-server.js http://localhost:5000
    # Run the tests
    - npm run cypress -- run --env baseUrl=http://localhost:5000,apiBaseUrl=http://localhost:5000/api
  artifacts:
    when: always
    paths:
      - logs
      - test/e2e/videos
      - test/e2e/screenshots
      - cypress/logs
    expire_in: 1 day
  retry: 2 
```

Enter fullscreen mode Exit fullscreen mode

下面是`*test_job`部分:

```
.test_job: &test_job
  <<: *node_job
  variables:
    <<: *test_variables
  stage: build_unit_test
  services:
    - name: registry.gitlab.com/gitlab-org/gitter/webapp/mongo:latest
      alias: mongo
    - name: redis:3.0.3
      alias: redis
    - name: registry.gitlab.com/gitlab-org/gitter/webapp/elasticsearch:latest
      alias: elasticsearch
    - name: neo4j:2.3
      alias: neo4j
  script:
    - make ci-test 
```

Enter fullscreen mode Exit fullscreen mode

我们也来看看`*node_job` :

```
.node_job: &node_job
  image: registry.gitlab.com/gitlab-org/gitter/webapp
  before_script:
    - node --version
    - npm --version
    - npm config set prefer-offline true
    - npm config set cache /npm_cache
    - mv /app/node_modules ./node_modules
    - npm install
  artifacts:
    expire_in: 31d
    when: always
    paths:
      - /npm_cache/
      - npm_cache/ 
```

Enter fullscreen mode Exit fullscreen mode

这个 CI 工作有很多事要做。如果你以前没有使用过 YAML 锚，这个想法就是让我们引用带有`job: &job`的键。参见[这篇文章](https://learnxinyminutes.com/docs/yaml/)了解更多关于 YAML 主播的信息。为了可读性，让我们把锚合并成一个键:

```
.test_e2e_job: &test_e2e_job
  image: registry.gitlab.com/gitlab-org/gitter/webapp
  before_script:
    - node --version
    - npm --version
    - npm config set prefer-offline true
    - npm config set cache /npm_cache
    - mv /app/node_modules ./node_modules
    - npm install
  variables:
    <<: *test_variables
    ENABLE_FIXTURE_ENDPOINTS: 1
    DISABLE_GITHUB_API: 1
    NODE_ENV: test-docker
  services:
    - name: registry.gitlab.com/gitlab-org/gitter/webapp/mongo:latest
      alias: mongo
    - name: redis:3.0.3
      alias: redis
    - name: registry.gitlab.com/gitlab-org/gitter/webapp/elasticsearch:latest
      alias: elasticsearch
    - name: neo4j:2.3
      alias: neo4j
  script:
    # Cypress dependencies https://docs.cypress.io/guides/guides/continuous-integration.html#Dependencies
    - apt-get update -q -y
    - apt-get --yes install xvfb libgtk2.0-0 libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2
    # Create `output/assets/js/vue-ssr-server-bundle.json`
    - npm run task-js
    # Start the server and wait for it to come up
    - mkdir -p logs
    - npm start > logs/server-output.txt 2>&1 & node test/e2e/support/wait-for-server.js http://localhost:5000
    # Run the tests
    - npm run cypress -- run --env baseUrl=http://localhost:5000,apiBaseUrl=http://localhost:5000/api
  artifacts:
    when: always
    paths:
      - logs
      - test/e2e/videos
      - test/e2e/screenshots
      - cypress/logs
    expire_in: 1 day
  retry: 2 
```

Enter fullscreen mode Exit fullscreen mode

关于这份工作，以下是一些需要提及的要点:

*   这项工作从主`webapp`容器(一个 express 应用程序)的基本映像开始。

*   支持`services`在`services`部分定义:mongo、redis、elasticsearch 和 neo4j。但是这些服务之间没有通信；在`webapp`容器和单个服务之间只有通信。

*   不是从 Cypress 映像开始，而是将 Cypress 及其依赖项安装在容器的`script`部分。柏树安装在`package.json`的`devDependencies`。

*   作业被设置为`retry`两次。有时，e2e 测试可能是古怪的。在我使用 Cypress 的经历中，我确实注意到了这一点。

现在让我们来看看我从这个例子中采用的方法。有两个主要部分:GitLab CI 作业和 T2 多阶段 Dockerfile 文件。我需要在同一个容器中为后端 Django 应用程序和 Vue 前端提供服务，即使这些服务在生产中是独立的。这是一个多阶段 docker 文件的完美用例。以下是我的文档中各个阶段的概述:

1.  构建静态资产
2.  构建生产后端 docker 映像
3.  启动`FROM`生产映像，`COPY`Vue 应用程序到`static`文件夹并安装 Cypress 依赖项。

这里是 Dockerfile:

```
# build stage that generates quasar assets
FROM node:10-alpine as build-stage
ENV HTTP_PROTOCOL http
ENV WS_PROTOCOL ws
ENV DOMAIN_NAME localhost:9000
WORKDIR /app/
COPY quasar/package.json /app/
RUN npm cache verify
RUN npm install -g @quasar/cli
RUN npm install --progress=false COPY quasar /app/
RUN quasar build -m pwa

# this image is tagged and pushed to the production registry (such as ECR)
FROM python:3.7 as production
ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1
RUN mkdir /code
WORKDIR /code
COPY backend/requirements/base.txt /code/requirements/
RUN python3 -m pip install --upgrade pip
RUN pip install -r requirements/base.txt
COPY backend/scripts/prod/start_prod.sh \
    backend/scripts/dev/start_ci.sh \
    backend/scripts/dev/start_asgi.sh \
    /
ADD backend /code/

# this stage is used for integration testing
FROM production as gitlab-ci
# update and install nodejs
COPY --from=build-stage /app/dist/pwa/index.html /code/templates/
COPY --from=build-stage /app/dist/pwa /static
COPY cypress.json /code
RUN mkdir /code/cypress
COPY cypress/ /code/cypress/
RUN apt-get -qq update && apt-get -y install nodejs npm
RUN node -v
RUN npm -v
# cypress dependencies
RUN apt-get -qq install -y xvfb \
  libgtk-3-dev \
  libnotify-dev \
  libgconf-2-4 \
  libnss3 \
  libxss1 \
  libasound2 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看看 GitLab CI 作业:

```
e2e: &cypress
  stage: integration
  image: $CI_REGISTRY_IMAGE/backend:latest
  services:
    - postgres:latest
    - redis:latest
  variables:
    SECRET_KEY: 'secret'
    DEBUG: 'True'
    CELERY_TASK_ALWAYS_EAGER: 'True'
  before_script:
    - python backend/manage.py migrate
    - python backend/manage.py create_default_user
    - cp /static/index.html backend/templates/
    - /start_asgi.sh &
  script:
    - npm install cypress
    - cp cypress.json backend/
    - cp -r cypress/ backend/cypress
    - cd backend
    - $(npm bin)/cypress run
  artifacts:
    paths:
      - backend/cypress/videos/
      - backend/cypress/screenshots/
    expire_in: 7 days 
```

Enter fullscreen mode Exit fullscreen mode

这个任务从上面 Dockerfile 文件创建的`backend:latest`图像开始。它引用了`postgres`和`redis`服务。`before_script`运行数据库迁移，用用户播种数据库，并将`index.html`复制到`templates`文件夹。最后，我们在后台运行`start_asgi.sh &`。接下来，我们安装 cypress 并运行测试。

我们不需要为 Django 后端、celery 和 daphne 服务(Django 通道 ASGI 服务器)使用不同的容器，我们可以只使用 daphne 来服务 HTTP 和 Websocket 流量，并且我们可以将`CELERY_TASK_ALWAYS_EAGER`设置为`True`,以便 celery 任务在我们的 e2e 测试中同步运行。我们可以将以下内容添加到我们的`urls.py`中来服务`index.html`和其他静态文件，以服务来自 Django 容器的 Vue 应用程序:

```
if settings.DEBUG:
    import debug_toolbar # noqa
    urlpatterns = urlpatterns + [
        path('', index_view, name='index'),
        path('admin/__debug__/', include(debug_toolbar.urls)),
        # catch all rule so that we can navigate to
        # routes in vue app other than "/"
        re_path(r'^(?!js)(?!css)(?!statics)(?!fonts)(?!service\-worker\.js)(?!manifest\.json)(?!precache).*', index_view, name='index') # noqa
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我们还将`STATIC_ROOT`设置为`/`，并禁用`CsrfViewMiddleware`。这些设置可以在`gitlab-ci.py`中找到。

这里有一个简单的`index_view`，它在对`/`的请求中为`index.html`文件提供服务，或者任何其他不以我们的`STATIC`文件夹中的任何内容开始的路径。这可以在`core/views.py` :
中找到

```
# Serve Vue Application via template for GitLab CI index_view = never_cache(TemplateView.as_view(template_name='index.html')) 
```

Enter fullscreen mode Exit fullscreen mode

GitLab CI 我很喜欢的一个功能是 GitLab runner。这是另一个开源项目，它允许我们在本地运行 GitLab CI 作业，就像您将代码推送到 gitlab.com 并在公共运行器上触发作业一样。当您在本地调试 CI 作业，并且不想一直将代码推送到 gitlab.com 来运行管道时，这非常有用。

在本文的最后一部分，我不想描述我们如何使用 GitLab runner 在本地测试 GitLab CI 作业。

为了在本地运行这项工作，我们确实需要做一项更改。让我们定义一个新的作业，它使用现有作业的锚，但是覆盖`image`键:

```
# use this test with gitlab-runner locally
e2e-local:
  <<: *cypress
  image: localhost:5000/backend:latest 
```

Enter fullscreen mode Exit fullscreen mode

在我的 repo 中，这个作业是通过在作业名称前面加一个句点(`.e2e-local`)来注释的。我不想在生产环境中运行这个作业，所以我需要在本地运行时取消对作业的注释，然后在我想将代码推送到 GitLab 时重新推荐它。

本地测试只需要几个步骤:设置本地注册表、构建映像、标记映像并将其推送到本地注册表。以下是如何做到这一点。运行以下命令([取自 docker 文档](https://docs.docker.com/registry/deploying/) ):

```
docker run -d -p 5000:5000 --restart=always --name registry registry:2 
```

Enter fullscreen mode Exit fullscreen mode

要构建我们将在测试中使用的生产映像，请运行以下命令:

```
docker-compose -f compose/test.yml build backend 
```

Enter fullscreen mode Exit fullscreen mode

然后用下面的命令标记图像:

```
docker tag compose_backend:latest localhost:5000/backend:latest 
```

Enter fullscreen mode Exit fullscreen mode

然后将标记的图像推送到本地注册表:

```
docker push localhost:5000/backend:latest 
```

Enter fullscreen mode Exit fullscreen mode

最后，提交您当前所做的任何更改。Gitlab runner 要求您在运行测试之前提交更改。使用以下命令运行 GitLab CI 作业:

```
gitlab-runner exec docker e2e-local 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这是对我正在进行的 CI/CD 管道的一个快速浏览，并仔细观察了集成阶段。我很想听听其他人在类似技术堆栈的项目中进行集成测试的方法。如果你对我如何改进我做集成测试的方式有任何建议，我很乐意听听你的想法！感谢阅读。