# 使用 React 和 GraphQL-3 构建完整的应用程序

> 原文：<https://dev.to/nabendu82/build-a-complete-app-with-react-and-graphql-3-48h1>

欢迎来到本系列的第 3 部分。这个系列的灵感来自于免费代码营的 youtube 教程。

根据第二部分的变化，我们增加了两位作者和他们的一些书。

[![More Books](img/d059d134e9985e0917863e3bf7a81719.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--VwoWzuog--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AmclYrJeuLwnJd32BVhg3EA.png) *更多书籍*

我们现在将更新我们的 return 语句，以便我们的查询再次工作。现在，它们包含了获取数据的 mongodb 方法。所以，让我们编辑我们的 **schema.js**

```
…
…
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
                return Author.findById(parent.authorId);
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
                return Book.find({authorId: parent.id});
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
                return Book.findById(args.id);
            }
        },
        author: {
            type: AuthorType,
            args: { id: { type: GraphQLID } },
            resolve(parent, args){
                //return authors.find(item => item.id === args.id);
                return Author.findById(args.id);
            }
        },
        books: {
            type: new GraphQLList(BookType),
            resolve(parent, args){
                //return books;
                return Book.find({});
            }
        },
        authors: {
            type: new GraphQLList(AuthorType),
            resolve(parent, args){
                //return authors;
                return Author.find({});
            }
        }
    }
});
…
… 
```

现在，让我们检查一下我们的查询是否工作正常。第一个查询是获取所有的书。

[![Get all books](img/805c5ea0734f37c340c9d37d2b9de4af.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--aUGJ3dLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2Adcuz6RBGsSwbCd-WOHGUyw.png) *获取所有书籍*

下一步是让所有的作者和他们的书在一起。

[![Get all authors](img/a525d5f77a42a90fbd08f0e0828dd326.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--8Uucbd7S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A0mNW97cMVyHml59JRDQmzw.png) *获取所有作者*

接下来，是获取一本书的细节

[![Details of a book](img/0f475334da4fab41b7039c87b581680b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--_rYrAYrU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2Aeut8p24jfJiYA5V83ZK8ig.png) *一本书的细节*

最后一个是获取作者的详细信息

[![Details of an author](img/9fa674e540402abf406d10d1dc06bd76.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ftCjSFn1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A6OoAol0BQyWpl3BiuqgfKA.png) *作者详情*

现在，在我们的逻辑中有一个小问题。我们可以添加新书或作者，字段更少。考虑下面的情况，我们只添加一本书的名字。

[![Junk Book](img/83248fa0f333c5633d3c090e332c3f58.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--jCL29Gpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ARBUHDZGRWGYSTgBR1Ev9JA.png) *垃圾书*

我们也可以在 mongoDB 中验证。

[![mongoDB verification.](img/fb651e2615376471e9ff10c4d341d4c4.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--wFQK6q74--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AY0wSa0y3l2A7hyPA_d6vgQ.png) * mongoDB 验证。*

现在，为了避免这种情况，我们添加了一个新的 GraphQL 属性 **GraphQLNonNull**

因此，打开您的 **schema.js** 并进行更改，用粗体标记。

```
…
…
const { GraphQLObjectType, GraphQLString, GraphQLSchema, GraphQLID, GraphQLInt, GraphQLList, GraphQLNonNull } = graphql;
…
…
const Mutation = new GraphQLObjectType({
    name: 'Mutation',
    fields: {
        addAuthor: {
            type: AuthorType,
            args: {
                name: { type: new GraphQLNonNull(GraphQLString) },
                age: { type: new GraphQLNonNull(GraphQLInt) }
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
                name: { type: new GraphQLNonNull(GraphQLString) },
                genre: { type: new GraphQLNonNull(GraphQLString) },
                authorId: { type: new GraphQLNonNull(GraphQLID) }
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
…
… 
```

现在，如果我们转到我们的 Graphiql，如果没有必填字段，我们将无法添加任何内容。

[![Not null not allowed](img/99d862b11c621dedd24fe788f5ff2e4e.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--n1-dAaOw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AchLaXT6WbVLQHyplkfwFnA.png) *非空不允许*

接下来，我们将在我们的应用程序中添加前端逻辑 React。因此，前进到您的根目录，用 **create-react-app** 创建一个客户端。

[![create-react-app](img/74537f9f622bfc53e9b8f32ce8a95302.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--GdxVwa4D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2An67bLv6hsz7lchKRersZ_w.png) *创建-反应-app*

接下来，我们启动 react 客户端。

[![cd and npm](img/128754075350a35dd0199bf80957a9c8.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--97t4n7nC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AOuwkDN4jR8JoWnAcfVJSnQ.png) * cd 和 npm*

现在，我们的客户端运行在端口 3000，服务器运行在端口 4000。

接下来，我们清除 react app 自带的一些垃圾。在**客户端**的 **src** 文件夹中，删除除 **App.js** 、 **index.js** 和

[![Keep bare-bones](img/a0382d96b27f2d86bf666d08fa3f5719.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--b5SCAF2m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AUZSq_UjI_WMGcvIJK1Jw_Q.png) *保持裸机*

另外，保持 **index.js** 简单如下。

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root')); 
```

现在，将 **App.js** 修改如下。

```
import React from 'react';

function App() {
  return (
    <div className="main">
      <h1>Top books to Read</h1>
    </div>
  );
}

export default App; 
```

以及下面的 **index.css** 。

```
body {
  font-family: "Helvetica Neue", sans-serif;
} 
```

它将呈现如下所示的 react 应用程序。

[![Our React app](img/77031d964449457832eb24d7142652da.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--grlgyV9b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ABrVEyJ5Njk5BU45MB5l6NQ.png) *我们的 React app*

现在，在 **src** 目录下新建一个文件夹 **components** 并添加一个文件 **BookList.js** 到其中。

[![BookList.js](img/9016bdd1eee38d2e8b2c2d9bdb1d5aa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oe_rKhcA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2878/1%2AnJRNxMF0RpryzAzFgVe26w.png)T3】booklist . js

接下来，我们将在 **BookList.js** 中添加一个基于类的组件。

```
import React, { Component } from 'react'

class BookList extends Component {
  render() {
    return (
      <div>
          <ul className="book-list">
            <li>Book Name</li>
          </ul>
      </div>
    )
  }
}

export default BookList; 
```

接下来，在 **App.js** 中添加这个组件

```
import React from 'react';
import BookList from './components/BookList';

function App() {
  return (
    <div className="main">
      <h1>Top books to Read</h1>
      <BookList />
    </div>
  );
}

export default App; 
```

我们需要一个 GraphQL 客户端来联系我们的 GraphQL 服务器，我们将使用的是 Apollo。

继续停止您的客户端并安装 graphql 客户端所需的以下软件包。

```
npm install apollo-boost react-apollo graphql --save 
```

[![GraphQL client](img/bdba718d604029b4226f2b1eaf44dfc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mKZ1b3w---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AyrHJHVagzzbdYZBTy9dxRw.png)T3】graph QL 客户端

**注:**在这里遇到了一个问题，在 **npm 安装了 Apollo-boost react-Apollo graph QL—save**之后。在[在线](https://github.com/facebook/create-react-app/issues/6398)找到解决方案，删除**客户端**内的**节点模块**文件夹，进行**纱线安装**

现在，让我们开始使用 graphql。我们将编辑 BookList.js 文件以从 graphql 服务器获取数据。

```
import React, { Component } from 'react';
import { gql } from 'apollo-boost';
import { graphql } from 'react-apollo';

const getBooksQuery = gql`
    {
        books {
            name
            id
        }
    }
`;

class BookList extends Component {
  render() {
    console.log(this.props);
    return (
      <div>
          <ul className="book-list">
            <li>Book Name</li>
          </ul>
      </div>
    )
  }
}

export default graphql(getBooksQuery)(BookList); 
```

在检查我们通过 console.log 得到的内容时，我们得到了一个严重的 cors 错误。

[![cors error](img/18f6011d4f1eaf07d000759c965a9164.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--gAhJ0tW0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AUq_AYQmEymfQ-m_5m75EVA.png) * cors 错误*

因此，我们将在我们的服务器中安装 **cors** 模块。停止服务器并由 npm 安装。

[![cors install](img/7b19714784300871fc9dcf710b6d623f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sdFlSy-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A1Ppg_Xt5c6dt1xS_tnHyBw.png)T3】CORS 安装

现在，转到**服务器**文件夹中的 **app.js** 并添加 cors

```
const express = require('express');
const graphqlHTTP = require('express-graphql');
const schema = require('./schema/schema');
const mongoose = require('mongoose');
const cors = require('cors');
const app = express();

app.use(cors());

mongoose.connect('mongodb://nabsNew:delete1@ds159546.mlab.com:59546/graphql-react');
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

现在，刷新你的应用程序，你不会得到错误。

[![Won’t get cors error](img/c024d1d5f1d4946a698c7e94625fd24f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--c63R2xCx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2A5sMIBmTXbgAzISpru7QVCw.png) *不会得到 cors 错误*

现在，我们得到了正确的书籍，我们将更新我们的 **BookList.js** 以在屏幕上显示书籍。

```
import React, { Component } from 'react';
import { gql } from 'apollo-boost';
import { graphql } from 'react-apollo';

const getBooksQuery = gql`
    {
        books {
            name
            id
        }
    }
`;

class BookList extends Component {
    displayBooks() {
        var data = this.props.data;
        if (data.loading) {
            return (<div>Loading books...</div>);
        } else {
            return data.books.map(book => {
                return (
                    <li key={book.id}>{book.name}</li>
                );
            })
        }
    }

    render() {
        console.log(this.props);
        return (
            <div>
                <ul className="book-list">
                    {this.displayBooks()}
                </ul>
            </div>
        )
    }
}

export default graphql(getBooksQuery)(BookList); 
```

这里，我们添加了一个方法 **displayBooks** ()来显示书籍。如果你已经注意到前面的 console.log，当**加载**为真时，第一次呈现一个空列表。当**加载**为假时，我们只在第二部分得到书单。

[![The Loading](img/423ae6c30cd0ca6643902bfa59f95992.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--hfs70e4T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AdEHtuKWGHDEEHQLGlRiLug.png) *装货*

这就完成了我们的通话，我们所有的书都显示在应用程序上。

[![Top Books to read](img/628959488369f97e2a7d31e622e33d3e.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--clWcDWrK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ADzzLyccAUMWy2GD_fzfvvg.png) *顶级书籍阅读*

本系列的第 3 部分到此结束。你可以在 github [链接](https://github.com/nabendu82/react-graphql)中找到代码。