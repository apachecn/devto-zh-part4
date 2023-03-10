# 在 Vue.js 中拖放文件上传

> 原文：<https://dev.to/raymondcamden/drag-and-drop-file-upload-in-vue-js-11kd>

这篇文章不会太长。我必须为一个朋友做一个小演示，演示拖放和上传，所以我想我应该把代码分享给其他人。老实说，这主要是对我来说，所以当我需要在几个月内再次建立这一点，我会谷歌，并最终回到这里完全惊讶，我已经写好了。

首先，我不想在这里讨论拖放的机制。MDN Web 文档有一篇关于这个的很棒的文章(当然他们有): [HTML 拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) 。在我的例子中，我不关心如何使 DOM 项可拖动，而是让我的代码响应 drop 事件。

对于我所需要的，我必须处理两个事件，`drop`和`dragover`。处理`drop`是有道理的。老实说，我不太清楚为什么我需要处理`dragover`，但是代码非常小，因为你只需要防止默认行为。

制作这个演示还教会了我一些关于 Vue 的事情。我习惯于这样构建我的 Vue 应用:

```
<div id="app">
Here is where the awesome happens...
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后我的 div 被传递给 Vue:

```
const app = new Vue({
    el:'#app',
    //lots more stuff here
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我想用`<div id="app">` app 本身做点什么呢？事实证明，您可以在那里添加 Vue 指令。我想这是有道理的，但我从来没有尝试过。我能够指定我的整个 Vue 应用程序“区域”被拖放支持所覆盖。

好了，现在我们来看看代码。我将从 HTML 开始。

```
<html>
<body>
<div id="app" v-cloak @drop.prevent="addFile" @dragover.prevent>
  <h2>Files to Upload (Drag them over)</h2>
  <ul>
    <li v-for="file in files">
      {{ file.name }} ({{ file.size | kb }} kb) <button @click="removeFile(file)" title="Remove">X</button>
    </li>
  </ul>

  <button :disabled="uploadDisabled" @click="upload">Upload</button>
</div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，您可以看到我的两个事件处理程序。正如我所说的，对于`dragover`,我们需要做的就是防止默认行为，这使得这部分变得简短而甜蜜。在`drop`事件`addFile`中，我将处理文件列表的生成。

在 div 中，我跟踪你想要上传的文件。对于每个条目，我输出名称、大小(通过一个过滤器`kb`)，并添加一个简单的按钮让您删除该条目。

最后，我有一个按钮来启动上传。在我的演示中，我没有使用任何形式的“加载”小部件，也没有在完成后清空文件。如果有人想看，尽管问！

好了，现在是密码。

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

Vue.filter('kb', val => {
  return Math.floor(val/1024);  
});

const app = new Vue({
  el:'#app', 
  data: {
    files:[]
  },
  computed: {
    uploadDisabled() {
      return this.files.length === 0;
    }
  },
  methods:{
    addFile(e) {
      let droppedFiles = e.dataTransfer.files;
      if(!droppedFiles) return;
      // this tip, convert FileList to array, credit: https://www.smashingmagazine.com/2018/01/drag-drop-file-uploader-vanilla-js/
      ([...droppedFiles]).forEach(f => {
        this.files.push(f);
      });
    },
    removeFile(file){
      this.files = this.files.filter(f => {
        return f != file;
      });      
    },
    upload() {

      let formData = new FormData();
      this.files.forEach((f,x) => {
        formData.append('file'+(x+1), f);
      });

      fetch('https://httpbin.org/post', {
        method:'POST',
        body: formData
      })
      .then(res => res.json())
      .then(res => {
         console.log('done uploading', res);
      })
      .catch(e => {
        console.error(JSON.stringify(e.message));
      });

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

在顶部你可以看到我的简单的`kb`过滤器来渲染文件大小更好一点。在 Vue 应用程序中，我有一个数据项`files`，注意`uploadDisabled`是如何作为一个很好的计算属性工作的。

在`addFile`中，我使用拖放 API 来访问被删除的文件(如果有的话)。这个演示让你拖动一个文件，或者 100 个文件(不要那样做)。然后我遍历每一个并将它们添加到`files`值中。请记住，当用户有意向 web 应用程序提供文件时，您现在拥有对该文件的读取权限。这就是我能够显示文件大小的方式。我可以在这里做更多的事情，比如验证文件类型，设置每个文件的最大大小，甚至设置允许的总大小。

最后，我的`upload`方法只是点击 httpbin.org，它将回显它被发送的内容。我创建了一个`FormData`对象，并添加了每个文件。记住，用户把文件放到应用程序上，我们就可以从中读取了。

仅此而已。我希望这个简单的演示有所帮助！

*标题图片由[吉米·张](https://unsplash.com/@photohunter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash* 上拍摄