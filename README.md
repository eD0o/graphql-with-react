# 2 - Graph Query Language

## 2.1 - What Is GraphQL?

GraphQL is a `powerful query language and runtime for APIs that lets clients ask for exactly the data they need`, no more, no less. It brings `precision, flexibility, and type safety` to client-server communication, making it a `modern alternative to REST`.

GraphQL is `like TypeScript for APIs`, it defines a `strongly typed schema`, allows precise queries, and guarantees predictable results.

`Instead of hitting multiple REST endpoints (e.g., /users, /posts, /comments), you use one endpoint (usually /graphql)` and describe the data you want:

```graphql
query {
  user(id: "1") {
    name
    email
    posts {
      title
      createdAt
    }
  }
}
```

Response:

```json
{
  "data": {
    "user": {
      "name": "Alice",
      "email": "alice@example.com",
      "posts": [{ "title": "First Post", "createdAt": "2023-01-01" }]
    }
  }
}
```

This `returns exactly what you asked` for no overfetching or underfetching.

### 2.1.1 - Key Features

- Fields: You define `what fields you need` (name, email).
- Nested Objects: Related data can be retrieved in the same query (e.g. a user's posts).
- Arguments: Filter or customize results directly in the query.

```graphql
query {
  post(id: "123") {
    title
    comments(limit: 5) {
      text
      author {
        name
      }
    }
  }
}
```

### 2.1.2 - Type System & Schema

GraphQL APIs are built around a schema that defines types, fields, and relationships. Every query is validated against this schema.

> If you ask for a field that doesn’t exist, the server returns an error before executing anything.

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String
}
```

### 2.1.3 - 🧪 Why GraphQL?

Originally created by Facebook in 2012, GraphQL was `built to improve mobile performance by avoiding overfetching and underfetching from REST APIs`. It was open-sourced in 2015 and is now maintained by a strong community.

Advantages:

- 🚫 No overfetching: request only what you need.
- 🔄 No API versioning: fields evolve gradually.
- 📦 One request, multiple resources: query multiple related objects in a single call.
- 🔒 Type-safe: schema and tooling (like GraphQL Codegen) ensure correctness at compile-time.

### 2.1.4 - 🔁 GraphQL vs REST

| Feature            | REST                                              | GraphQL                                                    |
| ------------------ | ------------------------------------------------- | ---------------------------------------------------------- |
| Endpoints          | Many endpoints (e.g., /users, /posts)             | Single endpoint (/graphql)                                 |
| Data Fetching      | Fixed response shape per endpoint                 | Clients define the shape they want                         |
| Over/Underfetching | Common: you might get too much or too little data | Eliminated: fetch exactly what's needed                    |
| Versioning         | Versioned via endpoints (/v1, /v2)                | Evolves via schema; no versioning needed                   |
| Type System        | Optional or manually documented                   | Strongly typed schema (SDL) enforced at runtime            |
| Caching            | Simple via HTTP status codes and headers          | Complex; needs custom cache logic (e.g., Apollo Client)    |
| Tooling            | Mature (Postman, Insomnia, OpenAPI, etc.)         | Strong introspection, autocompletion, GraphiQL/Playground  |
| File Uploads       | Native with multipart/form-data                   | Requires special handling (graphql-multipart-request-spec) |
| Error Handling     | Simple HTTP status codes                          | Always 200 OK; errors returned inside the JSON body        |
| Learning Curve     | Lower for simple APIs                             | Higher due to schema, types, query language, and tooling   |
| Real-Time Support  | Limited (often uses polling or custom solutions)  | Built-in with subscriptions (WebSockets)                   |
| Batching           | Requires manual optimization                      | Query multiple resources in one request                    |
| Security           | Easier to manage per-endpoint and method          | More complex; needs query cost limiting and depth analysis |

### 2.1.5 - When to Use GraphQL vs REST

Use GraphQL when:

- You want flexible data fetching (clients need control over what data they get)
- The client needs to query multiple related resources in one request
- You're building a modern frontend/mobile app with dynamic data needs
- You need to support rapid iterations without versioning
- You want to take advantage of strong typing and introspection
- Your API consumers vary (e.g. web, mobile, partners)

Example use cases:

- Newsfeed apps (e.g. Twitter clone)
- Dashboards with customizable UI
- SaaS platforms with multiple client apps

---

Use REST when:

- You want simplicity and familiarity
- Your data structures are well-defined and stable
- Caching is important and HTTP cache can help
- You need file uploads or streaming
- You have strict security/firewall requirements
- Your consumers are mostly internal and need quick-to-implement endpoints

Example use cases:

- CRUD APIs for admin tools
- Backend-for-frontend systems where you control both ends
- Legacy systems or microservices with stable, reusable endpoints

## 2.2 - GraphQL Query with fetch

GraphQL simplifies API structure by using a single endpoint (commonly /graphql) to handle all queries and mutations. Unlike REST, where different resources have separate endpoints, GraphQL encodes data requirements in the query body, not in the URL path, which is especially useful for relational data.

### 2.2.1 - All Requests Are POST (Even Queries)

- In practice, `GraphQL uses POST requests for everything, including simple queries`. (GET is possible but not recommended.)
- The query is passed in the body of the request as a JSON object:

  ```json
  { "query": "your GraphQL query as a string" }
  ```

### 2.2.2 - Making a GraphQL Request with fetch

Since a GraphQL query is just a string, `you can use standard tools like fetch, no special client required`:

```js
const url = "http://your-graphql-endpoint.com/graphql";
const query = `
  {
    user(id: "1") {
      name
      email
      posts {
        title
        content
      }
    }
  }
`;

