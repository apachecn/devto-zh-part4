# gRPC 从 Go 到 PHP 的高级错误处理

> 原文：<https://dev.to/khepin/grpc-advanced-error-handling-from-go-to-php-1omc>

GO 社区已经标准化使用`status` [包](https://godoc.org/google.golang.org/grpc/status)将 gRPC 服务中的大量错误发送回客户端。

通过这种方式，我们可以发送一个带有`BadRequest`错误的状态，错误消息本身可以包含许多`FieldViolation`错误，以及关于哪个字段有错误以及错误是什么的信息。

这些错误很容易从另一个 GO gRPC 客户机中检索和使用，但是在我的例子中，我需要一个解决方案来从 PHP 客户机中获取这些信息，但是可用的信息非常少。

这显示了如何做:

```
<?php declare(strict_types=1);

use GPBMetadata\Google\Rpc\ErrorDetails;
use Google\Rpc\Status;
use Google\Rpc\BadRequest;

[$response, $status] = $client->SomeRemoteCall($request)->wait();
if ($status->code !== \Grpc\STATUS_OK) {
    ErrorDetails::initOnce(); // Ensures that the protobuf descriptor is registered

    $details = $status->metadata["grpc-status-details-bin"][0] ?? null; // GO errors / status will be under this key
    if (is_null($details)) {
        throw new HttpException($message, $code, (array) $status);
    }
    $s = new Status();
    $s->mergeFromString($details);
    $errors = [];
    foreach ($s->getDetails() as $anyDetails) {
        $deets = $anyDetails->unpack(); // The details are a repeated Any field.
        $deets->discardUnknownFields(); // Because this was a "Any" field, it has unknown fields such as @type
        if ($deets instanceof BadRequest) {
            foreach ($deets->getFieldViolations() as $fv) {
                $errors[$fv->getField()] = [
                    'message' => $fv->getDescription(),
                ];
            }
        } else {
            $jsonDeets = $deets->serializeToJsonString(); // encode / decode the protobuf to json to get an array
            $errors[$deets->getField()] = json_decode($jsonDeets);
        }
    }

    // Formatted error with main error and a list of cause errors:
    // 'errors' [
    //  'field-name' => 'message describing the error'
    // ]
    $error = [
        'message' => $s->getMessage(),
        'errors' => $errors,
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

`Status`、`ErrorDetails`和`BadRequest`类来自包:`google/common-protos`所以一定要`composer require google/common-protos`