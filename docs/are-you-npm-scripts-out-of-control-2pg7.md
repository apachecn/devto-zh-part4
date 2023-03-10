# 你的 NPM 剧本失控了吗？

> 原文：<https://dev.to/codingcatdev/are-you-npm-scripts-out-of-control-2pg7>

## 举例

这是可行的，但我感觉它可以更好！文案:deps 长的吓人 lol。

[剧本](https://github.com/AJONPLLC/ajonp-ajsbooks-nextjs/blob/64f9eea1b900a37cbb75a16a745f8ee2a0f8cd81/package.json#L37)

```
 "scripts":  {  "dev":  "next",  "build":  "next build",  "start":  "next start",  "export":  "next export",  "analyze":  "cross-env BUNDLE_ANALYZE=both next build",  "analyze:server":  "cross-env BUNDLE_ANALYZE=server next build",  "analyze:browser":  "cross-env BUNDLE_ANALYZE=browser next build",  "build:functions":  "npm run build:functions:lint && cpx \"functions/lib/functions/src/**/*.*\" dist/functions",  "build:functions:lint":  "cd functions && npm run lint && npm run build",  "clean":  "rimraf dist && rimraf functions/lib && rimraf .next",  "copy:deps":  "cpx \"functions/*{package.json,package-lock.json}\" dist/functions && ncp functions/node_modules/ dist/functions/node_modules && cpx \".next/serverless/**/*.*\" dist/functions/_next/serverless/ && cpx \".next/static/**/*.*\" dist/public/_next/static/ && cpx \"static/**/*.*\" dist/public/static && cpx \"dist/functions/_next/static/**/*.*\" dist/public/_next/static && cpx \"dist/functions/_next/serverless/pages/*.html\" dist/public",  "firebase:build":  "npm install && npm run clean && npm run build && npm run build:functions && npm run copy:deps",  "firebase:serve":  "npm run firebase:build && firebase serve",  "firebase:deploy":  "npm run firebase:build && firebase deploy"  }, 
```

Enter fullscreen mode Exit fullscreen mode

## 我们应该什么时候停止呢？

那么一个剧本什么时候算太长呢？我真的不知道！

有时我会将它们分解到我的 cloudbuild.yaml 文件中，该文件会像这样调用另一个文件。

```
# Git the submodules, run npm install, hugo build
- name: 'gcr.io/$PROJECT_ID/hugo'
  args: ['bash', './deploy.sh'] 
```

Enter fullscreen mode Exit fullscreen mode

这也激发了很多使用 NPM 可以做的事情。我之所以移动这个，是因为如果我有 GCloud 运行它，我就不用担心 git 会因为登录而崩溃🦄！

```
#!/bin/bash

echo -e "\033[0;32mAdding Submodules...\033[0m"

git submodule init
git submodule update --recursive --remote

echo -e "\033[0;32mInstalling via npm...\033[0m"

npm install

echo -e "\033[0;32mBuilding via npm...\033[0m"

npm run build 
```

Enter fullscreen mode Exit fullscreen mode

## 思想

告诉我情况有多糟，你有什么例子吗？？