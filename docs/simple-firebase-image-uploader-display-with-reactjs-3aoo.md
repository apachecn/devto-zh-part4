# 带 ReactJs 的简单 Firebase 图像上传器/显示器

> 原文：<https://dev.to/clintdev/simple-firebase-image-uploader-display-with-reactjs-3aoo>

作为 react 的新手，我花了大量的时间寻找一个简单且精心制作的教程，来上传图片到 firebase。我发现的大多数教程都令人不快。这让我感到沮丧和彻底失望。我决定向一位朋友寻求帮助，这位朋友后来成了我的导师。没有进一步的行动，让我们开始吧，希望它能帮助那里的人。

现在，我假设你至少有关于 react 和 firebase 的基本知识，如果没有，我推荐这本书- [初学者 React 教程中的一个 Firebase【2019】](https://www.robinwieruch.de/complete-firebase-authentication-react-tutorial)作者[罗宾·威鲁奇](https://www.robinwieruch.de)。一旦得到这本书，你也将被添加到 react 的一个 slack 频道，在那里你可以与其他 react 开发者合作。

**设置燃烧基**

前往[firebase.com](https://firebase.google.com/)，使用您的 gmail 帐户登录。单击“开始”创建您的项目。如果你不知道选择哪个位置，随便选一个，真的没关系。最后，您已经准备好了 firebase 项目。

接下来，我们将把 firebase 添加到我们的 web 应用程序中。单击添加 web 应用程序图标。通过为您的 web 应用程序起一个昵称来注册它。在下一张幻灯片中，复制您的 web 应用程序 Firebase 配置:-

```
// Your web app's Firebase configuration
  var firebaseConfig = {
    apiKey: "AIzaSyC_O7nBCsvepKmcmswxabDyPDafIy8ta60",
    authDomain: "react-tutorial-7bf4c.firebaseapp.com",
    databaseURL: "https://react-tutorial-7bf4c.firebaseio.com",
    projectId: "react-tutorial-7bf4c",
    storageBucket: "",
    messagingSenderId: "145638556257",
    appId: "1:145638556257:web:dce1f6b880d2f342"
  };
  // Initialize Firebase
  firebase.initializeApp(firebaseConfig); 
```

Enter fullscreen mode Exit fullscreen mode

注意:这些是我的配置，不要复制粘贴，而是使用你的。

接下来，我们将从 web 控制台对 firebase 配置进行一些更改。在左侧面板中，开发，转到存储。这里，我们将创建一个默认存储桶，并对存储规则进行一些更改，以允许对新创建的存储桶进行数据读写。单击“开始”并完成设置。同样，不要介意地区，只需点击下一步和完成。

选择`rules`页签，定位这条线

`allow read, write: if request.auth != null;`

将其改为

`allow read, write: if true;`

**创建我们的 React 应用程序**

我们将使用 React 和 Firebase 构建的应用程序将使用脸书官方的 React 样板项目 [create-react-app](https://github.com/facebook/create-react-app) 进行设置。您可以在命令行上全局安装一次，之后您需要时就可以使用它。

`npm install -g create-react-app`

安装完成后，在命令行中设置您的项目，而项目的名称由您决定。之后，在命令行上导航到项目中:

`create-react-app react-image-uploader
cd react-image-uploader`

这将生成必要的 react 文件并安装依赖项来运行您的应用程序。接下来，我们将把 firebase 依赖项添加到我们的应用程序中。运行`npm i firebase --save`但是如果你使用`yarn`你可以运行`yarn add firebase`。

启动您的编辑器并打开您的项目。我更喜欢用`vsCode`。在你的`scr`文件夹中，添加另一个文件夹并命名为`Firebase`。继续在`Firebase`文件夹中添加`index.js`文件。这将保持我们的火焰基地配置。

在`scr\Firebase\index.js`内部，从`firebase/app`模块导入`firebase`。然后是来自`firebase`的`storage`。接下来，粘贴您的配置。将 firebase 存储函数存储在一个变量中，然后导出 firebase 和存储函数。您的文件现在应该是这样的:-

```
import firebase from "firebase/app";
import "firebase/storage";

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: "AIzaSyBZ4xQXULFhh51CV7VPgNnuOEZy3jsVaQQ",
  authDomain: "react-drawer-1.firebaseapp.com",
  databaseURL: "https://react-drawer-1.firebaseio.com",
  projectId: "react-drawer-1",
  storageBucket: "react-drawer-1.appspot.com",
  messagingSenderId: "177095687889",
  appId: "1:177095687889:web:05c8c913de0ba493"
};

// Initialize Firebase

firebase.initializeApp(firebaseConfig);
```

<b>Image Upload Component</b>

On your `scr` folder create another folder and name it `ImageUpload` then create `index.js` file inside it. Paste this code inside the file.

```javascript
import React, { Component } from "react";
import storage from "../Firebase/index";

class ImageUpload extends Component {
  constructor(props) {
    super(props);
    this.state = {
      image: null,
      url: "",
      progress: 0
    };
  }

  handleChange = e => {
    if (e.target.files[0]) {
      const image = e.target.files[0];
      this.setState(() => ({ image }));
    }
  };

  handleUpload = () => {
    const { image } = this.state;
    const uploadTask = storage.ref(`images/${image.name}`).put(image);
    uploadTask.on(
      "state_changed",
      snapshot => {
        // progress function ...
        const progress = Math.round(
          (snapshot.bytesTransferred / snapshot.totalBytes) * 100
        );
        this.setState({ progress });
      },
      error => {
        // Error function ...
        console.log(error);
      },
      () => {
        // complete function ...
        storage
          .ref("images")
          .child(image.name)
          .getDownloadURL()
          .then(url => {
            this.setState({ url });
          });
      }
    );
  };
  render() {
    return (
      <div className="center">
          <br/>
          <h2 className="green-text">React Firebase Image Uploader</h2>
          <br/>
          <br/>
        <div className="row">
          <progress value={this.state.progress} max="100" className="progress" />
        </div>
        <br />
        <br />
        <br />
        <div className="file-field input-field">
          <div className="btn">
            <span>File</span>
            <input type="file" onChange={this.handleChange} />
          </div>
          <div className="file-path-wrapper">
            <input className="file-path validate" type="text" />
          </div>
        </div>
        <button
          onClick={this.handleUpload}
          className="waves-effect waves-light btn"
        >
          Upload
        </button>
        <br />
        <br />
        <img
          src={this.state.url || "https://via.placeholder.com/400x300"}
          alt="Uploaded Images"
          height="300"
          width="400"
        />
      </div>
    );
  }
}

export default ImageUpload; 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我已经使用 css cdn 材质添加了一些样式。你也可以这样做。

这就是了。简单干净

从 [Github](https://github.com/ClintPy/Image-Uploader-React-Firebase) 获取代码。

黑客快乐！！