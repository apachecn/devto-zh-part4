# 具有现有 S3 存储桶和现有 SNS 主题的 AWS CDK 示例

> 原文：<https://dev.to/katryo/aws-cdk-sample-with-existing-s3-bucket-and-existing-sns-topic-4f1f>

您不能通过 CloudFormation 向现有 S3 时段添加新的 S3 通知。但是，您可以向现有 SNS 主题添加 SQS 订阅。

S3 => SNS 话题=> SQS 订阅= >λ

```
import core = require("@aws-cdk/core");
import lambda = require("@aws-cdk/aws-lambda");
import s3 = require("@aws-cdk/aws-s3");
import sns = require("@aws-cdk/aws-sns");
import sqs = require("@aws-cdk/aws-sqs");
import { SqsEventSource } from "@aws-cdk/aws-lambda-event-sources";
import { SqsSubscription } from "@aws-cdk/aws-sns-subscriptions";

export class ExistingS3BucketAndSNSTopicToLambdaThroughSQS extends core.Construct {
  constructor(scope: core.Construct, id: string) {
    super(scope, id);

    const bucket = s3.Bucket.fromBucketName(
      this,
      "ExistingS3Bucket",
      "bucketName"
    );

    const handler = new lambda.Function(this, "lambda", {
      runtime: lambda.Runtime.NODEJS_10_X,
      code: lambda.Code.asset("lambda/dist"),
      handler: "index.main",
      timeout: core.Duration.seconds(30),
      environment: {
        BUCKET_NAME: bucket.bucketName
      }
    });

    bucket.grantRead(handler);

    const topic = sns.Topic.fromTopicArn(
      this,
      "ExistingSNSTopic",
      `arn:${core.Aws.PARTITION}:sns:${core.Aws.REGION}:${core.Aws.ACCOUNT_ID}:existing-sns-topic`
    );
    const queue = new sqs.Queue(this, "queue");

    topic.addSubscription(new SqsSubscription(queue));

    handler.addEventSource(
      new SqsEventSource(queue, {
        batchSize: 1
      })
    );
  }
} 
```

这个例子在 AWS CDK 1 . 6 . 1 版上运行良好。将来它可能会被破坏，因为 AWS CDK 还在公测阶段。

gist:[https://gist . github . com/katryo/ff 3c F8 b5 E3 f 12823 ad 7 BC 2468 db 054 CD](https://gist.github.com/katryo/ff3cf8b5e3f12823ad7bc2468db054cd)