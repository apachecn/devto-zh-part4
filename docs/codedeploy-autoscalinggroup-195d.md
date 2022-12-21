# 在 CodeDeploy 中部署 AutoScalingGroup 时发生角色错误

> 原文：<https://dev.to/shootacean/codedeploy-autoscalinggroup-195d>

## 错误内容

`The IAM role arn:~~~ does not give you permission to perform operations in the following AWS service: AmazonAutoScaling.`

## Solution

只需将以下策略添加到当前分配给 CodeDeploy 的 IAM 角色策略中:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "VisualEditor0",  "Effect":  "Allow",  "Action":  [  "iam:PassRole",  "ec2:CreateTags",  "ec2:RunInstances"  ],  "Resource":  "*"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## Reference

*   [堆栈溢出](https://stackoverflow.com/questions/53731017/simple-iam-issue-with-codedeploy)
*   [https://h2ik.co/2019/02/28/aws-codedeploy-blue-green/](https://h2ik.co/2019/02/28/aws-codedeploy-blue-green/)