# MongoDB Golang 驱动教程

> 原文：<https://dev.to/eduardohitek/mongodb-golang-driver-tutorial-49e5>

在多年依赖像 mgo 和 T2 这样的社区驱动程序之后，去年 MongoDB T4 宣布他们正在构建自己的解决方案。去年三月他们发布了 1.0.0 版本，所以让我们看看如何使用官方驱动程序进行一些正常的操作。

首先，你需要使用 go get 下载驱动。

```
go.mongodb.org/mongo-driver/mongo 
```

Enter fullscreen mode Exit fullscreen mode

假设您的 MongoDB 安装使用默认设置，您的方法应该是这样的:

```
package main

import (
    "context"
    "log"

    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
    "go.mongodb.org/mongo-driver/mongo/readpref"
)

func GetClient() *mongo.Client {
    clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")
    client, err := mongo.NewClient(clientOptions)
    if err != nil {
        log.Fatal(err)
    }
    err = client.Connect(context.Background())
    if err != nil {
        log.Fatal(err)
    }
    return client
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试与 mongo 的连接，我们可以调用一个名为 Ping 的函数，并检查是否返回任何错误。否则，我们的连接是成功的。

```
func main() {
    c := GetClient()
    err := c.Ping(context.Background(), readpref.Primary())
    if err != nil {
        log.Fatal("Couldn't connect to the database", err)
    } else {
        log.Println("Connected!")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于下一个示例，我创建了一个名为`civilact`的数据库和一个集合`heroes`，并添加了以下文档:

```
{ 
    "_id" : ObjectId("5d0574824d9f7ff15e989171"), 
    "name" : "Tony Stark", 
    "alias" : "Iron Man", 
    "signed" : true
}
{ 
    "_id" : ObjectId("5d0574d74d9f7ff15e989172"), 
    "name" : "Steve Rodgers", 
    "alias" : "Captain America", 
    "signed" : false
}
{ 
    "_id" : ObjectId("5d0574e94d9f7ff15e989173"), 
    "name" : "Vision", 
    "alias" : "Vision", 
    "signed" : true
}
{ 
    "_id" : ObjectId("5d0575344d9f7ff15e989174"), 
    "name" : "Clint Barton", 
    "alias" : "Hawkeye", 
    "signed" : false
} 
```

Enter fullscreen mode Exit fullscreen mode

为了处理这些文档，如果我们创建一个表示所有字段及其 json 名称的结构会更好。

```
type Hero struct {
    Name   string `json:"name"`
    Alias  string `json:"alias"`
    Signed bool   `json:"signed"`
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个方法，它将返回所有期望 2 个参数的英雄:MongoDB 客户端和 bson。表示筛选器的 m。如果该过滤器为空，该方法将返回所有文档。

```
import (
    "context"
    "log"

    "go.mongodb.org/mongo-driver/bson"
    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
    "go.mongodb.org/mongo-driver/mongo/readpref"
)

func ReturnAllHeroes(client *mongo.Client, filter bson.M) []*Hero {
    var heroes []*Hero
    collection := client.Database("civilact").Collection("heroes")
    cur, err := collection.Find(context.TODO(), filter)
    if err != nil {
        log.Fatal("Error on Finding all the documents", err)
    }
    for cur.Next(context.TODO()) {
        var hero Hero
        err = cur.Decode(&hero)
        if err != nil {
            log.Fatal("Error on Decoding the document", err)
        }
        heroes = append(heroes, &hero)
    }
    return heroes
} 
```

Enter fullscreen mode Exit fullscreen mode

细分如下:

1.  创建一个代表数据库中集合的`collection`；
2.  要求`collection`返回基于过滤器的元素的光标(在这种情况下，过滤器为空，因此将返回所有元素)；
3.  迭代这个光标并将每个文档解码为一个英雄类型；
4.  将解码的英雄追加到`heroes`数组中。

如果我们在 main 函数内部运行，我们的返回将是:

```
heroes := ReturnAllHeroes(c, bson.M{})
for _, hero := range heroes {
    log.Println(hero.Name, hero.Alias, hero.Signed)
}

2019/06/15 21:07:00 Tony Stark Iron Man true
2019/06/15 21:07:00 Steve Rodgers Captain America false
2019/06/15 21:07:00 Vision Vision true
2019/06/15 21:07:00 Clint Barton Hawkeye false 
```

Enter fullscreen mode Exit fullscreen mode

要检索签署了[索科维亚协议](https://marvelcinematicuniverse.fandom.com/wiki/Sokovia_Accords)的英雄，我们只需要改变过滤器。

```
heroes := ReturnAllHeroes(c, bson.M{"signed": true})

2019/06/15 21:18:04 Tony Stark Iron Man true
2019/06/15 21:18:04 Vision Vision true 
```

Enter fullscreen mode Exit fullscreen mode

只检索一个英雄，我们的新方法如下:

```
func ReturnOneHero(client *mongo.Client, filter bson.M) Hero {
    var hero Hero
    collection := client.Database("civilact").Collection("heroes")
    documentReturned := collection.FindOne(context.TODO(), filter)
    documentReturned.Decode(&hero)
    return hero
} 
```

Enter fullscreen mode Exit fullscreen mode

电话将会是:

```
 hero := ReturnOneHero(c, bson.M{"name": "Vision"})
    log.Println(hero.Name, hero.Alias, hero.Signed)

    2019/06/15 22:55:44 Vision Vision true 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了增加我们收集的英雄和插入，例如奇异博士:新的方法将是这样的:

```
func InsertNewHero(client *mongo.Client, hero Hero) interface{} {
    collection := client.Database("civilact").Collection("heroes")
    insertResult, err := collection.InsertOne(context.TODO(), hero)
    if err != nil {
        log.Fatalln("Error on inserting new Hero", err)
    }
    return insertResult.InsertedID
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的方法将如何被我们之前的方法`ReturnOneHero`使用和检查:

```
hero = Hero{Name: "Stephen Strange", Alias: "Doctor Strange", Signed: true}
insertedID := InsertNewHero(c, hero)
log.Println(insertedID)
hero = ReturnOneHero(c, bson.M{"alias": "Doctor Strange"})
log.Println(hero.Name, hero.Alias, hero.Signed) 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们在我们的英雄收藏中增加了一个超级魔法师，但是如果他不喜欢并要求我们把他从收藏中移除呢？这就是为什么我们需要一个`RemoveOneHero`方法。

```
func RemoveOneHero(client *mongo.Client, filter bson.M) int64 {
    collection := client.Database("civilact").Collection("heroes")
    deleteResult, err := collection.DeleteOne(context.TODO(), filter)
    if err != nil {
        log.Fatal("Error on deleting one Hero", err)
    }
    return deleteResult.DeletedCount
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们如何检查:

```
heroesRemoved := RemoveOneHero(c, bson.M{"alias": "Doctor Strange"})
log.Println("Heroes removed count:", heroesRemove
hero = ReturnOneHero(c, bson.M{"alias": "Doctor Strange"})
log.Println("Is Hero empty?", hero == Hero{ }) 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们想象鹰眼改变了主意，现在想要签署协议。所以让我们制定德`UpdateHero`法。

```
func UpdateHero(client *mongo.Client, updatedData bson.M, filter bson.M) int64 {
    collection := client.Database("civilact").Collection("heroes")
    atualizacao := bson.D{ {Key: "$set", Value: updatedData} }
    updatedResult, err := collection.UpdateOne(context.TODO(), filter, atualizacao)
    if err != nil {
        log.Fatal("Error on updating one Hero", err)
    }
    return updatedResult.ModifiedCount
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！常规的 CRUD 操作被覆盖，我们的英雄可以决定他们的命运。
这些例子的所有代码都可以在[这里](http://github.com/eduardohitek/mongodb-go-example)找到，这个教程也发布在我的[博客](http://blog.eduardohitek.com)上。这是司机的官方[报告](https://github.com/mongodb/mongo-go-driver)和官方[文件](https://godoc.org/go.mongodb.org/mongo-driver/mongo)

如有任何问题、建议或我犯的错误，请随时联系我。