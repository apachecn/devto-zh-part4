# request.files['file']正在返回字符串，而不是文件存储对象

> 原文：<https://dev.to/rahultappetla/request-files-file-is-returning-string-instead-of-filestorage-obj-558p>

在本地运行时，request.files['file']返回文件存储对象，但当使用无服务器部署在 AWS 上时，它返回。txt 文件。这导致了错误“字符串对象没有属性保存”或“字符串对象没有属性文件名”当我试图做 f.save(f.filename)。请帮帮忙。

**PS:忽略下面代码中的缩进。我把它分成几部分复制粘贴。**

def create _ AWS _ event(flask _ request:request，path_data: Dict，resource: str) -> Dict:
#构建我们的路径数据
path _ parameters = { }
for p in path _ data:
path _ parameters[p]= path _ data[p]
#构建我们的事件查询字符串
query _ string _ parameters = { }
# no inspection PyUnresolvedReferences
for q in flask _ request . args:
# no

return {
"resource": resource，
"path": flask_request.path，
" http method ":flask _ request . method，
"headers": {}，
"multiValueHeaders": {}，
" query string parameters ":query _ string _ parameters，
" multivaluequerystring parameters ":{ }，
" path parameters ":path _ parameters，
"stageVariables": {}，
"requestContext "

@app.route('/uploader '，methods=['GET '，' POST '])
def upload _ file():
EVENT = create _ AWS _ EVENT(flask _ request = request，path_data={}，resource = '/uploader ')
print(' EVENT:'，EVENT)
print(EVENT[' body '])
result = AWS _ doc _ uploader . lambda _ handler(EVENT = EVENT，context=Context)
返回响应(result['body']，status = result[' status

**在 lambda_handler :**

```
elif resource == '/uploader' and method == 'POST':

    test = event['body']
    print("FILE: ", test)
    test.save(test.filename) 
```