# 理解和实现简单的 PYTHON REST API

> 原文：<https://dev.to/thecraftman/understanding-and-implementing-a-simple-rest-api-jok>

## 什么是 RESTFUL API？

`It is definitely an architectural style of building APis`，
REST 是 web 的底层架构原则。web 的惊人之处在于，客户端(浏览器)和服务器可以以复杂的方式进行交互，而无需客户端事先了解服务器及其托管的资源。关键的约束是服务器和客户机必须在所使用的媒体上达成一致，在 web 的情况下是 HTML。

RESTful API——也称为 RESTful web 服务——基于表述性状态转移 [REST](https://searchmicroservices.techtarget.com/definition/REST-representational-state-transfer)

api 的一个实际例子是，当*你和某人一起进入餐厅，你想为自己和那个人点一份食物，有一个服务员接受你的请求，在厨房为你准备一个响应并把它还给你，服务员充当 api*

`The waiter acts as HTTP and the kitchen is the web so it sends a request and the kitchen sends it back, the kitchen gives you the response back using HTTP response.`

[![screenshot](img/bfbbb01872d3b4d0cc41a809b0f6ff52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0MFv9uCZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qph.fs.quoracdn.net/main-qimg-6f8083f77cbb58baf7938d14c43ebeb5.webp)T3】

`Your standard database CRUD (Create, Read, Update, Delete) is translated to the HTTP verbs: POST, GET, PUT, DELETE. Following that convention and passing on the right parameters basically means that you have translated some of the business logic onto HTTP. It's as simple as that.`

-应该使用 GET 从 web 服务请求信息。

-应该使用 POST 将数据放入 web 服务器，其中没有关于 web 服务应该将数据放在哪里的规范。这可以被认为是插入的等价物。

-当您想要指定数据的去向时，应该使用 PUT。这是一个幂等操作，因为重复它不会改变每次重复调用的任何内容。或者，这可以被认为是更新的等价物。

-而删除显然是用来从 web 服务器上删除一些数据或资源。

##### 现在，让我们构建一个支持+、-、/、*的 restful api

##### 方法:发布

##### 资源:+，-，/，*

### 资源法图表

| 资源 | 方法 | 小路 | 用于/地址 | 因素 | 错误代码/状态 |
| --- | --- | --- | --- | --- | --- |
| + | 邮政 | /添加 | 将两个数相加 | X:int，y:int | 200:好，300:失踪 |
| - | 邮政 | /减去 | 减去两个数 | x:int，y:int | 200:好，300:失踪 |
| * | 邮政 | /相乘 | 两个数相乘 | X:int，y:int | 200:好，300:失踪 |
| / | 邮政 | /划分 | 将两个数相除 | x:int，y:int | 200:ok，301:missing，302:y 是零，所以它不会分裂 |

# 代码库

```
from flask import Flask, jsonify, request
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

def checkPostedData(postedData, functionName):
    if (functionName == "add" or functionName == "subtract" or functionName == "multiply"):
        if "x" not in postedData or "y" not in postedData:
            return 301 #Missing parameter
        else:
            return 200
    elif (functionName == "division"):
        if "x" not in postedData or "y" not in postedData:
            return 301
        elif int(postedData["y"])==0:
            return 302
        else:
            return 200

class Add(Resource):
    def post(self):
        #If I am here, then the resouce Add was requested using the method POST

        #Step 1: Get posted data:
        postedData = request.get_json()

        #Steb 1b: Verify validity of posted data
        status_code = checkPostedData(postedData, "add")
        if (status_code!=200):
            retJson = {
                "Message": "An error happened",
                "Status Code":status_code
            }
            return jsonify(retJson)

        #If i am here, then status_code == 200
        x = postedData["x"]
        y = postedData["y"]
        x = int(x)
        y = int(y)

        #Step 2: Add the posted data
        ret = x+y
        retMap = {
            'Message': ret,
            'Status Code': 200
        }
        return jsonify(retMap)

class Subtract(Resource):
    def post(self):
        #If I am here, then the resouce Subtract was requested using the method POST

        #Step 1: Get posted data:
        postedData = request.get_json()

        #Steb 1b: Verify validity of posted data
        status_code = checkPostedData(postedData, "subtract")

        if (status_code!=200):
            retJson = {
                "Message": "An error happened",
                "Status Code":status_code
            }
            return jsonify(retJson)

        #If i am here, then status_code == 200
        x = postedData["x"]
        y = postedData["y"]
        x = int(x)
        y = int(y)

        #Step 2: Subtract the posted data
        ret = x-y
        retMap = {
            'Message': ret,
            'Status Code': 200
        }
        return jsonify(retMap)

class Multiply(Resource):
    def post(self):
        #If I am here, then the resouce Multiply was requested using the method POST

        #Step 1: Get posted data:
        postedData = request.get_json()

        #Steb 1b: Verify validity of posted data
        status_code = checkPostedData(postedData, "multiply")

        if (status_code!=200):
            retJson = {
                "Message": "An error happened",
                "Status Code":status_code
            }
            return jsonify(retJson)

        #If i am here, then status_code == 200
        x = postedData["x"]
        y = postedData["y"]
        x = int(x)
        y = int(y)

        #Step 2: Multiply the posted data
        ret = x*y
        retMap = {
            'Message': ret,
            'Status Code': 200
        }
        return jsonify(retMap)

class Divide(Resource):
    def post(self):
        #If I am here, then the resouce Divide was requested using the method POST

        #Step 1: Get posted data:
        postedData = request.get_json()

        #Steb 1b: Verify validity of posted data
        status_code = checkPostedData(postedData, "division")

        if (status_code!=200):
            retJson = {
                "Message": "An error happened",
                "Status Code":status_code
            }
            return jsonify(retJson)

        #If i am here, then status_code == 200
        x = postedData["x"]
        y = postedData["y"]
        x = int(x)
        y = int(y)

        #Step 2: Multiply the posted data
        ret = (x*1.0)/y
        retMap = {
            'Message': ret,
            'Status Code': 200
        }
        return jsonify(retMap)

api.add_resource(Add, "/add")
api.add_resource(Subtract, "/subtract")
api.add_resource(Multiply, "/multiply")
api.add_resource(Divide, "/division")

@app.route('/')
def hello_world():
    return "Hello World!"

if __name__=="__main__":
    app.run(debug=True) 
```

为什么不在 Twitter 上分享这篇文章的链接呢？把话传出去！

别忘了在 twitter 上关注我

谢谢！