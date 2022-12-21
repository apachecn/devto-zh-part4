# 全堆叠:分页+数据加载器

> 原文：<https://dev.to/heymarkkop/fullstacking-pagination-dataloader-mc2>

经过一些挑战，我终于在应用程序中添加了分页。这需要我添加[数据加载器](https://github.com/graphql/dataloader)，ModernQueryRenderer 和 [RefetchContainer](https://relay.dev/docs/en/refetch-container) 。

我仍然不能详细解释我所做的一切，但我会分享我所改变的文件。

```
// EventList.js
import {
  createRefetchContainer, graphql,
} from 'react-relay';
// ...
<View style={styles.container}>
        <FlatList
          data={events.edges}
          renderItem={renderItem}
          keyExtractor={item => item.node.id}
          onEndReached={onEndReached}
          onRefresh={onRefresh}
          refreshing={isFetchingTop}
          ItemSeparatorComponent={() => <View style={styles.separator} />}
          ListFooterComponent={null}
        />
</View>
// ...
const EventListPaginationContainer = createRefetchContainer(
  EventList,
  {
    query: graphql`
      fragment EventList_query on Query  
      @argumentDefinitions(
        count: {type: "Int", defaultValue: 10}
        cursor: {type: "String"}
      ) {
        events(first: $count, after: $cursor)
          @connection(key: "EventList_events") {
          pageInfo {
            hasNextPage
            endCursor
          }
          edges {
            node {
              id
              title
              date
              description
              author
            }
          }
        }
      }
    `,
  },
  graphql`
      query EventListPaginationQuery($count: Int!, $cursor: String) {
        ...EventList_query @arguments(count: $count, cursor: $cursor)
      }
    `,
);
// ...
export default createQueryRendererModern(
  EventListPaginationContainer,
  EventList,
  {
    query: graphql`
      query EventListQuery($count: Int!, $cursor: String) {
        ...EventList_query
      }
    `,
    variables: {cursor: null, count: 5},
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

我们已经更新了 EventList 以显示一个 FlatList 组件，并使用了 createQueryRendererModern 和 createRefetchContainer。

```
// createQueryRendererModern.js
import * as React from 'react';
import {Text} from 'react-native';
import {QueryRenderer} from 'react-relay';

import Environment from './Environment';

export default function createQueryRenderer(FragmentComponent, Component, config) {
  const {query, queriesParams} = config;

  class QueryRendererWrapper extends React.Component {
    render() {
      const variables = queriesParams
        ? queriesParams(this.props)
        : config.variables;

      return (
        <QueryRenderer
          environment={Environment}
          query={query}
          variables={variables}
          render={({error, props}) => {
            if (error) {
              return <Text>{error.toString()}</Text>;
            }

            if (props) {
              return <FragmentComponent {...this.props} query={props} />;
            }

            return <Text>loading</Text>;
          }}
        />
      );
    }
  }

  return QueryRendererWrapper;
} 
```

Enter fullscreen mode Exit fullscreen mode

在服务器端，我们需要在应用程序的上下文中添加数据加载器，并使用它们从 Mongoose 加载事件。我们还使用[graph QL-mongose-loader](https://github.com/entria/graphql-mongoose-loader)来抽象从 MongoDB 加载数据时的交互。

```
// server/app.js
// ...
const graphqlSettingsPerReq = async req => {
  const { currentUser } = await getUser(req.header.authorization);

  const dataloaders = Object.keys(loaders).reduce(
    (acc, loaderKey) => ({
      ...acc,
      [loaderKey]: loaders[loaderKey].getLoader(),
    }),
    {},
  );

  return {
    schema,
    context: {
      currentUser,
      req,
      dataloaders
    }
  };
};
// ... 
```

Enter fullscreen mode Exit fullscreen mode

```
// EventLoader.js
import DataLoader from 'dataloader';
import { connectionFromMongoCursor, mongooseLoader } from '@entria/graphql-mongoose-loader';
import { ConnectionArguments } from 'graphql-relay';

import EventModel, { IEvent } from './EventModel';

export default class Event {

  constructor(data) {
    this.id = data.id ||  data._id;
    this._id = data._id;
    this.title = data.title;
    this.description = data.description;
    this.author = data.author;
  }
}

export const getLoader = () => new DataLoader(ids => mongooseLoader(EventModel, ids));

const viewerCanSee = () => true;

export const load = async (context, id) => {
  if (!id) {
    return null;
  }

  let data;
  try {
    data = await context.dataloaders.EventLoader.load(id);
  } catch (err) {
      console.log(err)
    return null;
  }

  return viewerCanSee() ? new Event(data, context) : null;
};

export const clearCache = ({ dataloaders }, id) => dataloaders.EventLoader.clear(id.toString());
export const primeCache = ({ dataloaders }, id, data) => dataloaders.EventLoader.prime(id.toString(), data);
export const clearAndPrimeCache = (context, id, data) => clearCache(context, id) && primeCache(context, id, data);

export const loadEvents = async (context, args) => {
    const where = args.search ? { title: { $regex: new RegExp(`^${args.search}`, 'ig') } } : {};
    const event = EventModel.find(where).sort({ createdAt: -1 });
  return connectionFromMongoCursor({
    cursor: event,
    context,
    args,
    loader: load,
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// QueryType.js
export default new GraphQLObjectType({
  name: "Query",
  description: "The root of all... queries",
  fields: () => ({
    node: nodeField,
    // ...
    },
    events: {
      type: EventConnection.connectionType,
      args: {
        ...connectionArgs,
        search: {
          type: GraphQLString
        }
      },
      resolve: (obj, args, context) => {
        return EventLoader.loadEvents(context, args)},
    },
    // ...
    }
  })
}); 
```

Enter fullscreen mode Exit fullscreen mode

这应该足以启用分页。
记得运行`yarn update-schema`和`yarn relay`来分别更新模式和生成的文件