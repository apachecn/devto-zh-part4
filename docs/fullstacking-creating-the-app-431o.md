# 全堆叠:创建应用程序

> 原文：<https://dev.to/heymarkkop/fullstacking-creating-the-app-431o>

我们最终创造了应用程序本身。这个挑战要求我们创建一个不同用户可以访问和添加对象的列表。起初，我想创建一个出售二手物品列表，但是我们应该创建一个事件列表。我们的活动将由标题、日期、描述和作者组成。

## 事件组件

让我们首先创建`EventCard`和`EventList`组件，并更新查询以从事件中获取更多信息。注意每当你改变一个 **graphql 查询**，你必须运行`yarn relay`

```
// packges/app/src/components/EventList.js
import React from 'react';
import {ScrollView, Text} from 'react-native';
import {graphql} from 'babel-plugin-relay/macro';
import {QueryRenderer} from 'react-relay';
import Environment from '../relay/Environment';
import EventCard from './EventCard';

const EventList = ({query}) => {
  const {events} = query;
  return (
    <>
      <Text>Event List</Text>
      <ScrollView>
        {events.map(event => (
          <EventCard key={event.id} event={event} />
        ))}
      </ScrollView>
    </>
  );
};

const EventListQR = () => {
  return (
    <QueryRenderer
      environment={Environment}
      query={graphql`
        query EventListQuery {
          events {
            id
            title
            date
            description
          }
        }
      `}
      variables={{}}
      render={({error, props}) => {
        console.log('qr: ', error, props);
        if (error) {
          return <Text>{error.toString()}</Text>;
        }

        if (props) {
          return <EventList query={props} />;
        }

        return <Text>loading</Text>;
      }}
    />
  );
};

export default EventListQR; 
```

Enter fullscreen mode Exit fullscreen mode

```
/// packages/app/src/components/EventCard.js
import React from 'react';
import {Text} from 'react-native';

const EventCard = ({event}) => {
  return (
    <>
      <Text>Title: {event.title}</Text>
      <Text>Date: {event.date}</Text>
      <Text>Description: {event.description}</Text>
      <Text>Author: {event.author}</Text>
    </>
  );
};

export default EventCard; 
```

Enter fullscreen mode Exit fullscreen mode

你还需要一个更新的`schema.graphql`。像处理产品一样，在服务器的包中创建一个**事件类型**和**事件模型**。运行`yarn update-schema`，将服务器的`graphql.schema`复制到 app 的`graphql.schema`。检查这个[提交](https://github.com/Markkop/fullstacking/commit/ca91c9cc42039300525dee692e165012f6bccf48)的一些细节。
运行`yarn relay`来编译 graphql 查询的代码。

然后，我们可以将 EventList 组件导入到我们的应用程序组件中，并呈现它

```
// packages/app/src/App.js
const App = ({query}) => {
  const {products} = query;

  return (
    <Fragment>
      <EventList />
    </Fragment>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 事件用 Formik 创建

首先，我们必须在我们的服务器中配置我们的突变根类型

```
// packages/server/graphql/schema.js
const {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLID,
  GraphQLList,
  GraphQLNonNull,
  GraphQLString
} = require("graphql");
const { fromGlobalId, mutationWithClientMutationId } = require("graphql-relay");
const eventGraphQLType = require("./eventType");
const Event = require("../models/Event");

const Query = new GraphQLObjectType({
  name: "Query",
  fields: {
    event: {
      type: eventGraphQLType,
      args: { id: { type: GraphQLNonNull(GraphQLID) } },
      resolve(parent, args) {
        return Event.findById(fromGlobalId(args.id).id);
      }
    },
    events: {
      type: GraphQLList(eventGraphQLType),
      resolve() {
        return Event.find();
      }
    }
  }
});

const EventCreate = mutationWithClientMutationId({
  name: "EventCreate",
  inputFields: {
    title: {
      type: new GraphQLNonNull(GraphQLString)
    },
    date: {
      type: new GraphQLNonNull(GraphQLString)
    },
    description: {
      type: new GraphQLNonNull(GraphQLString)
    }
  },
  outputFields: {
    id: {
      type: GraphQLID,
      resolve: payload => payload.id
    }
  },
  mutateAndGetPayload: async ({ title, date, description }) => {
    const newEvent = new Event({
      title,
      date,
      description
    });
    const returnedObject = await newEvent.save();
    const eventId = await returnedObject._id;
    console.log(`New Event created with id: ${eventId}`); //this will be in a subscription

    return {
      id: eventId
    };
  }
});

const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    EventCreate: EventCreate
  }
});

