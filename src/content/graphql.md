---
title: Graphql
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - IT、Web 
toc: true
thumbnail: 
---

​		我曾经在公司层面进行了不止一年的基于Graphql的前后端开发，因为积累了一些关于Graphql的相关

<!--more-->

## GraphQL

Apollo是基于GraphQL的全栈解决方案集合，包括了apollo-client和apollo-server，从后端到前端提供了对应的lib使得开发GraphQL更加方便

```toml
apollo-boost 包含启动阿波罗客户端的所有依赖
react-apollo 视图层面的集合
graph-tag 解析查询语句
graphql 也是解析查询语句
```

### fetch-policy

像大多数前端开发工具一样，[Apollo Client](https://link.zhihu.com/?target=https%3A//www.apollographql.com/client/) 是非常灵活的，比如 Apollo Cache 就是其中一个。Apollo Client 为浏览器提供了获取数据的缓存设置，避免不必要的网络请求，来提升应用的性能。

在使用 Apolllo Query 时，数据是从缓存或者服务端接口中获取，取决于 [fetch policy](https://link.zhihu.com/?target=https%3A//www.apollographql.com/docs/react/api/react-apollo.html%23graphql-config-options-fetchPolicy) 的设置。fetch policy 表示获取数据的优先级，比如是从服务端拉取最新的数据，还是从缓存中快速读取到数据。理解 fetch policy，有助于更清晰地理解 Apollo GraphQL 应用的数据流，解决一些获取数据时的异常。

cache-first:

Apollo 默认的 fetch policy 是 cache-first（缓存优先），与获取最新数据相比，这种方式会快速获取到数据。如果你不想数据发生变化或者对数据实时性要求不高的情况下，可以使用缓存优先：

1. 获取数据时，Apollo 会从 Apollo Cache 数据中检查是否命中缓存，如果命中则直接跳到 step 4。
2. 如果没有命中，Apollo 则会发起一个网络请求到服务端。
3. 服务端返回数据，Apollo 使用该数据去更新缓存。
4. 返回数据。

cache-and-network:

如果我们需要要显示经常更新的数据时，这是一个很好的 fetch policy。cache first 强调的是快速获取数据，而 cache-and-network 则侧重于让缓存数据跟服务端一样保持最新。如果对数据进行了修改，但是又担心缓存过期时，这个策略会是一个很好的解决方案。

1. 获取数据时，Apollo 会从 Apollo Cache 数据中检查是否命中缓存。
2. 如果命中缓存，则返回。
3. 无论 step 2 中是否获取到数据，Apollo 都会发一个请求去服务端获取最新数据。
4. 更新缓存里的数据。
5. 返回数据。

Network-only

如果不想因为显示过期的数据带来的风险时，使用 network-only 会更加合理，这个策略比如快速获取数据，更倾向于保证数据的实时性。不像 cache-and-network，该策略不会从缓存返回可能过期的数据，同时它又能保证缓存的数据是最新的。

1. Apollo 直接发起一个请求，而不会检查缓存。
2. 服务端返回数据，并更新缓存数据。
3. 返回数据。

no-cache

no-cache 有点类似 network-only，但是它跳过了缓存数据的更新。如果你不想在缓存中存储任何信息时，它会非常合适。

1. Apollo 直接发起一个请求，而不会检查缓存。
2. 服务端返回数据，但不会写入缓存中。

Cache-only

跟 no-cache 恰恰相反，这个策略会避免发起网络请求，但是如果获取数据没在缓存中的话，就会抛出一个错误。如果需要给用户一直显示同个数据而忽略服务端的变化时，或者在离线访问时，这个策略就非常有用了。

1. Apollo 从缓存获取数据。
2. 如果获取的数据在缓存的话，则返回，否则会抛出一个错误。

你既可以为整个应用设置 fetch policy，也可以单独为某个 query 设置，至于使用哪种策略，还需要根据实际的架构而定。

## UI

使用apollo-boost

```react
import ApolloClient from 'apollo-boost' 

const client = new ApolloClient({
    uri: 'http://localhost:5000/graphql'
})

import { ApolloProvider,Query } from 'react-apollo'
import { Mutation,MutationFunc } from 'react-apollo'

```

### @apollo/client

安装preset包

```shell
# installing the preset package 
npm install apollo-boost graphql-tag graphql --save
```

安装@apollo/client

```shell
# installing each piece independently 
npm install apollo-client apollo-cache-inmemory apollo-link-http graphql-tag graphql --save
```

使用@apollo/client

```react
import ApolloClient from 'apollo-boost';
 
const client = new ApolloClient();

import React, { ReactElement } from 'react';
import {useQuery, gql } from '@apollo/client';

const GET_AUTHOR = gql`
	query Author($id: Int!) {
		author(id: $id) {
			id
			firstName
			lastName
			posts {
				title
				author
			}
		}
	}
`

export default function Home({}): ReactElement {
  const {data, loading, refetch } = useQuery(GET_AUTHOR, { variables: {id: 1}});
  return (
  	<div>home</div>
  )
}
```

### 订阅

```javascript
import { split } from 'apollo-link';
import { HttpLink } from 'apollo-link-http';
import { WebSocketLink } from 'apollo-link-ws';
import { getMainDefinition } from 'apollo-utilities';

// Create an http link:
const httpLink = new HttpLink({
  uri: 'http://localhost:3000/graphql'
});

// Create a WebSocket link:
const wsLink = new WebSocketLink({
  uri: `ws://localhost:5000/`,
  options: {
    reconnect: true
  }
});

