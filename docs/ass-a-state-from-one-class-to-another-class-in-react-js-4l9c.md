# 在 REACT JS 中将状态从一个类传递到另一个类。

> 原文：<https://dev.to/cristiancuartas/ass-a-state-from-one-class-to-another-class-in-react-js-4l9c>

你好，我需要将一个状态从一个类传递到另一个类。

用回拨可以吗？

你帮我？

例子:
类 SearchNav 扩展组件{
构造函数(props) {
超级(props)；
this.state = {
q:' '，
param: {}，
get data:[]
}；
}

getdata search =()= > {
fetch(URL+`${this.state.q}`)
。然后(res = > res.json())
。然后(data =>{
this . setstate({ get data:data . hits })；
console . log(this . state . get data)；
})
。catch(Error =>{
console . log(Error)；
})；
}；
...
我需要将 state = > 'getData '传递给其他类。