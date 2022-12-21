# 无服务器更快的文件传输

> 原文：<https://dev.to/ibmdeveloper/faster-file-transfers-with-serverless-1lop>

本周，我一直在帮助一个客户使用无服务器加速云对象存储之间的文件传输。

他们在云提供商的对象存储上有一个 120GB 的文件。这需要复制到不同的云对象存储中，以便与平台服务集成。他们当前的文件传输过程是在本地下载文件，然后使用开发机器重新上传。由于带宽问题，这花了将近三个小时。

*听说了无服务器云平台的功能后，他们想知道是否可以利用无服务器提供的大规模并行性来加速这一过程？*🤔

经过一番调查，我找到了一种使用无服务器实现并发文件传输的方法。**传输时间从三个小时缩短到仅仅四分钟！**总传输时间减少了 98%。👏👏👏

在这篇博文中，我将概述我实现这一目标的简单步骤。我一直使用 IBM 云功能作为无服务器平台。两个不同的 [S3 兼容的](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html)对象存储被用于文件传输。这种方法应该适用于具有下述特性的任何对象存储。

## 兼容 S3 的 API 特性

用于文件传输的两个对象存储都提供了一个与 S3 兼容的 API。S3 API 有两个特性，当结合使用时，可以实现并发文件传输:[范围读取](https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests)和[多部分传输](https://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html)。

### 范围写着

HTTP/1.1 协议定义了一个`Range` [头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests)，允许客户端检索文档的一部分。客户端使用头值指定一个字节范围，例如`Range: bytes=0-499`。然后，字节值在 HTTP 响应中返回，带有一个 [HTTP 206](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/206) 状态代码。如果字节范围无效，则返回一个 [HTTP 416](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/416) 响应。

**S3 API 支持对象存储文件的`GET` HTTP 请求上的`Range`请求头。**

发送对象存储文件的 HTTP HEAD 请求将返回文件大小(使用`Content-Length`头值)。为固定字节块创建范围，最大为该文件大小(`0-1023`、`1024-2047`、`2048-3072`)...)允许并行检索文件的所有部分。

### 多部分转移

使用 HTTP PUT 请求将文件上传到存储桶。这些操作支持最大 5GB 的文件大小。只有使用“多部分”传输才能上传较大的文件。

客户端[使用 API 发起多部分传输](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html),并被返回上传标识符。然后这个大文件被分割成几个部分，使用[单独的 HTTP PUT 请求](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html)上传。上传标识符用于将各个请求标记为属于同一文件。一旦所有零件都上传完毕，API 用于[确认文件完成](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadComplete.html)。

文件部分不必按连续顺序上传，多个部分可以同时上传。

## 无服务器文件传输

结合这两个特性，我能够创建一个无服务器函数来在源和目标存储桶之间复制文件的一部分。通过并行调用数千个这样的函数，整个文件可以在存储桶之间的并行流中同时复制。这是由一个本地脚本控制的，该脚本用于管理函数调用、监控进程并在调用完成后完成多部分传输。

### 无服务器功能

无服务器功能在对象存储之间复制文件部分。使用访问桶文件、要复制的字节范围和多部分传输标识符所需的所有参数来调用它。

```
exports.main = async function main (params) {
  const { src_bucket, src_file, range, dest_bucket, dest_file, mpu, index} = params
  const byte_range = await read_range(src_bucket, src_file, range)
  const upload_result = await upload_part(dest_bucket, dest_file, mpu, index, byte_range)
  return upload_result
} 
```

#### 读取源文件部分

S3-API JS 客户端可以通过传递带有字节范围值的`Range`参数，例如`bytes=0-NN`，来创建一个*范围读取*请求。

```
const read_range = async (Bucket, Key, Range) => {
  const file_range = await s3.getObject({Bucket, Key, Range}).promise()
  return file_range.Body
} 
```

#### 上传文件部分

`uploadPart`方法用于完成多部分传输中的一部分。该方法需要在启动多部分传输时创建的`UploadID`和块索引的`PartNumber`。将返回上传内容的 ETags。

```
const upload_part = async (Bucket, Key, UploadId, PartNumber, Body) => {
  const result = await s3.uploadPart({Bucket, Key, UploadId, PartNumber, Body}).promise()
  return result
} 
```

*注意:`uploadPart`方法不支持流`Body`值，除非它们来自文件系统。这意味着整个部分必须在上传前读入内存。无服务器功能必须有足够的内存来处理这个问题。*

