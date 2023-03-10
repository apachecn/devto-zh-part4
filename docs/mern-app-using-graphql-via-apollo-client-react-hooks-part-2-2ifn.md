# MERN 应用程序通过阿波罗客户端使用 GraphQL，(反应钩)。(第二部分)

> 原文：<https://dev.to/vinodchauhan7/mern-app-using-graphql-via-apollo-client-react-hooks-part-2-2ifn>

今天我们将开始使用 graphql 创建 mern 应用程序系列文章的第二部分。在第 1 部分中，我们已经在从查询中提取数据方面取得了进展，现在我们将继续这方面的工作。

[GitHub 链接:MERN-app-using-graph QL-Apollo-client](https://github.com/vinodchauhan7/MERN-app-using-graphql-apollo-client/tree/master)

[![vinodchauhan7 image](img/be17ce86844e07d6d48266e54b3a2d51.png)](/vinodchauhan7) [## MERN 应用程序通过阿波罗客户端使用 GraphQL，(反应钩)。(第一部分)

### vinodchauhan 7 Sep 10 ' 198 分钟读取

#react #graphql #apolloclient #mern](/vinodchauhan7/mern-app-using-graphql-via-apollo-client-react-hooks-part-1-17p9)

现在我们首先定义汽车和所有者之间的关系。以及如何获取细节。

### 一)类型关系

1)首先，我们为每辆汽车赋予“ownerId”。
2)在“汽车类型”中定义汽车与唯一所有者的关系。
3)用车主类型中的 ownerId 定义每个车主的汽车列表。
4)在 rootQuery 中查询汽车和车主。

```
 //Schema.js
const graphql = require("graphql"); //use graphql package

const _ = require("lodash");

/*Getting GraphQLObjectType function from 'graphql' to define the (dataType) 
 structure of our queries and their model type.
*/
const {
  GraphQLObjectType,
  GraphQLID,
  GraphQLString,
  GraphQLInt,
  GraphQLSchema,
  GraphQLList
} = graphql;

const CarsArray = [
  { id: "1", name: "S-Class", model: "2019", company: "Mercedes" ,ownerId : "1"},
  { id: "2", name: "Continental GT", model: "2019", company: "Bentley",ownerId : "2" },
  { id: "3", name: "Phantom", model: "2019", company: "Rolls-Royce",ownerId : "1" },
  { id: "4", name: "Panamera", model: "2019", company: "Porsche" ,ownerId : "2"},
  { id: "5", name: "A8", model: "2019", company: "Audi" ,ownerId : "1"},
  { id: "6", name: "I-Pace", model: "2019", company: "Jaguar",ownerId : "3"}
];

var OwnersArray = [
  { id: "1", name: "Vinod Chauhan", age: 27, gender: "male" },
  { id: "2", name: "John Dow", age: 46, gender: "male" },
  { id: "3", name: "Kristen", age: 30, gender: "female" },
  { id: "4", name: "Paris", age: 44, gender: "female" },
  { id: "5", name: "Sylvestor", age: 26, gender: "male" }
];

//Defining CarType with its fields.
const CarType = new GraphQLObjectType({
  name: "Car",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    model: { type: GraphQLInt },
    company: { type: GraphQLString },
    owner : { //Supporting pwner query in carType
        type: OwnerType,
        resolve(parent,args){
           return _.find(OwnersArray,{id:parent.ownerId});
        }
    }//owner 
  })
});

//Defining CarType with its fields.
const OwnerType = new GraphQLObjectType({
  name: "Owner",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt },
    gender: { type: GraphQLString },
    cars : {  // Supporting list of cars query in Owner type
        type : new GraphQLList(CarType),
        resolve(parent,args){
            return _.filter(CarsArray,{ownerId : parent.id});
        }
    }
  })
});

//Defining RootQuery
const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  fields: {
    // Fields here will be the query for frontends
    //We are defining a 'car' query which can take (car ID ) to search in DB.
    car: {
      type: CarType, //Defining model for car Query
      args: { id: { type: GraphQLID } }, //args field to extract
      // argument came with car query, e.g : Id of the car object to extract its details.
      resolve(parent, args) {
        //code to get value  from DB
        /**
         * With the help of lodash library(_), we are trying to find car with id from 'CarsArray'
         * and returning its required data to calling tool.
         */
        return _.find(CarsArray, { id: args.id });
      } //resolve function
    }, //car query ends here
    owner: {
      type: OwnerType,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        return _.find(OwnersArray, { id: args.id });
      }
    },//owners ends here
    cars : {
        type : new GraphQLList(CarType),
        resolve(parent,args){
            return CarsArray;
        }
    },//cars query
    owners : {
        type : new GraphQLList(OwnerType),
        resolve(parent,args){
            return OwnersArray;
        }
    }
  } //fields end here
});

//exporting 'GraphQLSchema with RootQuery' for GraphqlHTTP middleware.
module.exports = new GraphQLSchema({
  query: RootQuery
}); 
```

