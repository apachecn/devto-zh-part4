# WebGL Month. Day 1

> 原文：[https://dev.to/lesnitsky/webgl-month-day-1-19ha](https://dev.to/lesnitsky/webgl-month-day-1-19ha)

# WebGL month

Hi 👋 My name is Andrei. I have some fun experience with WebGL and I want to share it. I'm starting a month of WebGL, each day I will post a WebGL related tutorial. Not Three.js, not pixi.js, WebGL API itself.

[Follow me on twitter](https://twitter.com/lesnitsky_a) to get WebGL month updates or [join WebGL month mailing list](http://eepurl.com/gwiSeH)

## Day 1\. Intro

[![GitHub stars](img/6cfb69d2e2e393a47a74848671853dd2.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/6cb151ea44418ce1e4768385a02ab98b.png)](https://twitter.com/lesnitsky_a)

[Join mailing list](http://eepurl.com/gwiSeH) to get new posts right to your inbox

[Source code available here](https://github.com/lesnitsky/webgl-month)

Built with

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

Welcome to day 1 of WebGL month. In this article we'll get into high level concepts of rendering which are improtant to understand before approaching actual WebGL API.

WebGL API is often treated as 3D rendering API, which is a wrong assumption. So what WebGL does?
To answer this question let's try to render smth with canvas 2d.

We'll need simple html

📄 index.html

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    WebGL Month
  </head>
  <body></body>
</html> 
```

and canvas

📄 index.html

```
 <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      WebGL Month
    </head>
-   <body></body> +   <body>
+     <canvas></canvas>
+   </body>
  </html> 
```

Don't forget beloved JS

📄 index.html

```
 </head>
    <body>
      <canvas></canvas>
+     <script src="./src/canvas2d.js"></script>
    </body>
  </html> 
```

📄 src/canvas2d.js

```
console.log('Hello WebGL month'); 
```

Let's grab a reference to canvas and get 2d context

📄 src/canvas2d.js

```
- console.log('Hello WebGL month');+ console.log('Hello WebGL month'); + 
+ const canvas = document.querySelector('canvas');
+ const ctx = canvas.getContext('2d'); 
```

and do smth pretty simple, like drawing a black rectangle

📄 src/canvas2d.js

```
 const canvas = document.querySelector('canvas');
  const ctx = canvas.getContext('2d');
+ 
+ ctx.fillRect(0, 0, 100, 50); 
```

Ok, this is pretty simple right?
But let's think about what this signle line of code actually did.
It filled every pixel inside of rectangle with black color.

Are there any ways to do the same but w/o `fillRect`?
The answer is yes

Let's implement our own version of

📄 src/canvas2d.js

```
 const canvas = document.querySelector('canvas');
  const ctx = canvas.getContext('2d');

- ctx.fillRect(0, 0, 100, 50);
+ function fillRect(top, left, width, height) {
+ 
+ } 
```

So basically each pixel is just a color encoded in 4 integers. R, G, B channel and Alpha.
To store info about each pixel of canvas we'll need a `Uint8ClampedArray`.
The size of this array is `canvas.width * canvas.height` (pixels count) `* 4` (each pixel has 4 channels).

📄 src/canvas2d.js

```
 const ctx = canvas.getContext('2d');

  function fillRect(top, left, width, height) {
- +     const pixelStore = new Uint8ClampedArray(canvas.width * canvas.height * 4);
  } 
```

Now we can fill each pixel storage with colors. Note that alpha component is also in range unlike CSS

📄 src/canvas2d.js

```
 function fillRect(top, left, width, height) {
      const pixelStore = new Uint8ClampedArray(canvas.width * canvas.height * 4);
+ 
+     for (let i = 0; i < pixelStore.length; i += 4) {
+         pixelStore[i] = 0; // r
+         pixelStore[i + 1] = 0; // g
+         pixelStore[i + 2] = 0; // b
+         pixelStore[i + 3] = 255; // alpha
+     }
  } 
```

But how do we render this pixels? There is a special canvas renderable class

📄 src/canvas2d.js

```
 pixelStore[i + 2] = 0; // b
          pixelStore[i + 3] = 255; // alpha
      }
+ 
+     const imageData = new ImageData(pixelStore, canvas.width, canvas.height);
+     ctx.putImageData(imageData, 0, 0);
  }
+ 
+ fillRect(); 
```

Whoa 🎉 We filled canvas with a color manually iterating over each pixel! But we're not taking into account passed arguments, let's fix it.

Calculate pixel indices inside rectangle

📄 src/canvas2d.js

```
 const canvas = document.querySelector('canvas');
  const ctx = canvas.getContext('2d');

+ function calculatePixelIndices(top, left, width, height) {
+     const pixelIndices = [];
+ 
+     for (let x = left; x < left + width; x++) {
+         for (let y = top; y < top + height; y++) {
+             const i =
+                 y * canvas.width * 4 + // pixels to skip from top
+                 x * 4; // pixels to skip from left
+ 
+             pixelIndices.push(i);
+         }
+     }
+ 
+     return pixelIndices;
+ }
+ 
  function fillRect(top, left, width, height) {
      const pixelStore = new Uint8ClampedArray(canvas.width * canvas.height * 4); 
```

and iterate over these pixels instead of the whole canvas

📄 src/canvas2d.js

```
 function fillRect(top, left, width, height) {
      const pixelStore = new Uint8ClampedArray(canvas.width * canvas.height * 4);
+     
+     const pixelIndices = calculatePixelIndices(top, left, width, height); 
-     for (let i = 0; i < pixelStore.length; i += 4) {
+     pixelIndices.forEach((i) => {
          pixelStore[i] = 0; // r
          pixelStore[i + 1] = 0; // g
          pixelStore[i + 2] = 0; // b
          pixelStore[i + 3] = 255; // alpha
-     } +     }); 
      const imageData = new ImageData(pixelStore, canvas.width, canvas.height);
      ctx.putImageData(imageData, 0, 0);
  }

- fillRect();
+ fillRect(10, 10, 100, 50); 
```

Cool 😎 We've just reimplemented `fillRect`! But what does it have in common with WebGL?

[![Everything](img/599fe4227437109c3ced8277e2c62dfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--19XCvTGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/thanos-everyhting.jpg)

That's exactly what WebGL API does – *it calculates color of each pixel and fills it with calculated color*

### What's next?

In next article we'll start working with WebGL API and render a WebGL "Hello world". See you tomorrow

[![GitHub stars](img/6cfb69d2e2e393a47a74848671853dd2.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/6cb151ea44418ce1e4768385a02ab98b.png)](https://twitter.com/lesnitsky_a)

[Join mailing list](http://eepurl.com/gwiSeH) to get new posts right to your inbox

[Source code available here](https://github.com/lesnitsky/webgl-month)

Built with

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

### Homework

Extend custom `fillRect` to support custom colors