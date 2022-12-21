# 两类函数及其区别

> 原文：<https://dev.to/samairali/two-type-of-functions-and-there-difference-h0g>

我有一个场景，我正在提交表单数据并提醒数据，我正在处理 react js，所以事情正在提交，代码是
on submit = { this . handle submit }
当我像这样声明函数时，我不明白的问题出现了

handle submit(e){
e . prevent default()
alert(`${this.state.inpUname} ${this.state.inpComment} ${this.state.profession}`)
}

它给出了错误，但是当我像那样声明函数时，一切都没问题

handle submit =(e)= > {
e . prevent default()
alert(`${this.state.inpUname} ${this.state.inpComment} ${this.state.profession}`)
}

我想知道区别