// using the ability to split links, you can send data to each link
// depending on what kind of operation is being sent
const link = split(
  // split based on operation type
  ({ query }) => {
    const definition = getMainDefinition(query);
    return (
      definition.kind === 'OperationDefinition' &&
      definition.operation === 'subscription'
    );
  },
  wsLink,
  httpLink,
);
```



### graphql-request

node端可以使用graphql-request请求

```shell
npm add graphql-request grapghql
```

使用

```react
import { request, gql } from 'graphql-request'

const query = gql`
  {
    company {
      ceo
    }
    roadster {
      apoapsis_au
    }
  }
`

request('https://api.spacex.land/graphql/', query).then((data) => console.log(data))

import { request, GraphQLClient } from 'graphql-request'

// Run GraphQL queries/mutations using a static function
request(endpoint, query, variables).then((data) => console.log(data))

// ... or create a GraphQL client instance to send requests
const client = new GraphQLClient(endpoint, { headers: {} })
client.request(query, variables).then((data) => console.log(data))
```

### graphql-code-generate

使用graphql-code-generate生成hooks代码

安装graphql-code-generator命令行工具

```shell
yarn add graphql
yarn add -D @graphql-codegen/cli
```

根据实际项目情况安装生成hooks的plugins

```shell
yarn add @graphql-codegen/typescript-react-apollo
yarn add @graphql-codegen/typescript
yarn add @graphql-codegen/typescript-operations
```

然后首先编写graphql文件

```graphql
mutation CreateAuthor($author: authorInput) {
	createAuthor(author: $author)
}

query Author($id: Int!) {
	author(id: $id) {
		id
		firstName
		lastName
		posts {
			title
			author
		}
	}
}
```

再编写codegen的config文件

```yaml
overwrite: trues
schema: ./schema.gql
documents: 'scr/**/*.graphql'
generates:
	src/generated/graphql.tsx:
		plugins:
			- 'typescript'
			- 'typescript-operations'
			- 'typescript-react-apollo'
		hooks:
			afterOneFileWrite:
				- prettier --write
	schema.graphql:
		plugins:
			- 'schema-ast'
		hooks:
			 afterOneFileWrite:
				- prettier --write
```

**`overwrite`** - 生成代码时覆盖文件的标志（`true`默认情况下）

 **`schema`（必需）** - 指向`GraphQLSchema`，可以通过本地文件路径、url等多种方式

 **`documents`** - 指向你的`GraphQL`文档：`query、 mutation、subscription、fragment` 

**`generates`（必需）** - 一个映射，其中键表示生成代码的输出路径，值表示该特定文件的一组相关选项：

配置package.json

```json
{
  "scripts": {
    "codegen": "graphql-codegen"
  },
}
```

### apollo-upload-client

graphql上传接口

```shell
npm install apollo-upload-client
```

使用

```react
import { gql, useMutation } from "@apollo/client";

const MUTATION = gql`
  mutation ($file: Upload!) {
    uploadFile(file: $file) {
      success
    }
  }
`;

function UploadFile() {
  const [mutate] = useMutation(MUTATION);

  function onChange({
    target: {
      validity,
      files: [file],
    },
  }) {
    if (validity.valid) mutate({ variables: { file } });
  }

  return <input type="file" required onChange={onChange} />;
}
```

@graphql-ws

graphql的websocket客户端

```javascript
import { GraphQLWsLink } from '@apollo/client/link/subscriptions';
import { createClient } from 'graphql-ws';

