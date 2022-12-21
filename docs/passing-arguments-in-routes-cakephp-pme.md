# 在路由中传递参数(CakePHP)

> 原文：<https://dev.to/vjnvisakh/passing-arguments-in-routes-cakephp-pme>

**问题:**如何在 CakePHP 中将参数传递给 url

**解决方案:**

在 **app/Config/routes.phg**

Router::connect('/:arg '，array('controller' => 'homes '，' action' => 'index '，' pass ' = >[' arg '])；

内**家/指数()**

index()
{
...
$ passedOne = $ this->request->params[" arg "]；
...

}

希望对某人有用。:D