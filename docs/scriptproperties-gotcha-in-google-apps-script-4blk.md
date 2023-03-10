# Google 企业应用套件脚本中的脚本属性

> 原文：<https://dev.to/bugmagnet/scriptproperties-gotcha-in-google-apps-script-4blk>

出于疯狂的原因，我用`get`、`set`、`forget`和`getKeys`的方法将`PropertiesService`的`ScriptProperties`包装在一个对象中，即:

```
function ScptProps() {
  this.scriptProperties = PropertiesService.getScriptProperties();
}

ScptProps.prototype.get = function (name) {
  return this.scriptProperties.getProperty(name);
};

ScptProps.prototype.set = function (name, value) {
  return this.scriptProperties.setProperty(name, value);
};

ScptProps.prototype.forget = function (name) {
  return this.scriptProperties.deleteProperty(name);
};

ScptProps.prototype.getKeys = function () {
  return this.scriptProperties.getKeys();
}; 
```

使用我以前发布的 REPL，我发出了以下命令:

```
(new ScptProps).set('goose',58);
typeof (new ScptProps).get('goose');
(new ScptProps).forget('goose'); 
```

鹅是我和 58 我的年龄感兴趣的人。

抓住你了吗？嗯，我最近有点吃惊，在调试一个数字到数字的比较问题时，发现当我存储一个数字时，我没有得到一个回来。我得到一个字符串，必须对它做一个`parseInt()`来得到它的初始值。`typeof (new ScptProps).get('goose');`的结果是，你猜对了，`string`！