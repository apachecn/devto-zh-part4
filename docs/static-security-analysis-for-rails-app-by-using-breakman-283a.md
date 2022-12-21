# 利用 Breakman 对 Rails 应用程序进行静态安全性分析

> 原文：<https://dev.to/n350071/static-security-analysis-for-rails-app-by-using-breakman-283a>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的 Rails 笔记

### n350071🇯🇵9 月 11 日 193 分钟阅读

#rails](/n350071/my-rails-note-47cj)

## 如何使用

### 跑刹车手

根据 [Brakeman 库](https://github.com/presidentbeef/brakeman)，在 app 的根目录下运行以下命令。

```
bundle exec brakeman 
```

*   📚[制动员:选项](https://brakemanscanner.org/docs/options/)

### 结果

你可能会得到这样的结果。有时候，结果很长，以至于你可以用`"new": [`关键字搜索新的提醒。

```
{
  "new": [
    {
      "warning_type": "File Access",
      "warning_code": 16,
      "fingerprint": "xxx",
      "check_name": "SendFile",
      "message": "Parameter value used in file name",
      "file": "app/controllers/download_controller.rb",
      "line": xx,
      "link": "https://brakemanscanner.org/docs/warning_types/file_access/",
      "code": "send_file(params[:file_name])",
      "render_path": null,
      "location": {
        "type": "method",
        "class": "DownloadController",
        "method": "file_download"
      },
      "user_input": "params[:file_name]",
      "confidence": "Weak"
    }, 
```

### 具体文件

```
bundle exec brakeman --only-files path/to/file/,path/to2/ --compare ./brakeman-result.json | grep '"new": ' -A25 
```

### 运行在 CI 服务器上

```
bundle exec brakeman -z ./ 
```

*   `-z`:返回 0 作为退出代码。如果检测到任何安全警告，Breakman 将返回非零代码作为默认值。此选项有助于 CI。

### 与上一次比较

```
bundle exec brakeman ./ --compare ./ci/brakeman-scan-result.json 
```

## 假阳性的情况下

写它忽略文件。

```
bundle exec brakeman -I ./ 
```

Breakman 会问你它是否检测到任何警告，那么我认为选项`n`是好的。n 表示`Add warning to ignore list and add note`。注释是一个注释。

*   `-I` : `--interactive-ignore`。(在假阳性时解释)

📚[制动员:忽略误报](https://brakemanscanner.org/docs/ignoring_false_positives/)