### 本地脚本

用于调用函数的本地脚本必须做以下事情...

*   创建并完成多部分转移
*   计算函数输入参数的文件部分字节范围
*   使用并发函数调用复制文件部分。

#### 创建多部分转移

S3 API JS 客户端可以用来创建一个新的多部分转移。

```
const { UploadId } = await s3.createMultipartUpload({Bucket: '...', Key: '...'}).promise() 
```

然后，`UploadId`可用作无服务器功能的输入参数。

#### 创建字节范围

可以使用客户端库检索源文件大小。

```
const file_size = async (Bucket, Key) => {
  const { ContentLength } = await s3.headObject({Bucket, Key}).promise()
  return ContentLength
} 
```

这个文件大小需要分割成固定大小块的连续字节范围。该函数将返回所需的 HTTP 范围头值(`bytes=N-M`)的数组。

```
const split_into_ranges = (bytes, range_mbs) => {
  const range_size = range_mbs * 1024 * 1024
  const ranges = []
  let range_offset = 0
  const last_byte_range = bytes - 1

  while(range_offset < last_byte_range) {
    const start = range_offset
    // Last byte range may be less than chunk size where file size
    // is not an exact multiple of the chunk size.
    const end = start + Math.min((range_size - 1), last_byte_range - start)
    ranges.push(`bytes=${start}-${end}`)
    range_offset += range_size
  }

  return ranges
} 
```

#### 调用并发功能

需要为上面计算的每个字节范围调用无服务器函数。根据所使用的文件和块的大小，所需的调用次数可能大于平台的并发率限制(在 IBM Cloud Functions 上默认为 1000 次)。在上面的例子中(100MB 块中的 120GB 文件)，需要 1229 次调用。

该脚本需要使用最多 1000 个并发调用，而不是一次执行所有的字节范围。当初始调用完成时，可以调用其他函数，直到处理完所有字节范围。这段代码片段展示了这个问题的解决方案(使用 [IBM Cloud Functions JS SDK](https://github.com/apache/openwhisk-client-js) )。

```
const parallel = require('async-await-parallel');
const retry = require('async-retry');
const openwhisk = require('openwhisk');

const concurrent = 1000
const retries = 3
const chunk_size = 100

const static_params = {
  source_bucket, dest_bucket, source_filename, dest_filename, mpu
}

const ow = openwhisk({...});

const bucket_file_size = await file_size(source_bucket, source_filename);
const ranges = split_into_ranges(bucket_file_size, chunk_size);

const uploads = ranges.map((range, index) => {
  const invoke = async () => {
    const params = Object.assign({range, index: index + 1}, static_params)
    const upload_result = await ow.actions.invoke({
      name: '...', blocking: true, result: true, params
    })
    return upload_result
  }

  return async () => retry(invoke, retries)
})

const finished = await parallel(uploads, concurrent) 
```

`uploads`值是一组延迟评估的无服务器函数调用。代码片段使用`async-await-parallel` [库](https://www.npmjs.com/package/async-await-parallel)来限制并发调用的数量。使用`async-retry` [库](https://www.npmjs.com/package/async-retry)处理间歇性或错误的调用错误。失败的调用将重试三次。

#### 完成多件传递

一旦上传了所有零件，ETags(从无服务器调用返回)和零件号用于完成多零件转移。

```
const parts = finished.map((part, idx) => {
  part.PartNumber = idx + 1
  return part
})

const { Location, Bucket, Key, ETag } = await s3.completeMultipartUpload({
  Bucket: '...', Key: '...', UploadId: '...', MultipartUpload: { Parts }
}).promise() 
```

## 结果

之前的文件传输过程(从本地下载并从开发机器重新上传)花费了将近**三个小时**。这是 1.33 MB/秒的平均吞吐率((120GB * 2) / 180)。

使用无服务器功能，整个过程在**四分钟**内完成。使用 1229 次函数调用并行传输了 100MB 的文件块。这是 60MB/s 的平均吞吐率。**总传输时间减少了约 98%。**💯💯💯

无服务器使得在云中运行[令人尴尬的并行](https://en.wikipedia.org/wiki/Embarrassingly_parallel)工作负载变得异常容易。只需几行代码，文件传输过程就可以使用数千个并发函数并行化。可以想象，客户对此印象深刻...😎