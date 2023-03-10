# 使用 AWS CloudFormation 通过 Amplify 控制台部署 Gatsby 应用程序

> 原文：<https://dev.to/taleb/deploy-a-gatsby-app-with-the-amplify-console-using-aws-cloudformation-3p4o>

```
AWSTemplateFormatVersion: 2010-09-09

Parameters:
  Repository:
    Type: String
    Description: GitHub Repository URL

  OauthToken:
    Type: String
    Description: GitHub token for repo read 
    NoEcho: true

  OauthTokenNPM:
    Type: String
    Description: GitHub token for private npm
    NoEcho: true

Resources:
  AmplifyRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - amplify.amazonaws.com
            Action:
              - sts:AssumeRole
      Policies:
        - PolicyName: Amplify
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action: 'amplify:*'
                Resource: '*'

  AmplifyApp:
    Type: 'AWS::Amplify::App'
    Properties:
      Name: firstapp
      Repository: !Ref Repository
      Description: a test website
      OauthToken: !Ref OauthToken
      EnvironmentVariables:
        - Name: NPM_PASS
          Value: !Ref OauthTokenNPM
      AutoBranchCreationConfig:
        EnableAutoBranchCreation: True
        EnableAutoBuild: False
      BuildSpec: |-
        version: 0.1
        frontend:
          phases:
            preBuild:
              commands:
                - cd gatsby
                - nvm use $VERSION_NODE_12
                - npx npm-cli-login -u example-user -p $NPM_PASS -e webmaster@example.com -r http://npmregistry.example.com
                - npm config set always-auth true
                - npm config set registry=http://npmregistry.example.com/
                - yarn

            build:
              commands:
                - nvm use $VERSION_NODE_12
                - yarn run build
          artifacts:
            baseDirectory: gatsby/public
            files:
              - '**/*'
          cache:
            paths:
              - node_modules/**/*
      CustomRules:
        - Source: gatsby/public/index.html
          Target: /index.html
          Status: '200'

      Tags:
        - Key: Name
          Value: web-test
      IAMServiceRole: !GetAtt AmplifyRole.Arn

  AmplifyBranch:
    Type: AWS::Amplify::Branch
    Properties:
      BranchName: develop
      AppId: !GetAtt AmplifyApp.AppId
      Description: Develop Branch
      EnableAutoBuild: false
      Tags:
        - Key: Name
          Value: test-develop
        - Key: Branch
          Value: develop

Outputs:
  DefaultDomain:
    Value: !GetAtt AmplifyApp.DefaultDomain 
```

现在这样跑

```
aws cloudformation deploy --profile <aws-profile> \
  --template-file ./amplify-template.yaml \
  --capabilities CAPABILITY_IAM \
  --parameter-overrides \
      OauthToken=<OauthToken> \
      OauthTokenNPM=<OauthTokenNPM> \
      Repository=https://github.com/<user>/<repo> \
  --stack-name Firstapp 
```

云层形成:[https://console.aws.amazon.com/cloudformation/home](https://console.aws.amazon.com/cloudformation/home)
扩音控制台:[https://console.aws.amazon.com/amplify/home](https://console.aws.amazon.com/amplify/home)

更多阅读:[https://AWS . Amazon . com/blogs/mobile/deploy-a-vuejs-app-with-AWS-amplify-console-using-cloud formation/](https://aws.amazon.com/blogs/mobile/deploy-a-vuejs-app-with-aws-amplify-console-using-cloudformation/)