你可以看到这样的结果。

[![Alt Text](img/a274698dea64e28a14a7aa0dcfc065cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i2dzIqkG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/16opcpj0n03yj83qkc5c.png)

[![Alt Text](img/6df869c3381bac92bdc67a3491d29f5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rxlu8Mpd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39gtq5w73h38sm3133tt.png)

#### 希望到目前为止你们都已经了解了 graphql，现在我们将连接 mongoDB(数据库)。

> 对于数据库，我们将使用在线 mongoDB 服务器 mlab[www.mlab.com](http://www.mlab.com)。那些不知道什么是 mlab 和如何创建数据库&的凭据，那么请寻找任何 youtube 视频你会得到你所有的答案。

### b)数据库连接

为了连接 mlab 服务器(mongoDB ),我们将使用 mongoose 包。

```
//In app.js
//Get the express library from node_modules which we have just downloaded.
const express = require("express");

const graphqlHTTP = require("express-graphql");

const mongoose = require("mongoose"); //using mongoose to connect with db

//Imports
const schema = require("./schema/schema");

//Making const of express() into a variable (JS function first class Object).
const app = express();

//Please change mongoDB connection as maybe I have deleted this db on mlab when you are using it.
mongoose.connect(
  "mongodb://test:test123@ds145434.mlab.com:45434/gql-practice",
  { useNewUrlParser: true },
  () => {
    console.log("Connect with DB successfully.");
  }
);

/*We can use graphql on express server with middlewares, so that whenever
    we need graphql query from frontend, our express server can handle it
    smoothly.
*/
app.use(
  "/graphql",
  graphqlHTTP({
    schema: schema,
    graphiql: true
  })
);

//When our application starts, it will listen on port 4000
app.listen(4000, () => {
  console.log("Server is listening on port 4000");
}); 
```

#### 端子上的输出

```
//Output on console
[nodemon] restarting due to changes...
[nodemon] starting `node .\app.js`
Server is listening on port 4000
Connect with DB successfully 
```

### C)为 mongoDB 创建模型。

这里我们需要创建 mongoDB 能够理解的 carType 和 OwnerType 模型，这使我们能够在其中保存我们的记录。
1)在“服务器”文件夹中创建“模型”文件夹。
2)创建“汽车”模型文件，以存储不同的汽车详细信息。
3)创建文件“所有者”模型来存储不同的所有者详细信息。

```
 //car.js
const mongoose = require("mongoose");

const CarSchema = new mongoose.Schema({
  name: String,
  model: Number,
  company: String,
  ownerId: String
});

module.exports = mongoose.model("cars", CarSchema);

//---------------------------------------------------------

//owner.js
const mongoose = require("mongoose");

const ownerSchema = new mongoose.Schema({
  name: String,
  age: Number,
  gender: String
});

module.exports = mongoose.model("owners", ownerSchema); 
```

### d)了解 GraphQL 中的 CRUD 突变’。

