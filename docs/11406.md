# 我的代码有问题(我刚刚开始)

> 原文：<https://dev.to/juanfe/problem-whit-my-code-im-just-starting-pd4>

Visual Studio 说我的代码有问题，我刚开始使用 Javascript，我不知道问题出在哪里。
代码:

DOCTYPE html
html
head
meta charset = " utf-8 "
title Functions/title
<meta name = " Functions " content = " Functions with days "
/head
body
p id = " output "/p
img id = " face "
Script type = " text/JavaScript "
function esFinDeSemana(){
const día = new Date()。getDay()；
if (dia 0 || dia === 6){
返回 true
}
else{
返回 false
}
让 ValorSrc
if(esFinDeSemana()){
ValorSrc = ' caritafeliz . png '
else {
ValorSrc CaritaTriste.png
}
document . getelementbyid(' face ')。src = ValorSrc
/脚本
/正文
/html