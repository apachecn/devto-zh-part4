# 使用 Vue.js 读取图像大小和尺寸

> 原文：<https://dev.to/raymondcamden/reading-image-sizes-and-dimensions-with-vue-js-53ek>

几周前，我写过([“使用 Vue.js 读取客户端文件进行验证”](https://dev.to/raymondcamden/reading-client-side-files-for-validation-with-vue-js-2io9))一个使用 JavaScript 检查输入字段中选择的文件以执行基本验证的示例。它利用了这样一个事实:一旦用户选择了一个文件，您的代码就可以读取该文件本身。本周早些时候，我一直在思考这个问题，并想到了另一个有用的例子——验证选定的图像的文件大小和尺寸(高度和宽度)。

这篇文章中的代码很大程度上基于我的[之前的例子](https://dev.to/raymondcamden/reading-client-side-files-for-validation-with-vue-js-2io9),所以一定要先阅读这篇博客文章。

让我们从解决两个主要需求开始——获取文件大小和图像尺寸。

文件大小很容易。一旦选择了一个文件，它就会出现在 file 对象的`size`属性中。还有其他可用的属性，比如上次修改的时间，您可以查看 MDN 上的[文件](https://developer.mozilla.org/en-US/docs/Web/API/File)文档了解更多信息。

获得尺寸也很容易，只要你小心。你可以用 JavaScript 制作一个新的图片对象，并指定来源:

```
let img = new Image();
img.src = someUrl; 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以立即检查`img.height`和`img.width`，但是您会发现有时两个结果都会得到`0`。为什么？图像还没有加载！幸运的是这很容易解决:

```
let img = new Image();

img.onload = () => {
    console.log(`the image dimensions are ${img.width}x${img.height}`);
}

img.src = someUrl; 
```

Enter fullscreen mode Exit fullscreen mode

好了，既然如此，让我们从一个简单的显示信息的例子开始。一、布局:

```
<div id="app" v-cloak>

  <input type="file" ref="myFile" @change="selectedFile" accept="image/*"><br/>

  <div v-if="imageLoaded">
    Image size is {{image.size}}<br/>
    Image width and height is {{image.width}} / {{image.height}}
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

第二个`div`标签有条件地出现，你可以看到我显示了我们关心的所有三个属性。注意，我已经在`input`字段中添加了一个`accept="image/*"`。这将有助于把用户引向图像。

这是代码和注释，我将重点关注与上一个例子的不同之处。

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

const app = new Vue({
  el:'#app',
  data: {
    image:{
      size:'',
      height:'',
      width:''
    },
    imageLoaded:false
  },
  methods:{
    selectedFile() {
      this.imageLoaded = false;

      let file = this.$refs.myFile.files[0];
      if(!file || file.type.indexOf('image/') !== 0) return;

      this.image.size = file.size;

      let reader = new FileReader();

      reader.readAsDataURL(file);
      reader.onload = evt => {
        let img = new Image();
        img.onload = () => {
          this.image.width = img.width;
          this.image.height = img.height;
          this.imageLoaded = true;
        }
        img.src = evt.target.result;
      }

      reader.onerror = evt => {
        console.error(evt);
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

首先，size 值是微不足道的——我们只是从 file 对象中复制它。我们使用`readAsDataURL`读取文件，这与前面的例子不同。这将返回一个用图像数据的 base64 版本编码的 URL。一旦我们有了它，我们可以把它分配给一个新的`Image`，等待`onload`，然后得到维度。你可以在下面看到这一点:

[https://codepen.io/cfjedimaster/embed/QXwEyq?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/QXwEyq?height=600&default-tab=result&embed-version=2)

既然您已经看到了基础知识，让我们考虑一个使用验证的例子。我们将以字节为单位指定最大大小、最大宽度和最大高度。下面是更新后的 HTML:

```
<div id="app" v-cloak>

  <input type="file" ref="myFile" @change="selectedFile" accept="image/*"><br/>

  <div v-if="imageError" class="imageError">
    {{ imageError }}
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一真正的变化是抛出错误时显示的可选的`div`。现在让我们看看 JavaScript。

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

// max size, 100KB, width and height
const MAX_SIZE = 100000;
const MAX_WIDTH = 500;
const MAX_HEIGHT = 300;

const app = new Vue({
  el:'#app',
  data: {
    image:{
      size:'',
      height:'',
      width:''
    },
    imageError:''
  },
  methods:{
    selectedFile() {
      this.imageError = '';

      let file = this.$refs.myFile.files[0];

      if(!file || file.type.indexOf('image/') !== 0) return;
      this.image.size = file.size;
      if(this.image.size > MAX_SIZE) {
        this.imageError = `The image size (${this.image.size}) is too much (max is ${MAX_SIZE}).`;
        return;
      }

      let reader = new FileReader();

      reader.readAsDataURL(file);
      reader.onload = evt => {
        let img = new Image();
        img.onload = () => {
          this.image.width = img.width;
          this.image.height = img.height;
          console.log(this.image);
          if(this.image.width > MAX_WIDTH) {
            this.imageError = `The image width (${this.image.width}) is too much (max is ${MAX_WIDTH}).`;
            return;
          }
          if(this.image.height > MAX_HEIGHT) {
            this.imageError = `The image height (${this.image.height}) is too much (max is ${MAX_HEIGHT}).`;
            return;
          }

        }
        img.src = evt.target.result;
      }

      reader.onerror = evt => {
        console.error(evt);
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，这与上一个例子非常相似，除了现在我们已经检查了大小、宽度和高度。请注意，我的代码将*仅*抛出一个错误，例如，如果宽度和高度都太大，您将仅看到第一个错误，但这也可以很容易地更改。下面是实际运行的代码:

[https://codepen.io/cfjedimaster/embed/ewmzjX?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/ewmzjX?height=600&default-tab=result&embed-version=2)

*头部图片由 [Clem Onojeghuo](https://unsplash.com/@clemono2?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 Unsplash* 上拍摄