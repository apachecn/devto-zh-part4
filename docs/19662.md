# 用于在线展示的开源 GraphQL API(网飞、HBO、Hulu、亚马逊 Prime 等。)

> 原文：<https://dev.to/prabhuomkar/open-source-graphql-api-for-online-show-netflix-hbo-hulu-amazon-prime-etc-5bmk>

frisky 是一个使用 GraphQL 的开源项目，试图提供关于电视节目及其剧集的信息，包括所有信息，如名称、描述、年份、海报、创作者、主演以及允许播放该节目的服务提供商。

**当前服务提供商:**
网飞、HBO、亚马逊 Prime、Hulu

**链接:**
**GitHub:**[https://github.com/prabhuomkar/frisky](https://github.com/prabhuomkar/frisky)
**文档:**[https://prabhuomkar.github.io/frisky](https://prabhuomkar.github.io/frisky)

***通过阅读 API 文档来展示对存储库的热爱或贡献数据/代码:)***

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [帕布霍姆卡](https://github.com/prabhuomkar) / [活蹦乱跳](https://github.com/prabhuomkar/frisky)

### 🍿用于在线节目的开源 GraphQL API

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![](img/259a2508d6e7a0038e8f8bcfc4a143e0.png)](https://camo.githubusercontent.com/6eb938983459583060ceadf19ffa5ff0dee24c33/68747470733a2f2f7072616268756f6d6b61722e6769746875622e696f2f667269736b792f6173736574732f66617669636f6e732f616e64726f69642d69636f6e2d34387834382e706e67) 活泼好动

Open Source GraphQL API for Online Shows

[![](img/396ab025a1506377778234ddf375d51a.png)](https://camo.githubusercontent.com/a02bf367099f91979e8fdbb3f3834db4db9bf903/68747470733a2f2f7072616268756f6d6b61722e6769746875622e696f2f667269736b792f6173736574732f6f74686572732f696c6c757374726174696f6e2e706e67)

## 使用的技术堆栈/框架

### 建造于

*   [云图](https://www.mongodb.com/cloud/atlas) -数据库服务提供商
*   [Heroku](https://heroku.com/) -平台服务提供商

### Node.js 包

*   [图表 QL](https://www.npmjs.com/package/graphql)
*   [快递](https://www.npmjs.com/package/express)
*   [快递图表 QL](https://www.npmjs.com/package/express-graphql)
*   [猫鼬](https://www.npmjs.com/package/mongoose)

## 使用

### 先决条件

*   [node . js](https://nodejs.org/)——Chrome 的 V8 Javascript 引擎
*   NoSQL 数据库

### 安装和设置

*   安装 package.json 文件中提到的包，以获得项目的所有依赖项。

```
npm install --save 
```

*   准备好配置后，在同一位置复制`.env.example`文件，并另存为`.env`

```
cp .env.example .env 
```

*   启动应用程序(在启动 MongoDB 的 mongod 之后)

```
npm start 
```

#### 林挺

*   林挺密码

```
npm run lint 
```

*   解决林挺问题

```
npm run fix 
```

#### 导入数据库

*   从示例数据导入节目

```
mongoimport -d <database_name> -c shows seeds/shows.json 
```

*   从样本数据导入剧集

```
mongoimport -d <database_name> -c episodes seeds/episodes.json 
```

## API 参考

查看[文档](https://prabhuomkar.github.io/frisky)了解更多信息！

## 捐助

投稿指南即将推出！

## 问题

问题通过 GitHub 问题管理。

## 许可证

…

</article>

[View on GitHub](https://github.com/prabhuomkar/frisky)