# Grapql

A representation of data with a collection of nodes and edges

## Fundamental

**How a query work?**

Here are the three high-level steps the server takes to respond to the query:

- Parse the query

The server parses the string and turns it into an AST — an abstract syntax tree. If there are any syntax errors, the server will stop execution and return the syntax error to the client.

- Validate

Make sure the query is valid given the schema

- Excute

GraphQL query like populate in mongoose. It calls the resolver function of the fields at the top level. It waits until all these resolve function return value, and then pass the return value to next resolve function to proceed (u can get this return value by the first param "parent" of a resolve function)

### Queries and Mutations

#### Aliases

- if u want to query for the same field with different arguments

```js
{
  guests: users(role: GUEST) {
    name
  }
  admins: users(role: ADMIN) {
    name
  }
}
==> {
      "data": {
        "guests": [{
          "name": "Luke Skywalker"
        }],
        "admins": [{
          "name": "admin1"
        }]
      }
    }
```

## Apollo server

### Schema

It defines what and how clients can query or mutate data

**Types**: Scala(Int, String), Object

### Resolver

**`Resolver arguments`**

|         |                                                           |
| ------- | --------------------------------------------------------- |
| parent  | the return value of the previous resolver                 |
| args    | GraphQL args                                              |
| context | object shared across all resolvers                        |
| info    | info of operation's execution state (field name, path...) |

**`Default resolver`**

If u dont define a resolver for a schema field, Apollo server defines a default resolver that get value from the parent argument

```js
query ExampleQuery($username: String!) {
  user(username: $username) {
    username
    email
    account {
      role
    }
  }
}
resolverGetUser(parent, { username }) {
  return { ...user, account: { role: 'default' } };
}
==> {
      "data": {
        "user": {
          "username": "sa",
          "email": "tiennhot8@gmail.com",
          "account": {
            "role": "default"
          }
        }
      }
    }
```

### Error handling

Apollo Server uses different HTTP status codes in various situations:

- 500: Error during processing, startup, functioin throws,...
- 400: Error when parse request into legal Graphql document and validate it against schema
- 405: Invalid HTTP method (GET with a mutation, or any HTTP method other than GET or POST)

### Questions

- GraphQL always use POST request even when we are trying to fetch?

> Depending on your graph's schema, the size of a valid query string might be very large, so if u put it in url it can hit 414 URI Too Long. That's why it put query to body

## Compare to REST

- GraphQL queries let client fetch lots of relate data in one request
- Every field can get its own set of args

```js
{
  human(id: "1000") {
    name
    // height
    height(unit: FOOT)
  }
}
==> {
      "data": {
        "human": {
          "name": "Luke Skywalker",
          // "height": 1.72 in meter unit
          "height": 5.6430448 // in foot unit
        }
      }
    }
```

## Packages

### DataLoader

[An easy to understand explaination](https://www.youtube.com/watch?v=ld2_AS4l19g)

- A batch loading function accepts an Array of keys, and returns a Promise which resolves to an Array of values
- The function excutes when the loader call .load() and the result value is cached be default

```js
async function batchLoadFn(ids) {
  const users = await db.find(id: {$in: ids})
  return ids.map(id => users.getUserById(id) || 'none')
}
const userLoader = new DataLoader(batchLoadFn)
```

- By default DataLoader will group all invidual loads occurs within a single tick of the event loop an pass to load function

```js
const [user1, user] = await userLoader.loadMany([1, 2]);
//or
const user1 = userLoader.load(1); // promise
const user2 = userLoader.load(2); // promise
// => load function will take [1, 2] (batchLoadFn([1, 2])) and call only 1 request to db
// ===

const user1 = await userLoader.load(1);
const user2 = await userLoader.load(2);
const user3 = await userLoader.load(1); // loader will get the value has been cached before, so no need to request to db
// => it isnt in the single tick so it load function executes 2 time and call 2 request to db to get user(1) and user(2) sequentially
```

- Array of values must be the same length as the Array of keys, after fetching values it will store each key corresponding to the value in the same index
- U should clear cache in some cases like loaded data is mutated or catch error when fetching, etc

> DataLoader is used as a per-request cache, so it use an Map as a memorization cache
> Or u can provide a custom Cache instance, as long as it follows the same API of Map
