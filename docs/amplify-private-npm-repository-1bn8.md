# 扩大私有国家预防机制储存库

> 原文：<https://dev.to/taleb/amplify-private-npm-repository-1bn8>

```
publicversion: 0.1
frontend:
  phases:
    preBuild:
      commands:
        # Monorepo going in gatsby directory
        - cd gatsby
        # Can use $VERSION_NODE_10/$VERSION_NODE_8
        - nvm use $VERSION_NODE_12
        - npx npm-cli-login -u $NPM_USER -p $NPM_PASS -e $NPM_EMAIL -r $NPM_REG
        - npm config set always-auth true
        - npm config set registry=$NPM_REG
        - yarn

    build:
      commands:
        # Can use $VERSION_NODE_10/$VERSION_NODE_8
        - nvm use $VERSION_NODE_12
        - yarn run build
  artifacts:
    baseDirectory: gatsby/public
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/* 
```