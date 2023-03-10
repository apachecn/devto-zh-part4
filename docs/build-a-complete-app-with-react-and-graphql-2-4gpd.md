# 使用 React 和 GraphQL-2 构建完整的应用程序

> 原文：<https://dev.to/nabendu82/build-a-complete-app-with-react-and-graphql-2-4gpd>

欢迎来到本系列的第 2 部分。这个系列的灵感来自于免费代码营的 youtube 教程。

我们将首先向 **schema.js** 添加另一个类型作者

该过程几乎类似于书籍类型的过程。

```
 const graphql = require('graphql');

    const { GraphQLObjectType, GraphQLString, GraphQLSchema, GraphQLID, GraphQLInt } = graphql;

    // Dummy data
    var books = [
        { name: 'Name of the Wind', genre: 'Fantasy', id: '1' },
        { name: 'Two States', genre: 'Drama', id: '2' },
        { name: 'The Long Earth', genre: 'Sci-Fi', id: '3' },
    ];

    var authors = [
        { name: 'Patrick Rothfuss', age: 44, id: '1' },
        { name: 'Chetan Bhagat', age: 45, id: '2' },
        { name: 'Terry Pratchett', age: 66, id: '3' }
    ];

    const BookType = new GraphQLObjectType({
        name: 'Book',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            genre: { type: GraphQLString }
        })
    });

    const AuthorType = new GraphQLObjectType({
        name: 'Author',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            age: { type: GraphQLInt }
        })
    });

    const RootQuery = new GraphQLObjectType({
        name: 'RootQueryType',
        fields: {
            book: {
                type: BookType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    return books.find(item => item.id === args.id);
                }
            },
            author: {
                type: AuthorType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    return authors.find(item => item.id === args.id);
                }
            }
        }
    });

    module.exports = new GraphQLSchema({
        query: RootQuery
    }); 
```

有一点要注意，我们已经把 id 设为 **GraphQLID** 。与 **GraphQLString** 不同的是，我们还可以在 **id** 中传递一个数字。