const wsLink = new GraphQLWsLink(createClient({
  url: 'ws://localhost:4000/subscriptions',
  connectionParams: {
    authToken: user.authToken,
  },
}));
```

React-apollo

react中提供graphql上下文的组件

```typescript
import { ApolloProvider } from 'react-apollo';
import { ApolloLink } from 'apollo-link';
import ApolloClient from 'apollo-client';

const App = () => {
  client = new ApolloClient({
      link: ApolloLink.from([onError(() => {}), new SchemaLink({ schema })]),
      cache: cache || globalCache || new InMemoryCache(),
    });
  
	return <ApolloProvider client={client}>
    {children}
  </ApolloProvider>;
}
```



### graph-tools



### urql

https://formidable.com/open-source/urql/docs/

graphQL客户端，支持Vue、React

```shell
yarn add urql
# or
npm install --save urql
```

使用

```react
import { Client, Provider, cacheExchange, fetchExchange } from 'urql';

const client = new Client({
  url: 'http://localhost:3000/graphql',
  exchanges: [cacheExchange, fetchExchange],
});

const App = () => (
  <Provider value={client}>
    <YourRoutes />
  </Provider>
);
```

组件使用

```react
import { gql, useQuery } from 'urql';

const TodosQuery = gql`
  query {
    todos {
      id
      title
    }
  }
`;

