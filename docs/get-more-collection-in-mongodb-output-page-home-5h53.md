# 在 mongodb 输出页面首页获取更多收藏？

> 原文：<https://dev.to/nguyennobita9999/get-more-collection-in-mongodb-output-page-home-5h53>

函数索引(请求，结果，下一个){

数据库集合(“产品”)。查找({})。toArray(函数(错误，产品){

db.collection(“横幅”)。查找({})。toArray(function(err，banner){

db.collection(“最新”)。查找({})。toArray(函数(错误，最新){

```
 return res.render("/index" , {product : product ,banner : banner , newest : newest}); 
}) 
```

})
})

}

请帮帮我？