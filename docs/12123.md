# JavaScript Quickies:用手控制 3D 对象🤯

> 原文：<https://dev.to/xenoxdev/javascript-quickies-controlling-3d-objects-with-hands-498n>

嘿伙计们，怎么了？我们 XenoX 团队的所有人都非常兴奋地通知您，我们正在开始一个新的系列文章，名为*。这些将是你们可以用 Javascript 做的快速实验，以探索新的技术。多亏了 Javascript，我们可以插入各种模块，创建任何东西。唯一的限制是你的想象力。*

 *#### 想法💡

我们都有自己在科幻电影中最喜欢的时刻。这些时刻对我们开发人员来说非常特别，因为我们不禁想知道我们在屏幕上看到的所有酷的科幻技巧是如何变成现实的。每当我看到这样的东西，我的大脑就会立即进入最佳状态，并开始思考所有的技术可能性。它有一种孩童般的魅力，我非常喜欢。

我记得十几岁时看过钢铁侠，他在实验室里与全息物体互动的场景让我完全惊呆了。当我回忆起那个场景时，我开始思考我是否能创造出类似的东西，一些能引发同样快乐的东西。

[![](img/12ffc9e94fc0c9c8b85fcd5da3ae3584.png)](https://i.giphy.com/media/f6hcD3BS1qaWZQVBoD/giphy-downsized-large.gif)

当然，我们还没有足够的技术来创造完全相同的效果，至少现在还没有。但是我们当然可以尝试让我们已经拥有的东西变得更酷。所以我在周末做了这个很酷的小项目来和你们分享。

系好安全带，复仇者们！让我们创造出 ***的手技*** 。

🤓 ***[试现](https://sarthology.github.io/thehandtrick/)*** 🤓

#### 要求🧺

我用普通的 Javascript 做了这个。所以你应该对 Javascript 有一个基本的了解才能理解这个教程。除此之外，我在这里使用了两个库:
**1。三. js** 👉🏼[链接](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene)
**2。Handtrack.js** 👉🏼[链接](https://github.com/victordibia/handtrack.js/)

就是这样。

#### 现在我们来编码吧👩🏽‍💻

代码的 HTML 部分非常简单。我们只是在这里加入了库，并添加了 div 来渲染浏览器中的摄像机镜头:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    The Hand Trick
    <link rel="stylesheet" href="css/style.css" />
  </head>
  <body>
    <!-- Video for handtracker -->
    <div class="tracker">
      <video id="myvideo"></video>
      <canvas id="canvas" class="border"></canvas>
      <button id="trackbutton" disabled onclick="toggleVideo()">Button</button>
      <div id="updatenote">hello</div>
    </div>
    <div class="data">
      <div class="hand-1">
        <p id="hand-x">X: <span>0</span></p>
        <p id="hand-y">Y: <span>0</span></p>
      </div>
    </div>
    <script src="js/three.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/handtrackjs/dist/handtrack.min.js"></script>
    <script src="js/scene.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，让我们快速跳回到 Javascript 方面。如果你知道 Three.js，可以跳过这一部分。对于其他人，我们在这里通过设置细节来创建一个场景。

```
// Setting scene for 3D Object
var scene = new THREE.Scene();
var camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
var vector = new THREE.Vector3();
var renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement); 
```

Enter fullscreen mode Exit fullscreen mode

之后，让我们创建一个 3D 对象来渲染场景。这里我们将定义盒子的几何形状和网格的材质类型。

```
// Creating 3D object
var geometry = new THREE.BoxGeometry(1, 2, 1);
var material = new THREE.MeshBasicMaterial({
  color: "rgba(3, 197, 221, 0.81)",
  wireframe: true,
  wireframeLinewidth: 1
});

var cube = new THREE.Mesh(geometry, material);

scene.add(cube);
camera.position.z = 5; 
```

Enter fullscreen mode Exit fullscreen mode

如果要在 3D 中旋转对象，此步骤是可选的。这样看起来更酷。

```
// Optional animation to rotate the element
var animate = function() {
  requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
};

animate(); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要用 Three.js 做的所有事情。现在让我们来玩一下 Handtrack.js

```
// Creating Canavs for video Input
const video = document.getElementById("myvideo");
const handimg = document.getElementById("handimage");
const canvas = document.getElementById("canvas");
const context = canvas.getContext("2d");
let trackButton = document.getElementById("trackbutton");
let updateNote = document.getElementById("updatenote");

let imgindex = 1;
let isVideo = false;
let model = null;

// Params to initialize Handtracking js
const modelParams = {
  flipHorizontal: true,
  maxNumBoxes: 1,
  iouThreshold: 0.5,
  scoreThreshold: 0.7
};

handTrack.load(modelParams).then(lmodel => {
  model = lmodel;
  updateNote.innerText = "Loaded Model!";
  trackButton.disabled = false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里定义加载 Handtrack js 的参数，但这一步是可选的；你也可以传递一个空的对象。`handTrack.load()`方法将帮助您加载一个模型。一旦 handtrack js 被加载，让我们编写函数在 html 中定义的画布中加载视频流。为此，我们使用了`handTrack.startVideo()`方法。

```
// Method to start a video
function startVideo() {
  handTrack.startVideo(video).then(function(status) {
    if (status) {
      updateNote.innerText = "Video started. Now tracking";
      isVideo = true;
      runDetection();
    } else {
      updateNote.innerText = "Please enable video";
    }
  });
}

// Method to toggle a video
function toggleVideo() {
  if (!isVideo) {
    updateNote.innerText = "Starting video";
    startVideo();
  } else {
    updateNote.innerText = "Stopping video";
    handTrack.stopVideo(video);
    isVideo = false;
    updateNote.innerText = "Video stopped";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以编写代码从 **handtrack.js**
中获取预测数据

```
//Method to detect movement
function runDetection() {
  model.detect(video).then(predictions => {
    model.renderPredictions(predictions, canvas, context, video);
    if (isVideo) {
      requestAnimationFrame(runDetection);
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 🧙的真正绝招🏼‍♂️

以上所有代码基本上都可以从库的文档中复制粘贴。但是真正的挑战是整合两者以获得想要的结果。

诀窍是在视频画布上跟踪手的坐标，并对三个 js 对象进行相应的更改。

来自`model.detect()`方法的`prediction`对象返回以下对象:

```
{  "bbox":  [x,  y,  width,  height],  "class":  "hand",  "score":  0.8380282521247864  } 
```

Enter fullscreen mode Exit fullscreen mode

`bbox`给出手周围绘制的方框的坐标、宽度和高度值。但是坐标不是中心点。为了计算中心点，我们使用这个简单的公式:

```
 let midvalX = value[0] + value[2] / 2;
 let midvalY = value[1] + value[3] / 2; 
```

Enter fullscreen mode Exit fullscreen mode

另一个问题是对象的画布和跟踪器的画布的比例非常大。此外，两个源的中心点原点不是中心。要做到这一点，首先我们必须移动坐标，这样视频画布的原点就可以在中心。

一旦做到这一点，解决规模问题就容易了。所以最后的结果会是这样的。

```
//Method to detect movement
function runDetection() {
  model.detect(video).then(predictions => {
    model.renderPredictions(predictions, canvas, context, video);
    if (isVideo) {
      requestAnimationFrame(runDetection);
    }
    if (predictions.length > 0) {
      changeData(predictions[0].bbox);
    }
  });
}

//Method to Change prediction data into useful information
function changeData(value) {
  let midvalX = value[0] + value[2] / 2;
  let midvalY = value[1] + value[3] / 2;

  document.querySelector(".hand-1 #hand-x span").innerHTML = midvalX;
  document.querySelector(".hand-1 #hand-y span").innerHTML = midvalY;

  moveTheBox({ x: (midvalX - 300) / 600, y: (midvalY - 250) / 500 });
}

//Method to use prediction data to render cube accordingly
function moveTheBox(value) {
  cube.position.x = ((window.innerWidth * value.x) / window.innerWidth) * 5;
  cube.position.y = -((window.innerHeight * value.y) / window.innerHeight) * 5;
  renderer.render(scene, camera);
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，就是这样。现在，您可以用手控制 3D 对象。我已经在 Github 上公开了代码，所以去看看吧。克隆它，运行它，并从中获得乐趣。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[绝招](https://github.com/sarthology/thehandtrick)

### 🖐🏼用手控制 3D 对象

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/45a2a3ddd4a42e83a517bc20dba3b9df.png)](https://camo.githubusercontent.com/d699f63d8b42c38a0702bd7494013ee7a26c93daf9dc99b430f876286c7cd190/68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f70726163746963616c6465762f696d6167652f66657463682f732d2d653953736e3732692d2d2f635f696d616767615f7363616c652c665f6175746f2c666c5f70726f67726573736976652c685f3432302c715f6175746f2c775f313030302f68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f70726163746963616c6465762f696d6167652f66657463682f732d2d324d412d4e6c52542d2d2f635f696d616767615f7363616c65253243665f6175746f253243666c5f70726f6772657373697665253243685f343230253243715f6175746f253243775f313030302f68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f77757179723068756a6c783875636832723174792e676966)

# 手的把戏

[演示](https://sarthology.github.io/thehandtrick/)

我们都有自己在科幻电影中最喜欢的时刻。这些时刻对我们开发人员来说非常特别，因为我们不禁想知道我们在屏幕上看到的所有酷的科幻技巧是如何变成现实的。每当我看到这样的东西，我的大脑就会立即进入最佳状态，并开始思考所有的技术可能性。它有一种孩童般的魅力，我非常喜欢。

我记得十几岁时看过钢铁侠，他在实验室里与全息物体互动的场景让我完全惊呆了。当我回忆起那个场景时，我开始思考我是否可以创造一些类似的东西，一些引发同样快乐的东西

*查看此处教程* [![](img/70285076f961b3da62a038de1ecb9afc.png)](https://dev.to/teamxenox/javascript-quickies-controlling-3d-objects-with-hands-498n)

## 先决条件

在本地运行之前，您必须安装这些程序

*   三. js
*   手动跟踪. js

## 加入团队

成为最酷项目的一部分，加入…

</article>

[View on GitHub](https://github.com/sarthology/thehandtrick)

#### 总结起来👋🏻

故事才刚刚开始。这是这个系列的第一篇教程，我计划将这个实验向前推进一步。我希望有一些贡献者。如果你想为这个项目做贡献，只要在 XenoX Multiverse 上生成一个 pull 请求，我会联系你的。

XenoX 团队最初是一个小型开发团队，为了好玩而从事开源项目。但几个月来，它变得越来越大，越来越强。这就是为什么我创建了 XenoX Multiverse，这是 XenoX 团队所有开源项目的家园。如果你想成为我们中的一员，就写下你的名字，开始投稿吧！

#### 我走之前

我们现在有一个给戴夫的电报频道了！在旅途中充分利用 Dev.to，以及我们每天发送的外部文章、视频和投票！
👉🏼[链接](https://t.me/bestofdev)

[![](img/214c62ffc79f346daac8c574a334dd38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dtn4eVzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xftk5giyty6bivniwnfa.png)

我该走了。就这些了，伙计们！请记住，这只是一个帮助你激发创造力的快速实验。您可以在其中添加更多很酷的特性，如果您觉得它运行缓慢，您可以稍后优化代码。重点是尽可能快地学习新的东西。希望你喜欢这篇文章。

回头见！

[![](img/e57bced7781a7f74a8388b428f57f47f.png)](https://i.giphy.com/media/MG4KfpZc8EipG/giphy.gif)*