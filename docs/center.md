# GraphQLifying REST

> 原文：[https://dev.to/open-graphql/graphqlifying-rest-mm](https://dev.to/open-graphql/graphqlifying-rest-mm)

[![](img/5af9370dad67e26189732a15eac185e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4MQi8JHy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3600/1%2AMXi8GpY6jVHwYYOvY3CZmA.jpeg)

When adopting GraphQL into an existing project or an engineering team, one of the easiest ways to get started is to put a GraphQL API in front of your existing REST API endpoints.

Doing this will allow your team to immediately begin seeing some of the benefits that GraphQL has to offer without having to sacrifice a large amount of engineering work.

With AWS AppSync you can specify **“HTTP”** data sources to bring in existing HTTP endpoints & perform queries & mutations against them in GraphQL.

Let’s take a look at how we can port over these two types of HTTP APIs:

*   **Cryptocurrency API **that returns an array of data & allows us to query based on limit & starting point for pagination.

*   **GitHub Jobs API** that returns an array of jobs. We need to be able to query based on location & job description.

*Let’s start building!*

## Getting Started

The first step is to create a new AWS AppSync API. To do so, visit the [AppSync dashboard](https://console.aws.amazon.com/appsync/home) & click **Create API**.

Next, choose Build from scratch & click **Next.**

[![Build from scratch](img/c8127e12f7f2beaf44315daa78118974.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hpyiq8Rm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3968/1%2AWZrGJ1L3gEZKKOuDihIIFg.png)*Build from scratch*

Next, give your AppSync API a name & click **Create**.

## Building a **Cryptocurrency GraphQL API**

Now that we’ve created the AppSync API, we will need to add our first data source.

> The first API we’ll be converting will be the [Coinlore API](https://www.coinlore.com/cryptocurrency-data-api).

In the left menu of your AppSync API click on Data Sources.

Next, click **Create data source.**

Here, give the data source a name of **CryptoAPI**, a Data source type of **HTTP Endpoint**, & an HTTP Endpoint of [**https://api.coinlore.com](https://api.coinlore.com) **then click **Create.**

Now that we’ve created our data source, let’s take a look at some of the requests that we’ll be making to the API:

[https://api.coinlore.com/api/tickers/](https://api.coinlore.com/api/tickers/)
[https://api.coinlore.com/api/tickers/?start=100](https://api.coinlore.com/api/tickers/?start=100)
[https://api.coinlore.com/api/tickers/?limit=100](https://api.coinlore.com/api/tickers/?limit=100)

Next, let’s take a look at the data structure that we’ll be working with:

```
{
  data: [
    {
      id: "90",
      symbol: "BTC",
      name: "Bitcoin",
      nameid: "bitcoin",
      rank: 1,
      price_usd: "6943.53",
      ...
    },
    {
      id: "80",
      symbol: "ETH",
      name: "Ethereum",
      nameid: "ethereum",
      rank: 2,
      price_usd: "186.20",
      ...
    }
  ],
  info: {
    coins_num: 2148,
    time: 1557709606
  }
} 
```

We will want to access the **data** array. With that in mind, we can create a GraphQL type in our schema (**Coin**) that corresponds to the objects in the data as well as a query that will fetch & return an array of the new type.

To create this new type & query click on **Schema** in the left menu, enter the following schema, & then click **Save**.

```
type Coin {
 symbol: String
 name: String
 rank: String
 price_usd: String
}

type Query {
 listCoins(limit: Int, start: Int): [Coin]
 listCoinsJSON: AWSJSON
} 
```

> You’ll notice that the listCoins query has an optional limit & start argument that we’ll need to handle in the resolver.

Now that we’ve defined our schema, let’s create a resolver for the **listCoins** query. To do so, find the **listCoins** query in the resolvers list on the right hand side of the schema in the **Schema** view. Next, click on **Attach**.

The resolver consists of three parts:

1.  **Data source** — Defines the data source for the resolver

2.  **Request mapping template **— Takes the incoming request after a GraphQL operation is parsed and converts it into a request configuration for the selected data source operation.

3.  **Response mapping template** — Interprets responses from your data source and maps to the shape of the GraphQL field output type

For the data source, choose the **CryptoAPI** data source we created earlier.

For the request mapping template, use the following request mapping template:

```
{
  "version": "2018-05-29",
  "method": "GET",
  "resourcePath": "/api/tickers/",
  "params": {
      "query": $util.toJson($ctx.args)
  }
} 
```

The **method** can be either PUT, POST, GET, DELETE, or PATCH. The **resourcePath** is the path of the url that you’d like to hit. The params are where you can specify query params (query), headers (headers), & the body (body) for PUT, POST, or PATCH operations.

In the query params of the above request mapping template you will see that we are specifying the $ctx.args as the query. This will allow us to pass in any arguments to the GraphQL query & they will be mapped into query parameters.

For the response mapping template, use the following:

```
#set($body = $util.parseJson($ctx.result.body))
$util.toJson($body.data) 
```

This response mapping template will parse the data & return the data property in the form of JSON back to us.

Save the new resolver & then click **Queries** in the left menu.

To test the new resolver, we can try out the following queries:

```
query listCoins {
  listCoins {
    price_usd
    name
    symbol
    rank
  }
}

query listCoinsJSON {
  listCoinsJSON
}

query listCoins {
  listCoins(limit: 5) {
    price_usd
    name
    symbol
    rank
  }
}

query listCoins {
  listCoins(start: 10) {
    price_usd
    name
    symbol
    rank
  }
} 
```

All of these queries should work for you, returning different subsets of the data from the API.

Congratulations, you’ve successfully migrated your first REST API to GraphQL!

## Building a **GitHub Jobs GraphQL API**

Next, let’s look at how we can fetch the jobs data from the [GitHub Jobs API](https://jobs.github.com/api).

The API endpoints we will be working with are:

[https://jobs.github.com/positions.json](https://jobs.github.com/positions.json) [https://jobs.github.com/positions.json?location=remote](https://jobs.github.com/positions.json?location=remote)
[https://jobs.github.com/positions.json?description=react](https://jobs.github.com/positions.json?description=react)
[https://jobs.github.com/positions.json?search=javascript&page=2](https://jobs.github.com/positions.json?search=javascript&page=2)

To get started, create a new HTTP data source called **GitHubJobsAPI** & set the HTTP endpoint to [https://jobs.github.com](https://jobs.github.com).

The data returned from the API call looks like this:

```
[
  {
    type: "Full Time",
    url: "[https://jobs.github.com/positions/9636b280-e12a-11e8-819f-7c947fed1ee2](https://jobs.github.com/positions/9636b280-e12a-11e8-819f-7c947fed1ee2)",
    company: "Homestars",
    location: "Toronto",
    title: "Salesforce Engineer ",
    description: "WHO WE ARE: Canadians spend...."
  },
  {
    type: "Contract",
    url: "[https://jobs.github.com/positions/8c96ec9e-e130-11e8-9bb2-d87985623e61](https://jobs.github.com/positions/8c96ec9e-e130-11e8-9bb2-d87985623e61)",
    created_at: "Mon Nov 05 19:26:48 UTC 2018",
    company: "Rifle Paper Co.",
    location: "Remote",
    title: "FREELANCE PHP DEVELOPER",
    description: "OVERVIEW: We are...
  }
] 
```

Let’s create a GraphQL type in our schema (**Job**) that corresponds to the objects in the array as well as a query that will fetch & return an array of the new **Job** type. We’ll also create an input for the arguments that we will be passing in to the query. Add the following to the schema, & then click **Save**.

```
type Job {
 title: String
 description: String
 url: String
 type: String
 company: String
 location: String
}

input JobsInput {
 page: String
 description: String
 location: String
 search: String
}

type Query {
  listJobs(query: JobsInput): [Job]
} 
```

Now that we’ve defined our schema, let’s create a resolver for the **listJobs** query. To do so, find the **listJobs** query in the resolvers list on the right hand side of the schema in the **Schema** view. Next, click on **Attach**.

For the data source, choose the **GitHubJobsAPI** data source we created earlier.

For the request mapping template, use the following:

```
{
  "version": "2018-05-29",
  "method": "GET",
  "resourcePath": "/positions.json",
  "params":{
      "query":$util.toJson($ctx.args.query)
  }
} 
```

For the response mapping template, use the following:

```
#set($body = $util.parseJson($ctx.result.body))
$util.toJson($body) 
```

Like the listCoins response mapping template, this response mapping template will parse the data & return the data property in the form of JSON back to us.

Save the new resolver & then click **Queries** in the left menu.

To test the new resolver, we can try out the following queries:

```
query listJobs {
  listJobs(query: {
    search: "javascript"
    page: "3"
  }) {
    title
    location
    description
  }
}

query listJobs {
  listJobs(query: {
    location: "remote"
  }) {
    title
    location
    description
  }
}

query listJobs {
  listJobs(query: {
    description: "react native"
  }) {
    title
    location
    description
  }
} 
```

## Next Steps

The two examples we covered only went over GET requests. What if you wanted to do a POST, PUT, PATCH, or DELETE?

The main difference would be in the request mapping template. Here is where you can define your method as well as pass in any headers or body to the request.

Let’s look at how we might pass in data in the body using a POST request.

First, let’s take a look at the mutation definition:

```
createItem(body: STRING): [Item] 
```

Next, let’s take a look at the request mapping template:

```
{
  "version": "2018-05-29",
  "method": "POST",
  "resourcePath": "/api/yourapi/",
  "params": {
      "body": $ctx.args.body
  }
} 
```

How about passing in headers / authorization headers?

```
#set($authHeader = "Bearer " + "SOMETOKEN")

{

  "version": "2018-05-29",

  "method": "POST",

  "resourcePath": "/api/yourapi/",

  "params": {

      "body": $ctx.args.body,

      "headers": {

        "Content-Type": "application/json",

        "Authorization": $authHeader

      }

  }

} 
```

## 
  
Conclusion

To view a video walkthrough of how to do this, check out this video:

<center>

Though we are currently working on it, as of this post, the Amplify CLI does not have direct support for HTTP resolvers. Support should be released soon, & when it is I’ll update this post to show how to do this with the Amplify CLI.

If you’d like to learn more about working with HTTP resolvers, check out the documentation [here](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-mapping-template-reference-http.html) & the HTTP Resolvers tutorial [here](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-http-resolvers.html).

> *My Name is [Nader Dabit](https://twitter.com/dabit3). I am a Developer Advocate at Amazon Web Services working with projects like [AWS AppSync](https://aws.amazon.com/appsync/) and [AWS Amplify](https://aws-amplify.github.io/). I specialize in cross-platform & cloud-enabled application development.*

</center>