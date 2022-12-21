# 使用 lerna monorepo 进行 node.js 项目完全清理构建

> 原文：<https://dev.to/iolo/nodejs-full-clean-build-with-lerna-monorepo-4m25>

```
$  rm -rf ~/.npm/_cacache 
$  npm cache clean --force
$  lerna clean
$  rm -rf node_modules
$  npm install
$  lerna bootstrap
$  lerna run build 
```

如果 nodejs 失败，那将是由于 npm 和 webpack，
就像 gem 和 nokogiri 在 ruby 中所做的那样。