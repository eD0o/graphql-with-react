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