要在 graphql 中实现 crud 实现，我们需要了解它的变化。
Mutation 是 GraphQLObjectType()的一个对象类型，我们可以在其中添加不同的操作，比如 addCar、addOwner &等等。

```
 //schema.js

const graphql = require("graphql"); //use graphql package

const _ = require("lodash");

const cars = require("../models/car");
const owners = require("../models/owner");

/*Getting GraphQLObjectType function from 'graphql' to define the (dataType) 
 structure of our queries and their model type.
*/
const {
  GraphQLObjectType,
  GraphQLID,
  GraphQLString,
  GraphQLInt,
  GraphQLSchema,
  GraphQLList
} = graphql;

//Defining CarType with its fields.
const CarType = new GraphQLObjectType({
  name: "Car",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    model: { type: GraphQLInt },
    company: { type: GraphQLString },
    owner: {
      type: OwnerType,
      resolve(parent, args) {
        // return _.find(OwnersArray, { id: parent.ownerId });
      }
    } //owner
  })
});

//Defining CarType with its fields.
const OwnerType = new GraphQLObjectType({
  name: "Owner",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt },
    gender: { type: GraphQLString },
    cars: {
      type: new GraphQLList(CarType),
      resolve(parent, args) {
        //return _.filter(CarsArray, { ownerId: parent.id });
      }
    }
  })
});

//Defining RootQuery
const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  fields: {
    // Fields here will be the query for frontends
    //We are defining a 'car' query which can take (car ID ) to search in DB.
    car: {
      type: CarType, //Defining model for car Query
      args: { id: { type: GraphQLID } }, //args field to extract
      // argument came with car query, e.g : Id of the car object to extract its details.
      resolve(parent, args) {
        //code to get value  from DB
        /**
         * With the help of lodash library(_), we are trying to find car with id from 'CarsArray'
         * and returning its required data to calling tool.
         */
        //return _.find(CarsArray, { id: args.id });
      } //resolve function
    }, //car query ends here
    owner: {
      type: OwnerType,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        // return _.find(OwnersArray, { id: args.id });
      }
    }, //owners ends here
    cars: {
      type: new GraphQLList(CarType),
      resolve(parent, args) {
        //return CarsArray;
      }
    }, //cars query
    owners: {
      type: new GraphQLList(OwnerType),
      resolve(parent, args) {
        //return OwnersArray;
      }
    }
  } //fields end here
});

const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    addOwner: {    // To add Owner in DB
      type: OwnerType,
      args: {
        name: { type: GraphQLString },
        age: { type: GraphQLInt },
        gender: { type: GraphQLString }
      },
      resolve(parent, args) {
        let owner = new owners({
          name: args.name,
          age: args.age,
          gender: args.gender
        });
        return owner.save(); //create owner data in mlab
      }
    }
  } //fields ends here
});

//exporting 'GraphQLSchema with RootQuery' for GraphqlHTTP middleware.
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation: Mutation
}); 
```

在上面的截图中，我们完成了以下步骤来连接我们的 graphQL-Express 服务器和 mongoDb :
1)在其中导入汽车&车主模型。
2)删除虚拟数据。
3)注释掉所有退货，因为我们现在将从 db 获取数据。
4)创建一个新的对象 mutations(意思是对某物进行突变)来实现我们的 crud 逻辑。
5)在 GraphQLSchema 中添加突变对象的条目。

打开 localhost:4000/graphql，在这里你会在右边新增一个突变条目。