const Todos = () => {
  const [result, reexecuteQuery] = useQuery({
    query: TodosQuery,
  });

  const { data, fetching, error } = result;

  if (fetching) return <p>Loading...</p>;
  if (error) return <p>Oh no... {error.message}</p>;

  return (
    <ul>
      {data.todos.map(todo => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
};
```

### gal.tada

生成graphql ts类型，

安装

```shell
npm install gql.tada
npm install --save-dev @0no-co/graphqlsp
```

在tsconfig中添加插件

```json
{
  "compilerOptions": {
    "strict": true,
    "plugins": [
      {
        "name": "@0no-co/graphqlsp",
        "schema": "./schema.graphql",
        "tadaOutputLocation": "./src/graphql-env.d.ts"
      }
    ]
  }
}
```

使用

```typescript
import { graphql } from 'gql.tada';

const TodosQuery = graphql(`
  query Todos ($limit: Int = 10) {
    todos(limit: $limit) {
      id
      title
      completed
    }
  }
`);
```





## Server

### apollo-server

安装依赖

```js
npm install apollo-server@2.13.1 graphql@14.6.0  type-graphql@0.17.6
```

引入

```js
import "reflect-metadata"
import {buildSchema,ObjectType,Field,ID,Resolver,Query} from "type-graphql";
import {ApolloServer} from "apollo-server";
```

后端定义schema和resolver

```js
@ObjectType()
class Post{
    @Field(type => ID)
    id: string;

    @Field()
    created: Data;

    @Field()
    content: String;
}

@Resolver(Post)
class PostResolver {
    @Query(returns => [Post])
    async posts(): Promise<Post[]>{
        return [
           {
              id:"0",
              created: new Date(),
              content:'aaa'
            },
            {
              id:"1",
              created: new Date(),
              content:'bbb'
            },
            {
              id:"2",
              created: new Date(),
              content:'ccc'
            },
        ]
    }
}
```

运行项目，在`localhost:4444`打开graphql的playground进行测试

### grapnel-yoga

基于graphql的后端框架

安装

```shell
pnpm add graphql-yoga graphql
```

使用

```javascript
import { createSchema, createYoga } from 'graphql-yoga'
import { createServer } from 'node:http'

const yoga = createYoga({
  schema: createSchema({
    typeDefs: `
      type Query {
        hello: String
      }
    `,
    resolvers: {
      Query: {
        hello: () => 'Hello from Yoga!'
      }
    }
  })
})

const server = createServer(yoga)

server.listen(4000, () => {
  console.info('Server is running on http://localhost:4000/graphql')
})
```



### nexus

类型安全的graph schema

安装

```shell
npm install nexus graphql
```

使用

```javascript
import { queryType, stringArg, makeSchema } from 'nexus'
import { GraphQLServer } from 'graphql-yoga'

const Query = queryType({
  definition(t) {
    t.string('hello', {
      args: { name: stringArg() },
      resolve: (parent, { name }) => `Hello ${name || 'World'}!`,
    })
  },
})

const schema = makeSchema({
  types: [Query],
  outputs: {
    schema: __dirname + '/generated/schema.graphql',
    typegen: __dirname + '/generated/typings.ts',
  },
})

const server = new GraphQLServer({
  schema,
})

server.start(() => `Server is running on http://localhost:4000`)
```

Nexus-prisma

基于prisma的model生成nexus的schema

安装

```shell
npm add nexus-prisma nexus graphql @prisma/client
```

使用

```react
import { User } from 'nexus-prisma'
import { makeSchema, objectType } from 'nexus'
 
export const schema = makeSchema({
  types: [
    objectType({
      name: User.$name
      description: User.$description
      definition(t) {
        t.field(User.id)
     // t.field(User.id.name, User.id)    <-- For nexus@=<1.0 users
      }
    })
  ]
})
```



### graphql-upload

在koa和express框架中使用graphql的upload接口

安装

```shell
npm install graphql-upload graphql
```

使用

```javascript
import graphqlUploadKoa from "graphql-upload";

app.use(
  graphqlUploadKoa({
    // Limits here should be stricter than config for surrounding infrastructure
    // such as NGINX so errors can be handled elegantly by `graphql-upload`.
    maxFileSize: 10000000, // 10 MB
    maxFiles: 20,
  })
);
```

### @graphql-tools

graphql的内部包

https://the-guild.dev/graphql/tools/docs/resolvers-composition



### graphql-subscriptions

node端graphql发布订阅

安装

```shell
npm install graphql-subscriptions graphql
```

使用

```javascript
import { PubSub } from 'graphql-subscriptions';

export const pubsub = new PubSub();

const SOMETHING_CHANGED_TOPIC = 'something_changed';

export const resolvers = {
  Subscription: {
    somethingChanged: {
      subscribe: () => pubsub.asyncIterator(SOMETHING_CHANGED_TOPIC),
    },
  },
}

pubsub.publish(SOMETHING_CHANGED_TOPIC, { somethingChanged: { id: "123" }});
```

### graphql-shield

graphql做auth验证层的中间件

使用

```javascript
import { shield, rule, allow } from 'graphql-shield';

const isAuthenticated = rule()((parent, args, ctx) => {
  if (ctx.userId) {
    return true;
  }
  return new ServerError({
    code: ServerErrorCode.LOGIN_FAILED,
    msg: `no user matched`,
  });
});

const authMiddleware = shield({
  Query: {
    users: allow,
  },
  Mutation: {
    admin: allow
  },
});
```

### graphql-middleware

个性化的graphql中间件

https://github.com/dimatill/graphql-middleware



### graphql-depth-limit

限制graphql查询层数

```shell
npm install graphql-depth-limit
```

可以配合express-graphql和koa-graphql使用

```javascript
import depthLimit from 'graphql-depth-limit'
import express from 'express'
import graphqlHTTP from 'express-graphql'
import schema from './schema'
 
const app = express()
 
app.use('/graphql', graphqlHTTP((req, res) => ({
  schema,
  validationRules: [ depthLimit(10) ]
})))
```

### graphql-voyager

将graphql的schema以图形化的形式展示

https://github.com/graphql-kit/graphql-voyager



### graphql-engine

graphql查询上云

https://github.com/hasura/graphql-engine



### graphurl

graphql请求数据cli

安装

```shell
npm install -g graphqurl
```

使用

```shell
gq https://my-graphql-endpoint/graphql \
     -H 'Authorization: Bearer <token>' \
     -q 'query { table { column } }'
```



### join-monster

将graphql转换为sql

https://github.com/join-monster/join-monster



### SOFA

将graphql的api转换为restful的API

安装

```shell
yarn add sofa-api
# or
npm install sofa-api
```

使用

```javascript
import { useSofa } from 'sofa-api';
import express from 'express';

const app = express();

app.use(
  '/api',
  useSofa({
    basePath: '/api',
    schema,
  })
);

// GET /api/users
// GET /api/messages
```

### 内省查询

内省查询就是指graphql的playground查询docs和schema，也可以显示地进行内省查询。



### Graphql漏洞

https://half90.top/2022/07/13/graphql-gong-ji-mian-zong-jie/#toc-heading-19



### postgraphile

基于***PostgreSQL***一个命令生成GraphQL API

```shell
npx postgraphile -c 'postgres://user:pass@localhost/mydb' --watch --enhance-graphiql --dynamic-json
```



### 

