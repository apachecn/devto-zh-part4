# 有人能告诉我为什么我的数组不能显示在我的主页上吗？

> 原文：<https://dev.to/cardorelngassak/somebody-can-tell-me-why-my-array-can-not-display-in-my-page-home-js-please-do4>

从“React”导入 React；
从'导入主页。/Home '；

函数 Li (props){
返回 li {props.value} /li
}

const List = props = > {
const numbers = props . numbers；
const listItem = numbers . map((number)=>
//Li value = { number } key = { number }/
)
return(
//ul { listItem }/ul
)
}

导出默认列表；

//* * * * * * * * * * * * * * *
从“React”导入 React；
从'导入列表。/list '；

function Home () {
常数 numbers = [1，2，3，4，5]；
归来(

# 主页

)
}

导出默认主页；