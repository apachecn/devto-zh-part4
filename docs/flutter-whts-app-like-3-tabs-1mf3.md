# 颤振-Whts-应用程序喜欢 3 个标签。

> 原文：<https://dev.to/sidkhore1002/flutter-whts-app-like-3-tabs-1mf3>

导入‘package:flutter/material . dart’；

void main()= > runApp(new MyApp())；

类 MyApp 扩展 StatelessWidget {
@ override
Widget build(build context context){
return new material app(
title:' Flutter Demo '，
theme:new theme data(
primary watch:colors . blue，
)，
home: new MyHomePage()，
)；
}
}

类 MyHomePage 扩展 StatefulWidget {

@ override
_ myhomepagestate createstate()=>new _ myhomepagestate()；

}

class _MyHomePageState 使用 SingleTickerProviderStateMixin {

TabController _ tabController
void initState(){
_ tab controller = new tab controller(长度:3，vsync:this)；
super . initstate()；
}

@ override
Widget build(build context context){
返回新的脚手架(

```
 appBar: new AppBar(
      title: new Text("WhatsApp", style: TextStyle(color: Colors.white, fontSize: 22.0, fontWeight: FontWeight.w600),),
      actions: <Widget>[

        Padding(
          padding: const EdgeInsets.only(right: 20.0),
          child: Icon(Icons.search),

        ),
        Padding(
          padding: const EdgeInsets.only(right: 16.0),
          child: Icon(Icons.more_vert),
        ),
      ],

  bottom: TabBar(
  tabs: [
    new Tab(icon: new Icon(Icons.chat)),
    new Tab(
      icon: new Icon(Icons.photo_library),
    ),
    new Tab(
      icon: new Icon(Icons.call),
    )
  ], indicatorColor: Colors.white,
  controller: _tabController, 
  ),
  ),

  body: TabBarView(      
  children: [
    Text("Chat Screen", textAlign: TextAlign.center,style: TextStyle( fontSize: 22.0, fontWeight: FontWeight.w600)),
    Text("Status Screen", textAlign: TextAlign.center,style: TextStyle( fontSize: 22.0, fontWeight: FontWeight.w600)),
    Text("Call Screen", textAlign: TextAlign.center,style: TextStyle( fontSize: 22.0, fontWeight: FontWeight.w600)),
  ],
  controller: _tabController,       

  ),

); 
```

}
}