fetch(url, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Accept: "application/json",
  },
  body: JSON.stringify({ query }),
})
  .then((response) => response.json())
  .then((data) => console.log("Data:", data.data))
  .catch((error) => console.error("Error:", error));
```

You can even send anonymous queries, they don’t require a name.

### 2.2.3 - When to use fetch vs a GraphQL Client

When to use only fetch

Best for:

- Simple applications
- Learning, testing, or prototyping
- Lightweight needs with minimal dependencies

Pros:

- Minimal bundle size (no extra libraries)
- Full control over request structure, headers, retries, etc.
- Good for quick testing in devtools or playgrounds

Cons:

- No built-in support for:

  - Caching
  - Automatic error handling
  - Query deduplication
  - Request batching

- You need to manually:

  - Handle loading/error states
  - Manage headers, variables, retries
  - Parse responses and errors
  - Reuse and manage queries
  - Write your own caching logic (if needed)

---

When to use a GraphQL Client

Best for:

- Mid to large applications
- Reusable data fetching logic
- Apps that require performance optimizations like caching or batching
- Apps that need authentication, optimistic UI, devtools, or SSR support

Pros:

- Caching and normalization out of the box
- Smart loading/error state management
- Polling, pagination, and subscriptions
- Built-in TypeScript support with codegen
- Devtools for inspecting cache, queries, and mutations

Cons:

- Heavier bundle size
- Requires setup and learning curve
- May feel like overkill for simple apps or MVPs

---

Middle Ground Options

- Use fetch + a lightweight wrapper (e.g., graphql-request)

  - ✅ Small, easier than raw fetch
  - ❌ Still lacks advanced features like cache

- Use a custom React hook (like useFetchGraphQL) to abstract the boilerplate.

#### Decision Table

| App Complexity                 | Suggested Approach       |
| ------------------------------ | ------------------------ |
| Static site / MVP              | fetch or graphql-request |
| Small app (few queries)        | fetch + custom hooks     |
| Mid-size React app             | Apollo Client / URQL     |
| Needs caching or SSR           | GraphQL client           |
| Mobile app (e.g. React Native) | Apollo Client or Relay   |

### 2.2.4 - Error Handling in GraphQL

GraphQL behaves differently from REST in how it handles errors:

| REST                    | GraphQL                         |
| ----------------------- | ------------------------------- |
| Uses HTTP status codes  | Always returns 200 OK           |
| 4xx/5xx for errors      | Errors included in JSON payload |
| Error = response.status | Error = response.errors field   |

- `Even server errors return a 200 HTTP status`.
- You must check the presence of the errors field in the response:

  ```js
  if (data.errors) {
    // handle errors
  }
  ```

This means browser tools like DevTools won’t show red network errors, which can confuse debugging. It’s your responsibility (or your GraphQL client’s) to detect and handle those errors from the response body.
