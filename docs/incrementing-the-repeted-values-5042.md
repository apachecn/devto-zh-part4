# 递增重复值

> 原文：<https://dev.to/hillal20/incrementing-the-repeted-values-5042>

我的代码

设 arr = [1，2，5，4，2，5，4]；

const fun =()= > {
let newArr =[]；

让 El =[]；
设结果= 0
设 I = 0；

while(arr . length > 0){
El = arr . splice(I，1)；
El = El[0]；
控制台. log(el)

如果(！new arr . includes(El)){
new arr . push(El)；
}
else if(new arr . includes(El)){
while(new arr . includes(El)){
++ El；
}
new arr . push(El)；
}
}

result = newArr.reduce((el，ACC)= > {
return El+ACC
})；
返回结果；
}
好玩(arr)