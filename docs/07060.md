# 使用纯 bash 的可恢复文件上传(TUS 协议实现)

> 原文：<https://dev.to/adhocore/tus-client-protocol-implementation-for-bash-13e6>

介绍使用 tus 协议上传可恢复文件的 bash 脚本。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[ad hoc ore](https://github.com/adhocore)/[tusc . sh](https://github.com/adhocore/tusc.sh)

### bash 的 TUS 客户端协议实现。

<article class="markdown-body entry-content p-5" itemprop="text">

# adhocore/tusc.sh

[![Latest Version](img/25ed6cf9f307be090f742952c34492e4.png)](https://github.com/adhocore/tusc.sh/releases)[![Travis Build](img/05b16fbcb64b492f346085085c1c191c.png)](https://travis-ci.com/adhocore/tusc.sh?branch=master)[![Software License](img/205e598804cfac2ff3998652ad68d005.png)](https://raw.githubusercontent.com/adhocore/tusc.sh/master/LICENSE)

`tusc`是 [TUS](https://tus.io) 为 bash 实现的客户端协议。

`tusc`让您直接从终端上传大文件到支持 tus 协议的服务器。

如果出现任何问题，您可以重新运行该命令，从停止的地方继续上传。

## 装置

```
# Requirements:
# jq
sudo apt install jq -y
curl -sSLo ~/tusc https://raw.githubusercontent.com/adhocore/tusc.sh/master/tusc.sh
chmod +x ~/tusc && sudo ln -s ~/tusc /usr/local/bin/tusc
```

## 用法和示例

```
 tusc v0.0.1 | (c) Jitendra Adhikari
  tusc is bash implementation of tus-client (https://tus.io)
  Usage
    tusc <--options>
    tusc <host> <file> [algo]
  Options:
    -a --algo      The algorigthm for key &/or checksum.
                   (Eg: sha1, sha256)
    -f --file      The file to upload.
    -h --help      Show help information and usage.
    -H --host      The tus-server host where file is uploaded.
  Examples:
    tusc version                 # prints current version of itself
    tusc --help                  # shows this help
    tusc 0:1080 ww.mp4           # uploads ww.mp4 to http://0.0.0.0:1080
    tusc -f
```

…</article>

[View on GitHub](https://github.com/adhocore/tusc.sh)

### 安装

```
sudo apt install jq -y

curl -sSLo ~/tusc https://raw.githubusercontent.com/adhocore/tusc.sh/master/tusc.sh
chmod +x ~/tusc && sudo ln -s ~/tusc /usr/local/bin/tusc 
```

### 入门

要在本地动手，您可以安装 [tusd](https://github.com/tus/tusd#download-pre-builts-binaries-recommended) 服务器。

然后，

```
# run tusd server (http://0.0.0.0:1080)
tusd -dir ~/.tusd-data &

# start uploading large files
DEBUG=1 tusc --host 0:1080 --file /full/path/to/large/file 
```

上传过程中，您可以使用`Ctrl+C`强制中止上传

然后再继续上传:

```
DEBUG=1 tusc --host 0:1080 --file /full/path/to/large/file 
```

它应该从上次停止的地方开始。

您可以这样检查上传的文件:

```
ls -al ~/.tusd-data 
```

欢迎并高度赞赏反馈和建议。

谢谢:)