[![number in id](img/ca53cf86f2f2d99a9bf00aac3639bc29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MCFktD38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AtC1Dx2POTbJBHH1tjU4w_A.png)*id 中的数字*

通常的字符串也可以。

[![id is string](img/88e60854e58e0ff226cd690595a58494.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Tca3pwyY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A08lohVtrwwhVh8GIv0SoRg.png) * id 是字符串*

接下来，我们将建立书和作者类型之间的关系。每本书都有一个作者，所以在 *book 数组*中我们添加了一个关键字 **authorId** ，为每个对象对应 *authors 数组*中的 Id。

此外，在 BookType 中，我们添加了一个查询，通过使用父代的 authorId 来获取作者。

```
 …
    …
    var books = [
        { name: 'Name of the Wind', genre: 'Fantasy', id: '1', authorId: '1' },
        { name: 'Two States', genre: 'Drama', id: '2', authorId: '2' },
        { name: 'The Long Earth', genre: 'Sci-Fi', id: '3', authorId: '3' },
    ];

    var authors = [
        { name: 'Patrick Rothfuss', age: 44, id: '1' },
        { name: 'Chetan Bhagat', age: 45, id: '2' },
        { name: 'Terry Pratchett', age: 66, id: '3' }
    ];

    const BookType = new GraphQLObjectType({
        name: 'Book',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            genre: { type: GraphQLString },
            author: {
                type: AuthorType,
                resolve(parent, args){
                    return authors.find(item => item.id === parent.authorId);
                }
            }
        })
    });

    …
    … 
```

现在，我们可以在 graphiql 中查询并获得一本书的作者详细信息。

[![author details also present](img/eaf0bba4128a452c9dc323262b211900.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--jZrcaRwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ADpSW0vVKEn4J_y_ySYLf3w.png) *作者详情还呈现*

现在，我们将做相反的事情。每个作者都有书，事实上每个作者可以有很多书。因此，我们有了一个新的变量类型，叫做 **GraphQLList** 。

```
 const graphql = require('graphql');

    const { GraphQLObjectType, GraphQLString, GraphQLSchema, GraphQLID, GraphQLInt, GraphQLList } = graphql;

    // Dummy data
    var books = [
        { name: 'Name of the Wind', genre: 'Fantasy', id: '1', authorId: '1' },
        { name: 'Two States', genre: 'Drama', id: '2', authorId: '2' },
        { name: 'The Long Earth', genre: 'Sci-Fi', id: '3', authorId: '3' },
        { name: 'The Colour of Magic', genre: 'Fantasy', id: '4', authorId: '3' },
        { name: 'The Light Fantastic', genre: 'Fantasy', id: '5', authorId: '3' },
        { name: 'Five Point something', genre: 'Drama', id: '6', authorId: '2' },

    ];

    var authors = [
        { name: 'Patrick Rothfuss', age: 44, id: '1' },
        { name: 'Chetan Bhagat', age: 45, id: '2' },
        { name: 'Terry Pratchett', age: 66, id: '3' }
    ];

    const BookType = new GraphQLObjectType({
        name: 'Book',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            genre: { type: GraphQLString },
            author: {
                type: AuthorType,
                resolve(parent, args){
                    return authors.find(item => item.id === parent.authorId);
                }
            }
        })
    });

    const AuthorType = new GraphQLObjectType({
        name: 'Author',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            age: { type: GraphQLInt },
            books: {
                type: new GraphQLList(BookType),
                resolve(parent, args){
                    return books.filter(obj => obj.authorId === parent.id);
                }
            }
        })
    });
    ...
    ... 
```

现在，在运行我们的作者查询时，我们得到了他所有的书

[![author’s books](img/64996dc89a721b2bfdf427764442005c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--UwzLsaV---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2876/1%2ASObMcI7ibHl2D9on5CbHdw.png) *作者书籍*

现在，如果我们想要我们数据库中的所有书籍或所有作者。使用 GraphQL 非常简单，因为我们只需要用下面的 **schema.js** 中的 **GraphQLList** 更新我们的 RootQuery。

```
 …
    …
    const RootQuery = new GraphQLObjectType({
        name: 'RootQueryType',
        fields: {
            book: {
                type: BookType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    return books.find(item => item.id === args.id);
                }
            },
            author: {
                type: AuthorType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    return authors.find(item => item.id === args.id);
                }
            },
            books: {
                type: new GraphQLList(BookType),
                resolve(parent, args){
                    return books;
                }
            },
            authors: {
                type: new GraphQLList(AuthorType),
                resolve(parent, args){
                    return authors;
                }
            }
        }
    });

    module.exports = new GraphQLSchema({
        query: RootQuery
    }); 
```

现在，图书查询将返回所有图书。

[![The books query](img/1cf047e00e1f250225ada65ea8665cf2.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---WTDZvBw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A7-5DienpBRjUkSWOa_jKPg.png) *账簿查询*

也可以进行作者查询。我们也可以包括这本书。

[![authors query](img/06345d02e6abd760efd1df7a5c33094f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--mvqWo-QJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AwYpMxIm6v03GrD68YBIigw.png) *作者查询*

现在，是时候将我们所有的数据存储到 mongoDB 中了。因此，前往[https://mlab.com/](https://mlab.com/)登录。

然后创建新的 mongoDB 部署。

[![Create New mongoDB](img/76055920c4b7831197d8e3d3b39d6a92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fi-FtcbL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2Aoi9R6QqcSL7NnXO-jvqk9A.png)T3】创建新的 mongoDB

点击免费计划。

[![The free plan](img/9d9d377218ffd588dcc0f8a0220f3da7.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---ytj13ZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2Ay6-mN8LUw6nqrPQ44m_urw.png) *自由计划*

选择一个区域，然后点按“继续”。

[![Select a Region](img/bb0296e9e629c877ec8c6bc3eae73b36.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--FWYKzhlO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ALlY4sZu75UC0c-S3OonJBQ.png) *选择地区*

为您的数据库命名，然后单击继续。

[![Database name](img/b8bbf9353f0310aa6f87e09ba02c0875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4gBSau0q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AEWyURx9T5P6jGwGcWZ3TSA.png)T3】数据库名称

在下一页中，提交订单。

[![Submit Order](img/6631c9c0874f010c9e12234a4de07977.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Ul6gYnzl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A-BHwyYhkZvE5BbQy4E38EQ.png) *提交订单*

接下来，单击您的新数据库。

[![The new DB](img/b9de08b58bd04811fe44937a5c6a7d5c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--x9Plv7U6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AgGjYxpskmqX-i46fV8i88w.png) *新 DB*

在**用户**选项卡中，点击添加数据库用户。它将打开一个弹出窗口，您需要在其中提供用户名和密码。

[![username and password](img/4c111557809a2799f004f0ef821dd4bd.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---u0jsM_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AMBnQlpY6vBuID9YZVM0qIA.png) *用户名密码*

现在，为了连接到我们的数据库，我们需要使用下面突出显示的查询字符串。

[![Query string](img/987679fa9b2b48b49908a2b1224b22b0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--pQ0PvFaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AcELsm-xyUFwgg4Q6zElPag.png) *查询字符串*

现在，打开你的终端，停止 nodemon，安装 mongoose。

[![install mongoose](img/8318be211df694a4f0f065425d94cde0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--f3oDs2DY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A62VuhB8DefNVNTO_tRPTNA.png) *安装猫鼬*

接下来，我们在 **App.js** 中添加代码，以连接到我们的数据库。

```
 const express = require('express');
    const graphqlHTTP = require('express-graphql');
    const schema = require('./schema/schema');
    const mongoose = require('mongoose');

    const app = express();

    mongoose.connect('mongodb://nabs:useyourown@ds159546.mlab.com:59546/graphql-react');
    mongoose.connection.once('open', () => {
        console.log('Conneted to database');
    });

    app.use('/graphql', graphqlHTTP({
        schema,
        graphiql: true
    }));

    app.listen(4000, () => {
        console.log('Listening at port 4000');
    }); 
```

正在运行的 nodemon 将显示成功消息。

[![Connected finally](img/750482c70b9e3e36c0826ccbc0e48f9f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Kpd2y0dK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AjHXOrZAf9eLg1lWQf03h2A.png) *终于接上了*

现在，我们需要创建我们的 mongodb 模式。因此，继续在**服务器**中创建一个文件夹 **models** 。在里面我们创建了两个文件——**book . js**和 **author.js**

在 **author.js** 中添加以下代码以导出一个 **authorSchema** 。

```
 const mongoose = require('mongoose');
    const Schema = mongoose.Schema;

    const authorSchema = new Schema({
        name: String,
        age: Number
    });

    module.exports = mongoose.model('Author', authorSchema); 
```

在 **book.js** 中添加下面的代码来导出一个 **bookSchema** 。

```
 const mongoose = require('mongoose');
    const Schema = mongoose.Schema;

    const bookSchema = new Schema({
        name: String,
        genre: String,
        authorId: String
    });

    module.exports = mongoose.model('Book', bookSchema); 
```

接下来，转到 **schema.js** 并删除伪数据和所有带有伪数据的 return 语句。我们将添加一个突变，它将用于在我们的数据库中插入数据。

```
 const graphql = require('graphql');
    const Book = require('../models/book');
    const Author = require('../models/Author');

    const { GraphQLObjectType, GraphQLString, GraphQLSchema, GraphQLID, GraphQLInt, GraphQLList } = graphql;

    const BookType = new GraphQLObjectType({
        name: 'Book',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            genre: { type: GraphQLString },
            author: {
                type: AuthorType,
                resolve(parent, args){
                    //return authors.find(item => item.id === parent.authorId);
                }
            }
        })
    });

    const AuthorType = new GraphQLObjectType({
        name: 'Author',
        fields: ( ) => ({
            id: { type: GraphQLID },
            name: { type: GraphQLString },
            age: { type: GraphQLInt },
            books: {
                type: new GraphQLList(BookType),
                resolve(parent, args){
                    //return books.filter(obj => obj.authorId === parent.id);
                }
            }
        })
    });

    const RootQuery = new GraphQLObjectType({
        name: 'RootQueryType',
        fields: {
            book: {
                type: BookType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    //return books.find(item => item.id === args.id);
                }
            },
            author: {
                type: AuthorType,
                args: { id: { type: GraphQLID } },
                resolve(parent, args){
                    //return authors.find(item => item.id === args.id);
                }
            },
            books: {
                type: new GraphQLList(BookType),
                resolve(parent, args){
                    //return books;
                }
            },
            authors: {
                type: new GraphQLList(AuthorType),
                resolve(parent, args){
                    //return authors;
                }
            }
        }
    });

    const Mutation = new GraphQLObjectType({
        name: 'Mutation',
        fields: {
            addAuthor: {
                type: AuthorType,
                args: {
                    name: { type: GraphQLString },
                    age: { type: GraphQLInt }
                },
                resolve(parent, args){
                    let author = new Author({
                        name: args.name,
                        age: args.age
                    });
                    return author.save();
                }
            }
        }
    });

    module.exports = new GraphQLSchema({
        query: RootQuery,
        mutation: Mutation
    }); 
```

接下来，前往 graphiql 添加一个有突变的作者。

[![Author added with mutation](img/10cf183f86e0aac922be1e7c073aa856.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--wtIgnNN3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ARZHzyLIOPv9OGuV5t4XsCQ.png) *作者补充有突变*

此外，检查您的 mlab 是否添加了该作者。

[![mlab checking](img/d6f931a9f0751df8ea7f3f67f4a54deb.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--IYNuTt_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AaTzjUSOakZ9JwmavXPvplw.png) * mlab 校验*

现在，我们将添加一本书的突变。

```
 ...
    ...

    const Mutation = new GraphQLObjectType({
        name: 'Mutation',
        fields: {
            addAuthor: {
                type: AuthorType,
                args: {
                    name: { type: GraphQLString },
                    age: { type: GraphQLInt }
                },
                resolve(parent, args){
                    let author = new Author({
                        name: args.name,
                        age: args.age
                    });
                    return author.save();
                }
            },
            addBook: {
                type: BookType,
                args: {
                    name: { type: GraphQLString },
                    genre: { type: GraphQLString },
                    authorId: { type: GraphQLID }
                },
                resolve(parent, args){
                    let book = new Book({
                        name: args.name,
                        genre: args.genre,
                        authorId: args.authorId
                    });
                    return book.save();
                }
            }
        }
    });
    ...
    ... 
```

接下来，前往 graphiql 添加一本带有突变的**书**。注意，我们必须从 mlab，从新创建的作者那里获得 **authorId** 。

[![book mutation](img/5b57a4b127779185446c1d41d128c8cd.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--3aKmAr_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A0xbxmj9pITu3V4xUhC_y1Q.png) *图书突变*

添加更多的书，然后前往 mlab 验证您的添加。

[![mlab checking](img/83351a069791b5d5c47e903bad99b445.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--QpBJ_-AZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ATfZPDe9FZbxJyxFWWkMIwA.png) * mlab 校验*

本系列的第 2 部分到此结束。你可以在 github [链接](https://github.com/nabendu82/react-graphql)中找到代码。