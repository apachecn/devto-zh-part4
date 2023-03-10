# 用于机器学习模型的无服务器 API

> 原文：<https://dev.to/ibmdeveloper/serverless-apis-for-machine-learning-models-43lk>

IBM 的[模型资产交易所](https://developer.ibm.com/exchanges/models/)为开发者提供了一个[管理的免费机器学习模型列表](https://developer.ibm.com/exchanges/models/all/)。目前发布的模型包括从图像中检测面部的[情绪](https://developer.ibm.com/exchanges/models/all/max-facial-emotion-classifier/)或[年龄](https://developer.ibm.com/exchanges/models/all/max-facial-age-estimator/)、预测天气[、将](https://developer.ibm.com/exchanges/models/all/max-weather-forecaster/)[语音转换为文本](https://developer.ibm.com/exchanges/models/all/max-speech-to-text-converter/)等等。模型经过预先训练，可以在云中使用。

模型作为一系列[公共 Docker 图像](https://hub.docker.com/search?q=codait&type=image)发布。图像自动公开一个 HTTP API 用于模型预测。模型库中的文档解释了如何在本地运行映像(使用 Docker)或部署到云中(使用 Kubernetes)。这让我想到…

**MAX 型号可以用于无服务器功能吗？**🤔

在无服务器平台上运行机器学习模型可以利用水平可扩展性来并行处理大量计算密集型分类任务。再加上无服务器的定价结构(“*免费使用*”)，这可能是在云中执行模型分类的一种极其廉价和有效的方式。

**接受挑战！**

经过几天的试验，我已经找到了一种简单的方法来自动将 MAX 模型作为无服务器 API 在 IBM 云函数上公开。🎉🎉🎉

我已经在下面给出了如何使用一个简单的脚本从模型中创建这些 API 的说明。如果你只是想使用这些模型，请遵循这些说明。如果你有兴趣了解这是如何工作的，请继续阅读，我会在后面解释我是怎么做的...

## 在 IBM 云函数上运行 MAX 模型

[这个库](https://github.com/jthomas/serverless-max-models)包含一个 [bash 脚本](https://github.com/jthomas/serverless-max-models/blob/master/build.sh)，它用 MAX 模型构建定制 Docker 运行时，以便在 [IBM 云函数](https://cloud.ibm.com/openwhisk)上使用。将这些映像推送到 Docker Hub 允许 IBM 云功能将它们用作定制运行时。从这些定制运行时映像创建的 Web 动作公开了模型文档中描述的相同预测 API。它们无需进一步修改或定制代码即可使用。

### 先决条件

在继续之前，请按照下面的链接设置以下工具。

*   [码头工人](https://www.docker.com/)
*   码头枢纽账户
*   [IBM 云账户](https://cloud.ibm.com/registration)
*   [IBM 云功能 CLI 安装完毕](https://cloud.ibm.com/openwhisk/learn/cli)

**查看“[无服务器最大型号](https://github.com/jthomas/serverless-max-models)资源库。从该文件夹运行以下所有命令。**

```
git clone https://github.com/jthomas/serverless-max-models 
cd serverless-max-models 
```

### 构建自定义运行时映像

*   用 [MAX model names](https://hub.docker.com/search?q=codait&type=image) 设置以下环境变量(`MODELS`)并运行构建脚本。
    *   `MODELS`:最大型号名称，如`max-facial-emotion-classifier`
    *   `USERNAME`:坞站枢纽用户名。

```
MODELS="..." USERNAME="..." ./build.sh 
```

这将使用 MAX 模型名称在本地创建 Docker 映像，并将其推送到 Docker Hub，以便在 IBM Cloud 函数中使用。 **IBM Cloud Functions 只支持公共 Docker 映像作为定制运行时。**

### 使用自定义运行时创建动作

*   使用[自定义 Docker 运行时](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions-docker.md)创建一个 Web 动作。

```
ibmcloud wsk action create <MODEL_IMAGE> --docker <DOCKERHUB_NAME>/<MODEL_IMAGE> --web true -m 512 
```

*   检索 Web 操作 URL ( `https://<REGION>.functions.cloud.ibm.com/api/v1/web/<NS>/default/<ACTION>`)

```
ibmcloud wsk action get <MODEL_IMAGE> --url 
```

### 用预测 api 参数调用 web 动作 url

对 Web 操作 URL 使用预测 API 规范中定义的相同 API 请求参数。这将调用模型预测并将结果作为 HTTP 响应返回，例如

```
curl -F "image=@assets/happy-baby.jpeg" -XPOST <WEB_ACTION_URL> 
```

*注意:创建动作后的第一次调用可能会导致长时间的冷启动延迟，因为平台会将远程映像拉入本地注册表。一旦映像在平台中可用，进一步的冷调用和热调用都会快得多。*

## 举例

下面是一个使用`max-facial-emotion-classifier` [MAX 模型](https://developer.ibm.com/exchanges/models/all/max-facial-emotion-classifier/)创建无服务器 API 的例子。已经测试过的模型的更多例子可在[这里](https://github.com/jthomas/serverless-max-models/blob/master/README.md#models)找到。如果遇到问题，请[在 Github 上开一期](https://github.com/jthomas/serverless-max-models/issues)。

### max-面部-情感-分类器

*   [面部情感分类器(`max-facial-emotion-classifier` )](https://developer.ibm.com/exchanges/models/all/max-facial-emotion-classifier/)

首先使用自定义运行时创建操作，然后检索 Web 操作 URL。

```
$ ibmcloud wsk action create max-facial-emotion-classifier --docker <DOCKERHUB_NAME>/max-facial-emotion-classifier --web true -m 512
ok: created action max-facial-emotion-classifier
$ ibmcloud wsk action get max-facial-emotion-classifier --url
ok: got action max-facial-emotion-classifier
https://<REGION>.functions.cloud.ibm.com/api/v1/web/<NS>/default/max-facial-emotion-classifier 
```

根据该模型的 [API 定义](http://max-facial-emotion-classifier.max.us-south.containers.appdomain.cloud/)，预测 API 期望一个带有图像文件的表单提交进行分类。使用来自模型报告的[样本图像](https://github.com/IBM/MAX-Facial-Emotion-Classifier/blob/master/assets/happy-baby.jpeg)，可以使用 curl 测试该模型。

```
$ curl -F "image=@happy-baby.jpeg" -XPOST https://<REGION>.functions.cloud.ibm.com/api/v1/web/<NS>/default/max-facial-emotion-classifier 
```

```
{  "status":  "ok",  "predictions":  [  {  "detection_box":  [  0.15102639296187684,  0.3828125,  0.5293255131964809,  0.5830078125  ],  "emotion_predictions":  [  {  "label_id":  "1",  "label":  "happiness",  "probability":  0.9860254526138306  },  ...  ]  }  ]  } 
```

#### 表现

*示例调用持续时间(冷):* ~4.8 秒

*示例调用持续时间(热):* ~ 800 毫秒

## 这是怎么回事？

### 背景

由于以下原因，使用来自无服务器功能的预训练模型运行机器学习分类在历史上一直具有挑战性…

> 开发人员无法控制(大多数)无服务器云平台中的运行时环境。必须在部署包中提供函数所需的库和依赖项。大多数平台都限制部署包的大小(压缩后大约 50MB，未压缩时大约 250MB)。

机器学习库和模型可以比那些部署大小限制大得多。这将阻止它们包含在部署包中。在调用期间动态加载文件是可能的，但是会导致极长的冷启动延迟和额外的成本。

幸运的是， [IBM Cloud Functions](https://cloud.ibm.com/openwhisk) 是基于开源的无服务器项目， [Apache OpenWhisk](http://openwhisk.incubator.apache.org/) 。该平台支持使用[定制 Docker 图像](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions-docker.md)的定制功能运行时。因此，可以在定制运行时提供机器学习库和模型。这消除了将它们包含在部署包中或在运行时加载的需要。

*有兴趣阅读其他关于使用具有 IBM 云功能的机器学习库和工具包的博文吗？[更多详情](http://jamesthom.as/blog/2018/08/13/serverless-machine-learning-with-tensorflow-dot-js/)见[这些帖子](http://jamesthom.as/blog/2017/08/04/large-applications-on-openwhisk/)。*

### MAX 模型图片

IBM 的[模型资产交换](https://developer.ibm.com/exchanges/models/all/)发布每个模型的 Docker 图像，以及预训练的模型文件。图片展示了一个用于预测的[HTTP API](https://github.com/IBM/MAX-Text-Sentiment-Classifier#3-use-the-model)，使用 Python 和 Flask 构建的端口 5000 上的模型。[API 的 Swagger 文件](http://max-text-sentiment-classifier.max.us-south.containers.appdomain.cloud/)描述了可用的操作、输入参数和响应体。

这些图像使用基于 Flask 的定制应用程序框架( [maxfw](https://pypi.org/project/maxfw/) )来标准化将 MAX 模型公开为 HTTP APIs。这个框架处理输入参数验证、响应编组、CORS 支持等。这允许模型运行时只实现预测 API 处理程序，而不是整个 HTTP 应用程序。

由于框架已经将模型公开为 HTTP API，我开始寻找一种方法来模拟进入框架的外部 HTTP 请求。如果可能的话，我可以从 Python Web 操作中触发这个假请求，根据输入参数执行模型分类。然后，Web 操作会将返回的 HTTP 响应转换为有效的 Web 操作响应参数。

### 烧瓶测试客户端

通读烧瓶[文档](http://flask.pocoo.org/docs/1.0/testing/)，我发现了完美的解决方案！👏👏👏

> Flask 通过公开 Werkzeug 测试客户端并为您处理上下文局部变量，提供了一种测试应用程序的方法。然后，您可以将它用于您最喜欢的测试解决方案。

这允许应用程序路由通过[测试客户端](https://werkzeug.palletsprojects.com/en/0.15.x/test/#werkzeug.test.Client)来执行，而不需要实际运行 HTTP 服务器。

```
max_app = MAXApp(API_TITLE, API_DESC, API_VERSION)
max_app.add_api(ModelPredictAPI, '/predict')
test_client = max_app.app.test_client()
r = test_client.post('/model/predict', data=content, headers=headers) 
```

在无服务器 Python 函数中使用此代码允许函数调用触发预测 API。无服务器函数只需将输入参数转换成假的 HTTP 请求，然后将响应序列化回 JSON。

### python docker 动作

定制的 MAX 模型运行时映像需要实现 Apache OpenWhisk 期望的 T2 HTTP API。这个 API 用于实例化运行时环境，然后在每个请求中传递调用参数。由于运行时映像包含处理请求所需的所有文件和代码，`/init`处理程序变成了[的空操作](https://english.stackexchange.com/questions/25993/what-does-no-op-mean)。`/run`处理器将 [Web 动作 HTTP 参数](https://github.com/apache/incubator-openwhisk/blob/master/docs/webactions.md#http-context)转换成假的 HTTP 请求。

下面是用于将传入的 Web 操作请求代理到框架模型服务的 Python 脚本。

```
from maxfw.core import MAXApp
from api import ModelPredictAPI
from config import API_TITLE, API_DESC, API_VERSION
import json
import base64
from flask import Flask, request, Response

max_app = MAXApp(API_TITLE, API_DESC, API_VERSION)
max_app.add_api(ModelPredictAPI, '/predict')

# Use flask test client to simulate HTTP requests for the prediction APIs
# HTTP request data will come from action invocation parameters, neat huh? :) test_client = max_app.app.test_client()
app = Flask(__name__)

# This implements the Docker runtime API used by Apache OpenWhisk
# https://github.com/apache/incubator-openwhisk/blob/master/docs/actions-docker.md
# /init is a no-op as everything is provided in the image. @app.route("/init", methods=['POST'])
def init():
    return ''

# Action invocation requests will be received as the `value` parameter in request body.
# Web Actions provide HTTP request parameters as `__ow_headers` & `__ow_body` parameters. @app.route("/run", methods=['POST'])
def run():
    body = request.json
    form_body = body['value']['__ow_body']
    headers = body['value']['__ow_headers']

    # binary image content provided as base64 strings
    content = base64.b64decode(form_body)

    # send fake HTTP request to prediction API with invocation data
    r = test_client.post('/model/predict', data=content, headers=headers)
    r_headers = dict((x, y) for x, y in r.headers)

    # binary data must be encoded as base64 strings to return in JSON response
    is_image = r_headers['Content-Type'].startswith('image')
    r_data = base64.b64encode(r.data) if is_image else r.data
    body = r_data.decode("utf-8")

    response = {'headers': r_headers, 'status': r.status_code, 'body': body }
    print (r.status)
    return Response(json.dumps(response), status=200, mimetype='application/json')

app.run(host='0.0.0.0', port=8080) 
```

### 建筑成图像

由于 MAX 模型已经作为公共 Docker 映像存在，因此在构建定制运行时时，这些映像可以用作基础映像。这些基础映像处理添加模型文件以及将它们执行到映像中所需的所有依赖关系。

这是构建脚本用来创建定制模型映像的`Dockerfile`。`model`参数是指包含模型名称的构建参数。

```
ARG model
FROM codait/${model}:latest

ADD openwhisk.py .

EXPOSE 8080

CMD python openwhisk.py 
```

然后在下面的构建脚本中使用它来为模型创建一个定制的运行时映像。

```
#!/bin/bash

set -e -u

for model in $MODELS; do echo "Building $model runtime image"
  docker build -t $model --build-arg model=$model .
  echo "Pushing $model to Docker Hub"
  docker tag $model $USERNAME/$model
  docker push $USERNAME/$model
done 
```

一旦图像发布到 Docker Hub，就可以在创建新的 Web 动作时引用它(使用`—docker`参数)。😎

```
ibmcloud wsk action create <MODEL_IMAGE> --docker <DOCKERHUB_NAME>/<MODEL_IMAGE> --web true -m 512 
```

## 结论

IBM 的模型资产交换是机器学习模型的精选集合，可以部署到云上执行各种任务。所有型号都有一系列公开的 Docker 图片。模型图像自动公开用于分类的 HTTP APIs。

模型库中的文档解释了如何在本地运行它们，以及如何使用 Kubernetes 进行部署，但是如何在无服务器的云平台上使用呢？由于水平可扩展性和成本优势，无服务器平台正成为部署机器学习模型的流行选择。

通过查看模型图像的源代码，我发现了一种机制，可以连接到用于将模型文件导出为 HTTP APIs 的定制模型框架。这允许我编写一个简单的包装器脚本来代理对模型预测 API 的无服务器函数调用。API 响应将被序列化为 Web 动作响应格式。

将这个脚本构建到一个新的 Docker 映像中，使用现有的模型映像作为基础映像，创建了一个可以在平台上使用的新的运行时。从这个运行时映像创建的 Web 动作将自动公开与现有映像相同的 HTTP APIs！