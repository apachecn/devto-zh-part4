# 用 Python (Pyodide)和 WebAssembly 创建 Dev 的离线页面🦄💡✨

> 原文：<https://dev.to/sayanarijit/create-dev-s-offline-page-with-python-pyodide-and-webassembly-241h>

受这篇文章的启发:

[![sendilkumarn](img/75ababb1cfcd601b558af3fa3f9c9e35.png)](/sendilkumarn) [## 用(微小的)GO 和 WebAssembly 创建 Dev 的离线页面🦄💡✨

### sendil Kumar 7 月 6 日 194 分钟阅读

#go #webassembly #showdev](/sendilkumarn/create-dev-s-offline-page-with-tiny-go-and-webassembly-9n6)

我决定用 Python 试一试。

这是我第一次写我想做很久的 WebAssembly。为此，我使用了 [Pyodide](https://github.com/iodide-project/pyodide) 。

## 我们开始吧

首先[下载 Pyodide](https://github.com/iodide-project/pyodide/releases) ，解压并复制`pyodide.js`文件到你当前的目录下。

现在我们把服务器写在`server.py`里。

```
from http.server import BaseHTTPRequestHandler, HTTPServer

class HTTPRequestHandler(BaseHTTPRequestHandler):
    """Request handler class"""

    def do_GET(self):
        """Handler for GET requests."""

        # Send response status code
        self.send_response(200)

        # Send headers
        self.send_header("Cache-Control", "no-cache")

        if self.path.endswith(".wasm"):
            # Header to serve .wasm file
            self.send_header("Content-Type", "application/wasm")
        else:
            self.send_header("Content-Type", "text/html")

        self.end_headers()

        # Serve the file contents
        urlpath = self.path
        if urlpath == "/":
            urlpath = "/index.html"

        with open(f".{urlpath}", "rb") as f:
            content = f.read()

        self.wfile.write(content)

def main():
    print("starting server...")

    # Server settings
    server_address = ("localhost", 8080)
    httpd = HTTPServer(server_address, HTTPRequestHandler)

    print("running server...")
    httpd.serve_forever()

if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

于是我们有了`pyodide.js`、`server.py`。

让我们把`index.html`写成

```
<!doctype html>
<html>
   <head>
      <meta charset="utf-8">
      Python wasm
   </head>
   <body>
      <script src="pyodide.js"></script>
      <div id="status">Initializing Python...</div>
      <canvas id="draw-here"></canvas>
      <style>
         .color {
         display: inline-block;
         width: 50px;
         height: 50px;
         border-radius: 50%;
         cursor: pointer;
         margin: 10px;
         }
      </style>
      <div id="colors" style="text-align:center"></div>
      <script>
        window.languagePluginUrl = "/pyodide.js";
        languagePluginLoader.then(() => {
          pyodide.runPython(` ! Python code goes here ! `)
        })
      </script>
   </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们为 canvas 编写 Python 代码。

```
from js import document as doc

# Make the "Initializing Python" status disappear doc.getElementById("status").innerHTML = ""

canvas = doc.getElementById("draw-here")

canvas.setAttribute("width", doc.body.clientWidth)
canvas.setAttribute("height", 300)
ctx = canvas.getContext("2d")
ctx.strokeStyle = "#F4908E"
ctx.lineJoin = "round"
ctx.lineWidth = 5

# Global variables pen = False
lastPoint = (0, 0)

def onmousemove(e):
    global lastPoint

    if pen:
        newPoint = (e.offsetX, e.offsetY)
        ctx.beginPath()
        ctx.moveTo(lastPoint[0], lastPoint[1])
        ctx.lineTo(newPoint[0], newPoint[1])
        ctx.closePath()
        ctx.stroke()
        lastPoint = newPoint

def onmousedown(e):
    global pen, lastPoint
    pen = True
    lastPoint = (e.offsetX, e.offsetY)

def onmouseup(e):
    global pen
    pen = False

canvas.addEventListener("mousemove", onmousemove)
canvas.addEventListener("mousedown", onmousedown)
canvas.addEventListener("mouseup", onmouseup)

# Colors 
div = doc.getElementById("colors")
colors = ["#F4908E", "#F2F097", "#88B0DC", "#F7B5D1", "#53C4AF", "#FDE38C"]

for c in colors:
    node = doc.createElement("div")
    node.setAttribute("class", "color")
    node.setAttribute("id", c)
    node.setAttribute("style", f"background-color: {c}")

    def setColor(e):
        ctx.strokeStyle = e.target.id

    node.addEventListener("click", setColor)
    div.appendChild(node) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过 AJAX 获取文本形式的代码，并使用`pyodide.runPython(code)`执行。

但为了简单起见，我们将直接把代码粘贴在`index.html`中。

* * *

所以最终版本看起来是这样的:

```
<!doctype html>
<html>
   <head>
      <meta charset="utf-8">
      Python wasm
   </head>
   <body>
      <script src="pyodide.js"></script>
      <div id="status">Initializing Python...</div>
      <canvas id="draw-here"></canvas>
      <style>
         .color {
         display: inline-block;
         width: 50px;
         height: 50px;
         border-radius: 50%;
         cursor: pointer;
         margin: 10px;
         }
      </style>
      <div id="colors" style="text-align:center"></div>
      <script>
         window.languagePluginUrl = "/pyodide.js";
         languagePluginLoader.then(() => {
           pyodide.runPython(`
from js import document as doc

# Make the "Initializing Python" status disappear
doc.getElementById("status").innerHTML = ""

canvas = doc.getElementById("draw-here")

canvas.setAttribute("width", doc.body.clientWidth)
canvas.setAttribute("height", 300)
ctx = canvas.getContext("2d")
ctx.strokeStyle = "#F4908E"
ctx.lineJoin = "round"
ctx.lineWidth = 5

# Global variables
pen = False
lastPoint = (0, 0)

def onmousemove(e):
    global lastPoint

    if pen:
        newPoint = (e.offsetX, e.offsetY)
        ctx.beginPath()
        ctx.moveTo(lastPoint[0], lastPoint[1])
        ctx.lineTo(newPoint[0], newPoint[1])
        ctx.closePath()
        ctx.stroke()
        lastPoint = newPoint

def onmousedown(e):
    global pen, lastPoint
    pen = True
    lastPoint = (e.offsetX, e.offsetY)

def onmouseup(e):
    global pen
    pen = False

canvas.addEventListener("mousemove", onmousemove)
canvas.addEventListener("mousedown", onmousedown)
canvas.addEventListener("mouseup", onmouseup)

# Colors

div = doc.getElementById("colors")
colors = ["#F4908E", "#F2F097", "#88B0DC", "#F7B5D1", "#53C4AF", "#FDE38C"]

for c in colors:
    node = doc.createElement("div")
    node.setAttribute("class", "color")
    node.setAttribute("id", c)
    node.setAttribute("style", f"background-color: {c}")

    def setColor(e):
        ctx.strokeStyle = e.target.id

    node.addEventListener("click", setColor)
    div.appendChild(node)
           `)
         })
      </script>
   </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要用`python server.py`运行 webserver，打开 [http://localhost:8080](http://localhost:8080)

如果你有困难，[这里是完整的项目](https://github.com/sayanarijit/pyodide-dev-offline-page)。