module.exports = new GraphQLSchema({
  query: Query,
  mutation: Mutation
}); 
```

Enter fullscreen mode Exit fullscreen mode

*(我们可能很快就要拆分这段代码)*

我们正在创建根**突变类型**和一个**事件创建**突变类型。这个新类型接收一个**标题**、**日期**和**描述**字符串，并返回新事件的 **id** 。

运行`yarn update-schema`在服务器的包中创建`schema.graphql`。应该是这样的。

```
type Event {
  id: ID!
  _id: String
  title: String
  date: String
  description: String
  author: String
}

input EventCreateInput {
  title: String!
  date: String!
  description: String!
  clientMutationId: String
}

type EventCreatePayload {
  id: ID
  clientMutationId: String
}

type Mutation {
  EventCreate(input: EventCreateInput!): EventCreatePayload
}

type Query {
  event(id: ID!): Event
  events: [Event]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在复制`schema.graphql`来替换应用程序包中的同一个文件。

然后用 **Formik** 创建一个`EventCreate`组件如下:

```
// packages/app/components/EventCreate.js
import React from 'react';
import {TextInput, Button, ButtonText} from 'react-native';
import {Formik} from 'formik';
import EventCreateMutation from './EventCreateMutation';

const EventCreate = () => {
  const handleSubmit = values => {
    const {title, date, description} = values;

    const input = {
      title,
      date,
      description,
    };

    const onCompleted = id => {
      // Some implementation that requires the id from
      // the new event created
      alert(JSON.stringify(id));

      // Redirect
      // this.props.navigation.navigate('UserList');
    };

    const onError = err => {
      console.error(err);
    };

    EventCreateMutation.commit(input, onCompleted, onError);
  };
  return (
    <Formik
      initialValues={{title: '', date: '', description: ''}}
      onSubmit={values => handleSubmit(values)}>
      {({values, handleChange, handleSubmit}) => (
        <>
          <TextInput
            placeholder="Title"
            onChangeText={handleChange('title')}
            value={values.title}
          />
          <TextInput
            placeholder="Date"
            onChangeText={handleChange('date')}
            value={values.date}
          />
          <TextInput
            placeholder="Short description"
            onChangeText={handleChange('description')}
            value={values.description}
          />
          <Button onPress={handleSubmit} title="Add Event"></Button>
        </>
      )}
    </Formik>
  );
};

export default EventCreate; 
```

Enter fullscreen mode Exit fullscreen mode

要使用突变，您必须创建一个**突变文件**并提交它，如上所示。

```
// packages/app/components/EventCreateMutation.js
import {commitMutation, graphql} from 'react-relay';
import Environment from '../relay/Environment';

const mutation = graphql`
  mutation EventCreateMutation($input: EventCreateInput!) {
    EventCreate(input: $input) {
      id
    }
  }
`;

function commit(input, onCompleted, onError) {
  return commitMutation(Environment, {
    mutation,
    variables: {
      input,
    },
    onCompleted,
    onError,
  });
}

export default {commit}; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以添加新的事件，在重新加载后(例如通过`yarn shake`),我们可以看到我们的新事件在列表中。我们可能还不担心实时更新和订阅。

## 导航

在 React 中，我们可以使用 react-router，但在 React-Native 中，我们将使用`react-navigation`安装它及其依赖项，方法是在`packages/app`中运行以下代码:
`yarn add react-native-reanimated react-native-gesture-handler react-native-screens`
，并添加我们将使用的导航器:
`yarn add react-navigation-tabs`

现在编辑`App.js`如下:

```
import React from 'react';
import {createAppContainer} from 'react-navigation';
import { createMaterialTopTabNavigator } from 'react-navigation-tabs';
import EventList from './EventList';
import EventCreate from './EventCreate';

const App = createMaterialTopTabNavigator(
  {
    EventCreate: {screen: EventCreate},
    EventList: {screen: EventList},
  },
  {
    initialRouteName: 'EventList',
  },
);

export default createAppContainer(App); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在屏幕间导航。稍后我们可能会把这个导航器换成另一个。

## 调试提示

添加/更改 graphql 查询后始终运行`yarn relay`运行
更改`schema.js`后始终运行
保持两个`schema.graphql`文件相同
更改应用程序的依赖关系后运行`yarn android`尝试`yarn start:app --reset-cache`
运行`yarn redirect`
关闭并打开智能手机中的应用程序

## 参考文献

[react native+formik+yup❤️](https://medium.com/fotontech/react-native-formik-yup-%EF%B8%8F-18465e020ea0)
[formik 文档](https://jaredpalmer.com/formik/docs/guides/react-native)
[React 导航文档](https://reactnavigation.org/docs/en/hello-react-navigation.html)
[GraphQL.js 文档](https://github.com/graphql/graphql-relay-js)