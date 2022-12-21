# 用接口增强 GraphQL 模式

> 原文：<https://dev.to/eveporcello/enhancing-graphql-schemas-with-interfaces-53a2>

*本文原帖[月球公路](https://moonhighway.com/enhancing-schemas-with-interfaces)。*

任何 GraphQL 项目的核心都是一个模式，一个描述 API 上可用的所有类型、查询、变异和订阅的文档。除了这些基本类型，GraphQL 模式定义语言为我们提供了一种创建[接口](https://graphql.org/learn/schema/#interfaces)的方法。

接口的概念经常出现在面向对象的语言中。它们定义了一个对象为了与其他对象通信而必须实现的属性和方法。例如，交通灯不需要知道路上每辆车的内部工作情况。但是，路上的每一辆车确实需要红灯停，绿灯行。我们可以为交通灯定义一个接口，要求路上的每辆车都实现`stop()`和`go()`方法。只要汽车、公共汽车或行人拥有为`stop()`和`go()`定义的方法，他们就能够将*与红绿灯*对接。

在 GraphQL 中，接口有类似的用途。GraphQL 接口需要类型来实现特定的字段，而不是属性和方法。让我们看看通过合并接口来改进现有的模式意味着什么。

我们已经为滑雪场员工建立了一个计划。我们可以通过使用接口来改进这个模式。在这里，我们有一个类型`Employee`，它有以下字段:

```
type  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  job:  JobType  }  enum  JobType  {  LIFTOPERATOR  SKIPATROL  INSTRUCTOR  BARTENDER  } 
```

乍一看，这看起来不错，但还有改进的空间。电梯操作员和酒保可能有几个共享字段，但他们中的每一个可能都有唯一的字段。让我们看一下每个作业的样本数据记录。我们希望在 API 中提供一些特定于角色的字段:

**bartenders.json**

```
[  {  "id":  "0001",  "firstName":  "Topher",  "lastName":  "Saunders",  "assignment":  "SUMMIT",  "supervisor":  true,  "shift":  1  }  ] 
```

**教官. json**

```
[  {  "id":  "0002",  "firstName":  "Matt",  "lastName":  "Christie",  "level":  3,  "privateLessons":  true  }  ] 
```

**lift ops . JSON**T2】

```
[  {  "id":  "0008",  "firstName":  "Shawni",  "lastName":  "Horizon",  "yearsExperience":  1  }  ] 
```

**patrol.json**

```
[  {  "id":  "0007",  "firstName":  "Denise",  "lastName":  "Lankman",  "certified":  true,  "aviLevel":  3  }  ] 
```

我们可以通过使用 GraphQL **接口**来增强我们的模式。我们将从把`Employee`类型转换成一个`Employee`接口开始。这将包括每个员工都有的基本字段:

```
interface  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  } 
```

接下来，我们将为每个雇员类型创建该接口的实现。所有这些新类型都需要实现与接口相同的字段(`id`、`firstName`和`lastName`)，然后工作角色特有的字段可以添加到每个类型:

```
type  Bartender  implements  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  assignment:  Location!  supervisor:  Boolean!  shift:  Int!  }  type  Instructor  implements  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  level:  Int!  privateLessons:  Boolean!  }  type  LiftOperator  implements  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  yearsExperience:  Int!  }  type  SkiPatrol  implements  Employee  {  id:  ID!  firstName:  String!  lastName:  String!  certified:  Boolean!  aviLevel:  Int!  } 
```

如果我们想查询在 Snowtooth Mountain 工作的所有人的列表，不管他们的工作角色如何，我们现在可以使用`Employee`接口:
来定义该查询

```
type  Query  {  allEmployees:  [Employee!]!  } 
```

为了实现这一点，我们需要在解析器中反映这一变化。我们需要添加一个`__resolveType`解析器，它将返回被解析类型的名称:

```
const resolvers = {
  Query: {...},
  Mutation: {...},
  Employee: {
    __resolveType: parent => {
      if (parent.assignment) {
        return "Bartender";
      } else if (parent.yearsExperience) {
        return "LiftOperator";
      } else if (parent.certified) {
        return "SkiPatrol";
      } else {
        return "Instructor";
      }
    }
  }
}; 
```

在解析雇员时，`__resolveType`解析器将查看父数据，并决定正在解析哪种类型的`Employee`。在这种情况下，我们检查父对象，看它是否包含与每种类型相关的字段。例如，如果父节点有一个用于`certified`的字段，那么它们的类型必须是`SkiPatrol`。

现在，我们可以查询所有 Snowtooth 员工的列表，而不管他们是什么类型:

```
query  {  allEmployees  {  id  firstName  lastName  }  } 
```

可以将`__typename`字段添加到我们的选择集中，以查看我们正在处理的员工类型:

```
query  {  allEmployees  {  __typename  id  firstName  lastName  }  } 
```

而且，在同一个查询中，我们仍然可以使用内联片段来请求关于每个单独类型的特定数据:

```
query  {  allEmployees  {  __typename  id  firstName  lastName  ...  on  SkiPatrol  {  certified  }  }  } 
```

该查询返回的数据将包含所有雇员的`id`、`firstName`、`lastName`，以及仅滑雪巡逻队雇员的`certified`。查询的 JSON 响应看起来像这样:

```
[  {  "__typename":  "SkiPatrol",  "id":  "0011",  "firstName":  "Jill",  "lastName":  "Johnson",  "certified":  "true"  },  {  "__typename":  "Bartender",  "id":  "0012",  "firstName":  "Rebecca",  "lastName":  "Wilson"  }  ...  ] 
```

接口也使你的 API 更具可扩展性和可维护性。当一个新的工作角色出现时，您可以为实现`Employee`接口的角色创建形式化的对象类型。这将使新类型在您的模式中解析 employees 的所有字段上都可用。

另一个好处是你仍然可以在你选择的任何地方独立地使用接口类型:

```
type  Query  {  allBartenders:  [Bartender!]!  allInstructors:  [Instructor!]!  allLiftOperators:  [LiftOperator!]!  allSkiPatrol:  [SkiPatrol!]!  } 
```

最后，让我们再尝试一个查询，一个显示什么类型实现了`Employee` :
的自省查询

```
query  UnionInterfaceTypes  {  __type(name:  "Employee")  {  possibleTypes  {  name  kind  }  }  } 
```

该查询返回实现了`Employee`接口的每种类型的列表:

```
{  "data":  {  "__type":  {  "possibleTypes":  [  {  "name":  "Bartender",  "kind":  "OBJECT"  },  {  "name":  "Instructor",  "kind":  "OBJECT"  },  {  "name":  "LiftOperator",  "kind":  "OBJECT"  },  {  "name":  "SkiPatrol",  "kind":  "OBJECT"  }  ]  }  }  } 
```

当你用 GraphQL 对你的领域对象建模时，接口是一个很好理解的结构。不要犹豫，今天就开始使用它们。