[![Alt Text](img/ab3664c4ff00e8d372827fb648d594f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_lhjDMgo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lzerliago7oxda7v4p3b.png)

[![Alt Text](img/f20dd7c386c6b8f299ab97e7c7e2f0f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VP4q_J5r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/254rnns643g83uegoo91.png)

### 同样，在“addOwner”突变后添加“addCar”的步骤同上。

```
 addCar: {
      type: CarType,
      args: {
        name: { type: GraphQLString },
        model: { type: GraphQLInt },
        company: { type: GraphQLString },
        ownerId: { type: GraphQLID }
      },
      resolve(parent, args) {
        let car = new cars({
          name: args.name,
          model: args.model,
          company: args.company,
          ownerId: args.ownerId
        });

        return car.save();
      }
    }//addCar 
```

[![Alt Text](img/3a06fac0b7b595b6169605e4f4d1383d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C9EEWDwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n4sz76yxwtgengiesbxq.png)

### e)更新所有查询的解析功能

```
const graphql = require("graphql"); //use graphql package

const _ = require("lodash");

const cars = require("../models/car");
const owners = require("../models/owner");

/*Getting GraphQLObjectType function from 'graphql' to define the (dataType) 
 structure of our queries and their model type.
*/
const {
  GraphQLObjectType,
  GraphQLID,
  GraphQLString,
  GraphQLInt,
  GraphQLSchema,
  GraphQLList
} = graphql;

//Defining CarType with its fields.
const CarType = new GraphQLObjectType({
  name: "Car",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    model: { type: GraphQLInt },
    company: { type: GraphQLString },
    owner: {
      type: OwnerType,
      resolve(parent, args) {
        return owners.findById(parent.ownerId);
      }
    } //owner
  })
});

//Defining CarType with its fields.
const OwnerType = new GraphQLObjectType({
  name: "Owner",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt },
    gender: { type: GraphQLString },
    cars: {
      type: new GraphQLList(CarType),
      resolve(parent, args) {
        return cars.find({ ownerId: parent.id });
      }
    }
  })
});

//Defining RootQuery
const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  fields: {
    // Fields here will be the query for frontends
    //We are defining a 'car' query which can take (car ID ) to search in DB.
    car: {
      type: CarType, //Defining model for car Query
      args: { id: { type: GraphQLID } }, //args field to extract
      // argument came with car query, e.g : Id of the car object to extract its details.
      resolve(parent, args) {
        //code to get value  from DB
        /**
         * With the help of lodash library(_), we are trying to find car with id from 'CarsArray'
         * and returning its required data to calling tool.
         */
        return cars.findById(args.id);
      } //resolve function
    }, //car query ends here
    owner: {
      type: OwnerType,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        return owners.findById(args.id);
      }
    }, //owners ends here
    cars: {
      type: new GraphQLList(CarType),
      resolve(parent, args) {
        return cars.find({});
      }
    }, //cars query
    owners: {
      type: new GraphQLList(OwnerType),
      resolve(parent, args) {
        return owners.find({});
      }
    }
  } //fields end here
});

const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    addOwner: {
      type: OwnerType,
      args: {
        name: { type: GraphQLString },
        age: { type: GraphQLInt },
        gender: { type: GraphQLString }
      },
      resolve(parent, args) {
        let owner = new owners({
          name: args.name,
          age: args.age,
          gender: args.gender
        });
        return owner.save();
      }
    }, //AddOwner ends here
    addCar: {
      type: CarType,
      args: {
        name: { type: GraphQLString },
        model: { type: GraphQLInt },
        company: { type: GraphQLString },
        ownerId: { type: GraphQLID }
      },
      resolve(parent, args) {
        let car = new cars({
          name: args.name,
          model: args.model,
          company: args.company,
          ownerId: args.ownerId
        });

        return car.save();
      }
    } //addCar
  } //fields ends here
});

//exporting 'GraphQLSchema with RootQuery' for GraphqlHTTP middleware.
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation: Mutation
}); 
```

打开 localhost:4000/graphql，在上面进行查询。

[![Alt Text](img/c5494d455330e314947a918d1cfba081.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xd7yJGdT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/txqi7h9xwbpl5oynb967.png)

# 代码结构

[![Alt Text](img/e131bfbe31fa829b8389d72991ead70e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5K-UsUz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ejur55wo2scdfnuxfi12.png)

> 恭喜你！！我们刚刚用 mongoDB 制作了我们的 express-graphql 服务器。在下一部分中，我们将创建前端客户端(react + apollo-client)来处理这些查询。到那时再见，伙计们，玩得开心。