# 将 angularJs1.4 代码转换为 ionic 4 angular 7

> 原文：<https://dev.to/selvamp/convert-angularjs1-4-code-to-ionic-4-angular-7-5hno>

Hi
手机 app 开发 Angularjs 1.4。该插件还开发了 Angularjs 1.4。angularjs 应用程序中使用的代码的插件部分。现在我新开发了 ionic 4 & angular 7 代码。我想把插件部分代码转换成 angular 7。代码如下:这是访问服务器的定制插件。以下是完整代码:

if(plugin name = = ' getDeviceModelAndOSVersion '){
var vectorAppRootScope = angular . element(document . query selector('[app-root]'))。范围()。$ root
browser . execute script({ code:success function name+'(" '+vectorapprootscope . this . device . model+' "，" '+vectorapprootscope . this . device . version+' ")；"
})；
}

请帮帮忙