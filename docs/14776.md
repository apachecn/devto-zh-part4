# 出色的静态网站插件，让你手舞足蹈

> 原文：<https://dev.to/jaredwolff/how-to-setup-worry-free-blog-comments-in-20-simple-steps-5cdk>

[![Host Your Own Blog Comments @ jaredwolff.com](img/0d05b0526fb39fa1ab5c593a69991da3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29PIDyl1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Copy_of_Static_Site_Docker_Recipes-2-064c6683-f09a-4993-84aa-7f92e9485239.jpg)

隐私。

性能。

光彩照人的容貌。

你能三者兼得吗？

(当然！)

拥有一个静态生成的博客很棒。许多人使用像 Disqus 和 Google Analytics 这样的服务来使他们变得更好。如果你是他们中的一员就不足为奇了！隐私问题是每个人关注的焦点。所以，与其保持现状，不如是时候做点什么了！

如果你一直在寻求保护你的网站访问者的隐私和提高性能，这篇博文就是为你准备的。

在本文中，我们将使用 DigitalOcean 的 Docker droplet。它允许您在一台(虚拟)机器上托管多个不同的应用程序/服务。结束时，你将知道如何使用 Commento 运行你自己的评论服务器。另外，我会分享一些我在这个过程中学到的技巧，让你更容易掌握。

李 eets 走！

## 反向代理

这个设置最重要的一个方面是反向代理。反向代理的作用类似于路由器。对某个域的请求进来。该请求然后被路由到与该域相关联的服务。

这是 Nginx 反向代理的图表+让我们加密助手文档。这将有助于说明这个想法。

[![Nginx Reverse Proxy with Let's Encrypt](img/a886447ca976c90dc17911353a545773.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NOgGjZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/webproxy-1f1c7540-4b86-4478-bb3e-f05043d671a5.jpg)

另一个好处是对外界多了一层保护。您的网站运行在一个私有网络中，唯一的访问是通过 Nginx 反向代理。将您的 DNS 指向服务器，Nginx 会处理所有的魔法。

以下是如何设置它:

1.  继续设置你的数字海洋水滴。你需要的所有信息都在这里。5 美元的版本已经足够了。
2.  [转到这里克隆存储库。](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion)您也可以在您的终端上运行。确保你首先进入你的数字海洋水滴！

    ```
    git clone git@github.com:evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git 
    ```

3.  将目录更改为克隆的存储库。

4.  将`.env.sample`复制到`.env`并更新里面的值。我不得不把`IP`的值改成我的数字海洋水滴的 IP。我把其他的都留下了。

5.  运行`docker-compose up -d`开始一切。(您可以在没有`-d`选项的情况下运行，以确保一切正常开始。或者您可以使用`docker container logs -f <container name`附加日志输出

将子域指向此服务器时，请确保使用 A 记录。这是我的一个例子:

[![NS1 A Record Configuration](img/ab17fb7369a28c6f0691eab5815131a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ayX7JvbX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-9c0432cd-4d40-4c89-88f3-24037d915eaf.52.32_PM.png)

根据您的 DNS 提供商，您必须弄清楚如何设置 A 记录。但是这超出了本文的目的！

## 用 Docker 编写设置注释

[![Commento Logo with Docker Logo](img/712be95091dd7c4a3f5c1a3763d9076f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hEtMY5p3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Compose-1c868832-6819-43e2-8696-ab698a10dbee.jpg)

这是我目前用于 Commento 的 docker 撰写文件。它还包括一些用于配置 Github、Gitlab 和 Google 的环境变量。它还包括用于设置 SMTP 设置的环境变量。这些参数很重要。否则，您将无法收到密码重置或审核电子邮件！

```
version: '3'

services:
  commento:
    image: registry.gitlab.com/commento/commento
    container_name: commento
    restart: always
    environment:
      COMMENTO_ORIGIN: https://${COMMENTS_URL}
      COMMENTO_PORT: 8080
      COMMENTO_POSTGRES: postgres://postgres:postgres@postgres:5432/commento?sslmode=disable
      COMMENTO_SMTP_HOST: ${SMTP_HOST}
      COMMENTO_SMTP_PORT: ${SMTP_PORT}
      COMMENTO_SMTP_USERNAME: ${SMTP_USERNAME}
      COMMENTO_SMTP_PASSWORD: ${SMTP_PASSWORD}
      COMMENTO_SMTP_FROM_ADDRESS: ${SMTP_FROM_ADDRESS}
      COMMENTO_GITHUB_KEY: ${COMMENTO_GITHUB_KEY}
      COMMENTO_GITHUB_SECRET: ${COMMENTO_GITHUB_SECRET}
      COMMENTO_GITLAB_KEY: ${COMMENTO_GITLAB_KEY}
      COMMENTO_GITLAB_SECRET: ${COMMENTO_GITLAB_SECRET}
      COMMENTO_GOOGLE_KEY: ${COMMENTO_GOOGLE_KEY}
      COMMENTO_GOOGLE_SECRET: ${COMMENTO_GOOGLE_SECRET}
      COMMENTO_TWITTER_KEY: ${COMMENTO_TWITTER_KEY}
      COMMENTO_TWITTER_SECRET: ${COMMENTO_TWITTER_SECRET}
      VIRTUAL_HOST: ${COMMENTS_URL}
      VIRTUAL_PORT: 8080
      LETSENCRYPT_HOST: ${COMMENTS_URL}
      LETSENCRYPT_EMAIL: ${EMAIL}
    depends_on:
      - postgres
    networks:
      - db_network
      - webproxy

  postgres:
    image: postgres
    container_name: postgres
    environment:
      POSTGRES_DB: commento
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    networks:
      - db_network
    volumes:
      - postgres_data_volume:/var/lib/postgresql/data

networks:
  db_network:
  webproxy:
    external: true

volumes:
  postgres_data_volume: 
```

要设置环境变量，将它们放在一个`.env`文件中。确保`.env`文件和`docker-compose.yml`在同一个目录下。当你运行`docker-compose up`时，它将应用在`.env`文件中设置的变量。如果它们留空，则什么都不会设置。

设置所需的`COMMENTS_URL`和`EMAIL`，否则可能会遇到问题。设置这些的最好方法是在`.env`文件中调整它们。这里有一个例子:

```
COMMENTS_URL=comments.your.url

EMAIL=[you@your.url](mailto:you@your.url) 
```

## 
  
获取 OAuth 密钥&秘密

Commento 与大多数流行的 OAuth 提供商合作。因此，访问者可以留下评论，而无需创建帐户。

每个的说明都是相似的。我在下面列出了所有这些方法的步骤。

### 碎碎念

1.  登录[Twitter.com](http://twitter.com)，申请开发者账号:[https://developer.twitter.com/en/application/use-case](https://developer.twitter.com/en/application/use-case)

    [![Twitter API Access](img/50a4e386b607bfb23b6664b812a501d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KxYBRAfZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-4171cdf7-6c2b-408b-bb64-57822ede91cb.26.08_PM.png)

2.  描述你将如何使用 API。你可以用我写的。

    [![How will you use the API?](img/2747452071ffa84abd2728d93972e4bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YoLgq7GF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-4c0aecf2-c020-4005-bd5f-81e3b4ac6b8f.28.43_PM.png)

3.  仔细检查你的输入，点击**看起来不错！**

    [![Is everything correct?](img/21856b43b039ac7c9052dfc7fe4aa12a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nemdQzZ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-ade63510-86d3-48a4-a121-221f6e14cd96.28.50_PM.png)

4.  同意服务条款。

    [![Agree to Developer Agreement](img/8c404c2f0ca22b38ce0fd37fcdf44bf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fNQGr573--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-2e8e3089-bd51-4d27-8573-6987aafc663e.28.59_PM.png)

    [![You did it!](img/da1015bfa10e486198a657fb462b869a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1_BETd6H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-145b1bfd-9fc7-4ea6-ba5f-032e59d7fe8d.41.47_PM.png)

5.  他们会告诉你检查你的电子邮件进行确认。确认您的电子邮件，您应该能够创建您的第一个应用程序！

6.  一旦被批准进入**开始**点击**创建 app** 。

    [![Create an app](img/eb48a22fe54cdb509a74f0d4e689960d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fsfxPnkq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-640686b8-15c6-4af0-b9df-65ce15ae0fe7.29.22_PM.png)

7.  下一屏，再次点击**创建应用**

    [![Create an app](img/3c0d2cd19f14a7545a1df08a0a13ff20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qYP4EEs_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-de2b85d5-8bb7-428f-bfd1-2a23d0b7d4e0.29.26_PM.png)

8.  输入所有适当的详细信息。对于回调 URL，使用 [`https://<your URL>/api/oauth/github/callback`](https://comments.jaredwolff.com/api/oauth/google/callback) ，其中 [`<your URL>`](https://comments.jaredwolff.com/api/oauth/google/callback) 是您的评论子域。

    [![App details](img/cf1f1f6160d436706760ba2f9aac92fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ba3Ngchv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-91acb343-9dee-4917-be77-9704fe439722.32.44_PM.png)

9.  最后，一旦你完成填写信息去**钥匙和令牌**的区域。保存密钥和令牌。将它们输入到`.env`文件中。可以用`COMMENTO_TWITTER_KEY`和`COMMENTO_TWITTER_SECRET`

    [![Get oauth key and secret](img/11e42c12ecd08231d2f15557387da2c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CtSgCmVs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_6-b910e9ff-dc34-45e8-94df-affb06702617.33.07_PM.png)

### Gitlab

1.  登录[Gitlab.com](http://gitlab.com)并转到右上角点击**设置**
2.  然后点击**应用**

    [![Gitlab profile](img/0fbb177bebcc47c12c2ada92967bfee5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hvv4bNXL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-c6da9d02-2052-4fa4-89de-d5212b8f49ca.56.47_PM.png)

3.  输入应用的名称。我把**注释到**。

4.  将重定向 URI 设置为 [`https://<your URL>/api/oauth/gitlab/callback`](https://comments.jaredwolff.com/api/oauth/google/callback)

5.  选择 **read_user** 范围。

    [![Gitlab add application](img/07d37f3bbc146eb5f043dcafc1e9b5db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ol-s9Byx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-e616c338-6144-4704-93c6-914db6fad5f6.59.15_PM.png)

6.  点击绿色的**保存应用**按钮

7.  使用`COMMENTO_GITLAB_KEY`和`COMMENTO_GITLAB_SECRET`复制**应用 ID** 和**秘密**并将其放入您的`.env`文件中

    [![Application key and secret](img/d203d95f657a0e468cc5f8cb9db9fe57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fk5RSfIH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_1-a4f4ab4a-9fd6-423f-821c-6ff2f174e589.04.10_PM.png)

### Github

1.  要获得 OAuth 密钥和秘密，您需要访问以下网址:[https://github.com/settings/developers](https://github.com/settings/developers)
2.  在那里，点击**新 OAuth 应用**

    [![Add OAuth application](img/b1a81507a8faa9eae430d081ba798b28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oJsZwyMZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_9-18bf8f23-916f-476b-8c25-3377de931fe3.15.33_AM.png)

3.  输入您的详细信息。对于回调 URL，使用 [`https://<your URL>/api/oauth/github/callback`](https://comments.jaredwolff.com/api/oauth/google/callback) ，其中 [`<your URL>`](https://comments.jaredwolff.com/api/oauth/google/callback) 是您的评论子域。

    [![Register new OAuth application](img/7f917ff9765b63eb8988f98deaadb380.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ivrq79mU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_9-6e616334-7123-4de4-a4fd-f2fe319b1971.28.24_AM.png)

    *注意:确保在你的 URL 中包含`https`。*

4.  使用`COMMENTO_GITHUB_KEY`和`COMMENTO_GITHUB_SECRET`获取**客户端 ID** 和**客户端秘密**，并将其放入您的`.env`文件中

    [![Application created successfully](img/2226337704c625d8729ddffcb7fdbc5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R0AlSu25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_9-7505a3ef-386a-4b75-a7dc-1dd3e22d0baf.29.28_AM.png)

### 谷歌

设置谷歌和设置推特一样乏味。不管我刚才说的有多可怕，这是完全可行的。以下是步骤。

1.  转到这个网址:[谷歌开发者控制台](https://console.developers.google.com/cloud-resource-manager?previousPage=%2Fapi)
2.  创建新项目

    [![Create a new project](img/7cb660dd0ca55973e8f978934fa74c28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u7EDXiIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_8-f3793926-cc54-4345-b81c-5ec0f4631a35.42.48_AM.png)

3.  一旦你有了一个项目，点击左上角的 **GoogleAPIs 标志**返回。(确保 **GoogleAPIs 徽标**旁边的下拉菜单与您的新项目相同！)

4.  然后，点击左侧的**凭证**。

5.  在 **OAuth 同意界面**中更新**应用名称**和**授权域**

    [![Setup application](img/f3e7df1337553ac65f90ed3ecaece4e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZju-jsi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_8-d839a5c9-3368-4f18-b674-73b6e4e7c17c.47.15_AM.png)

6.  点击**创建凭证**，然后点击 **OAuth 客户端 ID**

    [![Setup credentials](img/d185bdafb2be955f5b872d6a54ac98a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtKwQpvm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_8-201545f9-4d47-4e0c-ae9a-b40efdc35a4b.44.36_AM.png)

7.  在 **Create OAuth client ID** 上输入与 Commento 关联的子域到**授权的 Javascript origins。**然后，输入完整的回拨网址。比如 [`https://comments.jaredwolff.com/api/oauth/google/callback`](https://comments.jaredwolff.com/api/oauth/google/callback) 。将`comments.jaredwolff.com`替换为您的 URL，让它成为您的。

    [![Create OAuth Client ID](img/a8ae09c5e555a1cb15052beb11cfe5f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M47GsuO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_8-fdba3491-d562-41f3-acff-2857ea816cec.52.15_AM.png)

    一旦进入，点击**创建**按钮。

8.  抓取**客户端 ID** 和**客户端秘密**

    [![OAuth Credentials](img/2bb2ac36f1a75e5e2339f11ce0f92975.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---ygaoQrZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-04_at_8-0c3f2895-0cb9-4b3a-a154-a3d80fd9716a.57.40_AM.png)

9.  使用`COMMENTO_GOOGLE_KEY`和`COMMENTO_GOOGLE_SECRET`更新你的`.env`文件

## 安装您的应用

您已经输入了 OAuth 凭据电子邮件、域和 SMTP 凭据。是时候结束这场演出了！

1.  一旦你编辑完你的`.env`文件。运行`docker-compose up`(对于未命名为`docker-compose.yml`的文件，使用`-f`标志。例子:`docker-compose -f commento.yml up`
2.  观察输出中的错误。如果它看起来不错，你可能想杀死它( **CTRL+C** )并带着`-d`标志运行
3.  第一次启动时，Commento 会提示您一个登录屏幕。

    [![Commento Login](img/0b7caa097dc40b21c5dbc7d03a343e10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vf1E0L6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-d5a1ca53-93b3-49c5-a3a7-e8b728259e2d.11.29_PM.png)

4.  点击**创建新账户还没有账户？报名吧。**

5.  输入您的信息并点击**注册**

6.  查看您的电子邮件，然后点击包含的链接:

    [![Validation email with link](img/a47c70d992848a4367afdd19416af1d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d1kmnctS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-e263aa4f-201b-42ac-986c-b28c5f003f38.12.48_PM.png)

7.  使用您新创建的帐户登录。

8.  然后，点击**添加一个新域。**

    [![Add new domain](img/d395f8a6d1243f31cab76a70ba957a91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ziT-hRfh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-46acfe9c-f3f4-4d3e-b8fb-97fbff643a86.10.47_PM.png)

9.  创建完成后，转到**安装指南。**复制这个片段，然后把它放在你想要你评论的地方。在我的例子中，我把代码片段放在了我的`<article>`标签后面的区域。

    [![Code snippet](img/8930027a65e15c6041c92b2dbb884f79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r7H7fXlf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-f78f36c5-f3f7-45ec-971d-9bf0bf7b7d1f.36.35_PM.png)

10.  重新编译你的网站并检查是否成功！

    [![Blog comment section with checkmarks](img/1a2e07d2c32792bb864131bc66412465.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RlBVjQMx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jaredwolff.com/how-to-setup-worry-free-blog-comments-in-less-than-20-simple-steimg/Screen_Shot_2019-07-05_at_12-8f7ffbdc-c49f-49bc-95bb-1f53a926f361.30.27_PM.png)

    勾号！最后，我建议您尝试使用每个单独的 OAuth 配置登录。这样你就知道它为你的网站访问者工作。😎

## 替代品

我花了很大一部分时间研究一些替代方案。这绝不是关于什么最适合你的网站的权威指南。以下是撰写本文时的一些最佳案例:

[https://utteranc.es/#configuration](https://utteranc.es/#configuration)

[https://github.com/netlify/gotell](https://github.com/netlify/gotell)

[https://github.com/eduardoboucas/staticman](https://github.com/eduardoboucas/staticman)

[https://posativ.org/isso/](https://posativ.org/isso/)

[https://www.remarkbox.com](https://www.remarkbox.com/)

[https://www.vis4.net/blog/2017/10/hello-schnack/](https://www.vis4.net/blog/2017/10/hello-schnack/)

[https://github.com/gka/schnack](https://github.com/gka/schnack)

Hugo 博客上也有一个巨大的主题，里面有很多链接和资源:

[https://discourse . go Hugo . io/t/alternative-to-disqus-needle-than-ever-by-less/5516](https://discourse.gohugo.io/t/alternative-to-disqus-needed-more-than-ever/5516)

## 结论

恭喜你。您现在拥有了自己的评论服务器！🎉

在本文中，您了解了如何利用 Docker 和 Nginx 反向代理的力量。另外，您知道如何设置 OAuth 凭证！这样，未来的设置将很容易。

顺便说一下，这只是冰山一角。您可以为分析、数据收集等设置相同的服务器。所有示例代码，包括其他应用程序的代码，都可以在这里找到。

最后，如果你正在寻找付费评论，请访问 www.commento.io 并注册这项服务。你将支持令人敬畏的开源软件！

如果你有意见和问题，让我们听听。开始下面的对话。👇👇👇