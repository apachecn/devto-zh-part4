# Hugo + Firebase

> 原文：<https://dev.to/uatthaphon/hugo-firebase-5dc9>

很久以前，Deveploper 一直在工作，不管是自由职业公司工作还是做自己的项目都很好，但最近才意识到我们现在还没有时间。

当我想到了，我决定建立一个 portfolio 网站，但如果我只是做了一个展示，我没有什么可以像其他人一样拿出来展示，他就用一个东西。

在互联网上进行了一段时间的知识获取后，得出了一个结论:[Hugo](https://gohugo.io/)[【firebase】](https://firebase.google.com/)的结论。

## 为什么 Hugo + Firebase

在收购工具和托管网站之前，这次我先问了自己的需求，我想要一些简单的东西，只是构建。

1.  快速载入
2.  很容易写博客，只要[马克敦](https://th.wikipedia.org/wiki/%E0%B8%A1%E0%B8%B2%E0%B8%A3%E0%B9%8C%E0%B8%81%E0%B8%94%E0%B8%B2%E0%B8%A7%E0%B8%99%E0%B9%8C)够了。
3.  易于开发。
4.  易于维护、维护和管理代码段
5.  不要对安全系统头痛
6.  免费托管易于管理

那就是工具选择的起源，也就是雨果的静态站点生成器+光纤主机这次。

## 为什么雨果

Hugo 选择的主要原因

*   html 编译速度
*   project management 部分中的结构简单性
*   很容易开发，比如当我们做 Theme 更新时，Hugo 会自动为我们做更新。
*   简单易懂的文件

这次安装，我在 Mac 机器上进行了安装，如果谁使用了 Window 或 Linux，看看如何在[【安装 Hugo】t1 中进行安装。](https://gohugo.io/getting-started/installing/)

### 雨果的安装

开始，准备好了，然后打开 iTerm 或 Mac 终端，键入命令。

```
# ติดตั้ง Hugo ด้วย Homebrew
brew install hugo 
```

**如果任何人尚未登录，则先通过键入命令进行安装。

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 
```

安装完成后，检查安装是否顺利。

```
# เรียกดูเวอร์ชั่นของ hugo ที่ทำการติดตั้ง
hugo version 
```

就这样，我们就能得到少数的雨果。

### 用 Hugo 构建 web

键入命令开始重建项目。

```
hugo new site dejo 
```

现在，我们可以在一个名为 dejo 的文件夹中找到一个项目。

### 为项目选择主题

Hugo theme 提供了很多选择，我们可以从[【https://themes】进入。gohugo .io/](https://themes.gohugo.io/) 对于本文选择的主题，命名为[【海德主题】T3⾹。](https://themes.gohugo.io/hyde/)

```
# เข้ามายังโฟลเดอร์ themes
cd dejo/themes/

# โคลน Theme ที่ชื่อว่า hyde
git clone https://github.com/spf13/hyde.git

# ทำการเพิ่ม Hyde Theme เข้าไปยัง config.toml
echo 'theme = "Hyde"' >> config.toml 
```

就这样，我们得到了一个美丽的主题。

### 为我们的博客添加一篇文章

```
# ทำการสร้างไฟล์ที่มีขื่อว่า first-post.md ในโฟลเดอร์ posts
hugo new posts/first-post.md 
```

写完文章后，我们通过键入命令来查看我们的网站的外观。

```
# สร้าง server จำลองพร้อมทั้งเปิดใช้งาน draft โหมด
hugo server -D 
```

其余的是把网站的顶点装饰成我们喜欢的风格，并增加我们网站的内容。

## [t1㎡为什么要纤维碱](#%E0%B8%97%E0%B8%B3%E0%B9%84%E0%B8%A1%E0%B8%95%E0%B9%89%E0%B8%AD%E0%B8%87-firebase)

我选择使用 Firebase 的主要原因是

*   很容易再添加自定义域，也提供了 SSL，让我们免费使用它。
*   ฟรี全球 CDN 边缘服务器

首先，我们需要在 [Firebase 控制台](https://console.firebase.google.com/)中创建一个项目来完成我们的网站主机，或者如果没有，我们也可以。

### 安装和操作规程

在 Firebase Console 中成功创建项目后，我们必须在其中安装[【firebase CLI 工具】t1㎡firebase。js〔T3〕在我们安装了节点后，先把它整理好。js 成功了，让我们键入命令。](https://firebase.google.com/docs/cli/)

```
# ติดตั้ง firebase tools ด้วย Node.js
npm install -g firebase-tools 
```

接下来，我们还需要用命令将 Firebase 工具连接到我们的 Google 帐户。

```
firebase login 
```

此命令将打开浏览器，以便我们选择一个帐户并执行登录。我们可以使用`firebase logout`命令在我们做笔的情况下执行注销。

使用`firebase list`命令查看我们的 Firsbase project 中所有项目的列表，其中所有项目的列表应与中的项目列表相同。

让我们更改我们要安装 firebase 的目录项目，这里是我们已经创建的 dejo，并键入命令以执行默认设置。

```
firebase init 
```

从这里开始

*   在“公平查询”中选择托管
*   选择刚刚在 Firebase 控制台中创建的所需项目
*   为 rules 文件数据报选择默认
*   选择发布目录(公共)的默认
*   选择“No”，如果我们执行单个 a-page app，但对我来说，选择“Yes”。

然后我们就可以得到两个新的文件，它们在文件夹项目中是`firebase.json`和`.firebaserc`的。

我们可以在 git 中使用 ignore `.firebaserc`因为这个文件只是用来存储我们正在使用的项目在 firebase 中使用的是什么工具。

```
{
  "hosting": {
    "public": "public",
    "ignore": [
      "**/.*",
      "firebase.json",
    ],
  }
} 
```

现在我们已经准备好了在我们的新项目中执行代码的地方，把它放在我们的 Firebase Hosting，让我们键入命令。

```
firebase deploy 
```

那就等着..一旦转移完成，我们就可以用托管部分的 Firebase Console 页面上提供的 URL 访问我们的新网站。

## 现在怎么办？

在本文中，我们只讨论了 Hugo 安装基础和 Firebase 主机对 deploy 的设置问题。

就我个人而言，除了美化和增加内容外，下一步也将是如此。

*   将域绑定到 Firebase hosting
*   对项目设置标头
*   การทำ持续集成

哦，我的耳朵！我的天啊！还有很多事情要完成