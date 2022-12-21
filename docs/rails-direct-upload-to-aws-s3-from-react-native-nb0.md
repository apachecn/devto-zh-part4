# 从 React Native 直接上传到 AWS S3

> 原文：<https://dev.to/itzsaga/rails-direct-upload-to-aws-s3-from-react-native-nb0>

我最近接受了一项任务，允许我正在帮助开发的一款名为 [React Native](http://facebook.github.io/react-native/) 的应用的用户上传一张自定义的个人资料图片。当我在我们的 sprint 计划中估计它时，这听起来像是一个相对简单的任务。然而，我还是给了自己一些宽限，因为我以前从来没有做过这样的事情，并且花了 8 个小时。我一点也不知道将要发生什么。

你看，我知道我们的后端正在运行 [Ruby on Rails](https://rubyonrails.org) (RoR)，我也知道[活动存储](https://guides.rubyonrails.org/active_storage_overview.html)现在是主流，但我没有意识到当我把[亚马逊网络服务(AWS) S3](https://aws.amazon.com/s3/) 加入进来时会遇到的问题。我听说过关于活动存储的好消息，虽然我没有使用过它，但我非常了解 RoR，知道他们添加的东西是有意的，通常都是经过深思熟虑的，我也知道我在 S3 的经验是，当涉及到 IAM 角色和事情时，配置可能有点复杂，一旦它按照您想要的方式运行*应该*非常容易使用。尤其是对于即将公开的事情。

在我从事这项工作的早期，后端工程师告诉我，活动存储有一个非常好的方法，允许客户端应用程序直接向 S3 发送文件，只向 Rails 服务器发送一个引用字符串。这是首选，因为数据不是从客户机发送到 Rails 服务器，而是直接从客户机发送到 Amazon。跳过一个步骤可以加快速度，还可以节省服务器的一些负载。我觉得这很酷。我们在 [Airship](https://teamairship.com) 之前已经用一个网络应用程序完成了这项工作，结果很不错。我有代码可以参考，也是我工作的基础。

#### 哪里开始出问题了……

这是事情开始分裂的地方。我开始消化我们创建的 web 应用程序的代码:

```
import axios from "axios";
import SparkMD5 from "spark-md5";

const getUploadInfo = async (file) => {
  const checksum = await createFileChecksum(file);

  return axios.post(
    `${process.env.BASE_URL}/rails/active_storage/direct_uploads`,
    {
      blob: {
        filename: file.name,
        content_type: file.type,
        byte_size: file.size,
        checksum: checksum
      }
    }
  );
};

export const createFileChecksum = async (file) => {
  return new Promise((resolve, reject) => {
    const chunkSize = 2097152; // 2MB
    const chunkCount = Math.ceil(file.size / chunkSize);
    var chunkIndex = 0;

    const md5Buffer = new SparkMD5.ArrayBuffer();
    const fileReader = new FileReader();

    const readNextChunk = () => {
      if (chunkIndex < chunkCount || (chunkIndex === 0 && chunkCount === 0)) {
        const start = chunkIndex * chunkSize;
        const end = Math.min(start + chunkSize, file.size);
        const fileSlice =
          File.prototype.slice ||
          File.prototype.mozSlice ||
          File.prototype.webkitSlice;
        const bytes = fileSlice.call(file, start, end);
        fileReader.readAsArrayBuffer(bytes);
        chunkIndex++;
        return true;
      } else {
        return false;
      }
    };

    fileReader.addEventListener("load", event => {
      md5Buffer.append(event.target.result);

      if (!readNextChunk()) {
        const binaryDigest = md5Buffer.end(true);
        const base64digest = btoa(binaryDigest);
        resolve(base64digest);
      }
    });

    fileReader.addEventListener("error", () =>
      reject(`Error reading ${file.name}`)
    );

    readNextChunk();
  });
};

export const uploadFile = async (file) => {
  const uploadInfo = await getUploadInfo(file);

  await axios.put(uploadInfo.data.direct_upload.url, file, {
    headers: uploadInfo.data.direct_upload.headers
  });

  return uploadInfo.data.signed_id;
}; 
```

很快的，`getUploadInfo()`将文件的相关信息发送到 Rails 后端，并返回直接上传到 S3 所需的信息。`createFileChecksum()`被`getUploadInfo()`用来计算正在发送的文件的 base64 编码的 md5 校验和。虽然 Amazon 不需要这个，但是 Rails 需要。最后，`uploadFile()`将文件上传到 AWS，然后返回`signed_id`,该文件将被发送到 Rails，这样 Rails 就可以将该文件与后端的任何内容相关联。

我后来意识到大部分代码来自其他地方，甚至可能是 [@rails/activestorage](https://www.npmjs.com/package/@rails/activestorage) 包。我在 GitHub 的 Rails 库的一个 [`file_checksum.js`](https://github.com/rails/rails/blob/master/activestorage/app/javascript/activestorage/file_checksum.js) 文件中找到了类似的代码。不管代码的来源是什么，都有一个问题。我无法访问移动设备上的`FileReader` api。我用的是 React Native，而不是浏览器。所以现在搜索开始在 React Native 中做同样的事情。

## 所有没用的东西

实际上，我不会用所有没用的东西来烦你。我真的认为你不在乎。你可能在谷歌上搜索过如何做到这一点，但在互联网上根本找不到。然而，这种直接上传功能在 Rails 中出现已经有一段时间了。你甚至可能已经登陆了 Rails 问题[让 ActiveStorage 只为 API 应用程序工作](https://github.com/rails/rails/issues/32208)和那里的一个评论:

> 对于 react native 上的用户，我可以使用`rn-fetch-blob`进行 md5 散列(以十六进制输出)，然后使用`buffer`计算校验和，将其十六进制输出转换为 base64。为了查找 content_type，我使用了`react-native-mime-types`，最后但同样重要的是，再次使用`rn-fetch-blob`来计算大小。然后，只要遵循 [@cbothner](https://dev.to/cbothner) 指出的沟通准则，如果文件很大，使用`rn-fetch-blob`高效上传文件。
> 
> <cite>–[硅锌矿](https://github.com/rails/rails/issues/32208#issuecomment-477688465)T3】</cite>

所以，我试着按照上面的线索，我不能让它工作。当然，这个评论已经有将近 6 个月的历史了，而在 JavaScript 时代，那已经是很久以前的事了。我遇到的主要问题是，我无论如何也无法让校验和与亚马逊计算的结果相匹配。我不断得到“您指定的 Content-MD5 无效”的响应。我尝试了许多生成 md5 校验和的方法，结果都是从 AWS 返回相同的内容 MD5 消息。

下面是我如何让它工作的(为什么你真的在这里):

```
import axios from "axios";
import Config from "react-native-config";
import RNFetchBlob from "rn-fetch-blob";
import AWS from "aws-sdk/dist/aws-sdk-react-native";
import { Platform } from "react-native";
import { Buffer } from "buffer";
const { fs } = RNFetchBlob;

AWS.config.update({
  accessKeyId: Config.AWS_ACCESS_KEY_ID,
  region: Config.AWS_REGION,
  secretAccessKey: Config.AWS_SECRET_ACCESS_KEY
});
const s3 = new AWS.S3({ apiVersion: "2006-03-01" });

const getUploadInfo = async (fileInfo, file) => {
  const params = {
    Bucket: Config.AWS_BUCKET,
    ContentType: fileInfo.type,
    Key: fileInfo.fileName,
    Body: file
  };
  const psUrl = s3.getSignedUrl("putObject", params);
  const checksum = unescape(psUrl.split("&")[1].split("=")[1]);

  return axios.post(
    `${Config.API_UPLOAD_HOST}/rails/active_storage/direct_uploads`,
    {
      blob: {
        filename: fileInfo.fileName,
        content_type: fileInfo.type,
        byte_size: fileInfo.fileSize,
        checksum: checksum
      }
    }
  );
};

export const uploadFile = async (fileInfo) => {
  const uri =
    Platform.OS === "ios" ? fileInfo.uri.replace("file://", "") : fileInfo.uri;
  const file = await fs
    .readFile(uri, "base64")
    .then(data => new Buffer(data, "base64"));

  const uploadInfo = await getUploadInfo(fileInfo, file);
  const { headers, url } = uploadInfo.data.direct_upload;

  try {
    await axios.put(url, file, { headers: { ...headers } });
  } catch (e) {
    throw e;
  }

  return uploadInfo.data.signed_id;
}; 
```

这绝对不是最优雅的解决方案。我也还没有重构它。然而，它是有效的。在代码的世界里，这是有意义的。这到底是怎么回事？我会从头到尾走一遍，不过，我会跳过一些文件。首先，我设置了`aws-sdk`和一个新的`s3`实例。我在这里使用`react-native-config`来管理环境变量。我最初这样做是为了看看我是否可以通过绕过 Rails 直接上传到 AWS 来获得我需要的`signed_id`，但这并不奏效。然而，当我生成一个通过`aws-sdk`上传的预签名 URL 时，我注意到该 URL 包含 md5 校验和！

#### 回码

好的，代码，走一遍，我们开始。我在屏幕组件上调用来自`react-native-image-picker`的响应中的`uploadFile()`。这就是`fileInfo`论点的来源。然后，我获得基于操作系统的适当的 URI，并用`rn-fetch-blob`读取文件。我将这些数据转换成一个缓冲区，因为在创建一个预先签名的 URL 时，上的`aws-sdk`接受某些类型的文件。然后我沿着`getUploadInfo()`通过`fileInfo`和`file`。`getUploadInfo()`然后使用我们之前设置的`s3`实例创建一个预签名的 URL，并执行一些不正规的字符串操作(需要重构)来获取校验和。现在，我可以使用这个校验和(由 Amazon 代码创建)从 Rails 获取直接上传的 URL 和头。最后，我将文件上传到 AWS，并返回我在代码中发送到 Rails 的`signed_id`。

最终，这是一个非常令人沮丧的问题。然而，当我上传一个文件，看到用户资料图像发生变化时，感觉真好。我真的站了起来，双手举在空中，绕着我的家庭办公室跑来跑去。我也很高兴能分享这个解决方案，看看其他人如何改进我所做的，或者找出更好的方法。我不认为这是解决这个问题的最佳方案，但是，这是一个可行的方案。

从我的`yarn.lock`:
–react-native v 0 . 60 . 5
–react-native-image-picker v 1 . 1 . 0
–rn-fetch-blob v 0 . 10 . 16
–AWS-SDK v 2 . 532 . 0

从 React Native 直接上传到 AWS S3 的帖子[首先出现在](https://sethaalexander.com/rails-direct-upload-aws-s3-react-native/) [Seth Alexander](https://sethaalexander.com) 上。