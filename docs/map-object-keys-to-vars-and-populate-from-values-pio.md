# 将对象键映射到变量并从值填充

> 原文：<https://dev.to/rebelclause/map-object-keys-to-vars-and-populate-from-values-pio>

[https://codepen.io/rebelclause/embed/GVQNOQ?height=600&default-tab=result&embed-version=2](https://codepen.io/rebelclause/embed/GVQNOQ?height=600&default-tab=result&embed-version=2)

模板字面来救援，做事情踢踏舞在邻里已经禁止！

对于 codepen，使用“global”代替“window”作为 Node，因为 Node 没有 window 对象，尽管我对 rendertron 等服务器端的无头渲染感到疑惑。也许这里有人对此有看法。

另外，考虑到左边显示的析构同时进行计算和赋值:

```
soapand = [
    {name: 'far',
    email: 'quad'},
    {name: 'car',
    email: 'squad'}
]

// let {name: `${'wingedAngel'}`, email} = soapand
// global[`wingedAngel`] = `${}`
refreshed = soapand.map((item, idx) => {
    // console.log(item)
    window[`wingedAngel`] = `${item.name}`
    mad = ['holyWater']
    window[`${mad[0]}`] = item.email
    // window['holyWater'] = item.email
    return wingedAngel + ' ' + holyWater
})

// window[`wingedAngel`] = `${soapand[0].name}`
// window['holyWater'] = soapand.email
console.log(refreshed)
console.log(wingedAngel)
console.log(holyWater) 
```

我很高兴在 MDN 文档中找到了一行模糊的引用。我希望这一点电影魔术能在某种程度上帮助你。