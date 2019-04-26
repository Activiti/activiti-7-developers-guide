# Activiti Cloud Notification Service

In order to provide a scalable notification service, we evaluated GraphQL and the integration with messaging systems such as RabbitMQ to provide reliable Subscription based mechanism where users can register interest in certain events emitted by the core building blocks. For now this endpoint is available as ACTIVITI\_ADMIN role only.

## GraphQL Query Endpoint

The GraphQL Query API provides single entry GraphQL query endpoint at `/graphql` URI with support for expressive process entity schema and type-safe query criteria expressions in order to execute flexible, fast and declarative data queries for consumer applications.

### Supported HTTP Methods, Headers, and Body

Activiti GraphQL Rest Controller handles both HTTP GET and POST method requests.

#### GET requests

When receiving an HTTP GET request, the GraphQL query should be specified in the `query` parameter. For example, if we wanted to execute the following GraphQL query:

```text
{
  ProcessInstance(processInstanceId:"1") {
    processInstanceId
    tasks {
      id
      name
    }
  }
}
```

This request could be sent via an HTTP GET like so:

`http://host/graphql?query={ProcessInstance(processInstanceId:"1"){processInstanceId,tasks{id,name}}}`

Query variables can be sent as a JSON-encoded string in an additional query parameter called `variables`.

#### POST requests

A standard GraphQL POST request uses the application/json content type, and includes a JSON-encoded body of the following form:

```text
{
  "query": "query($id: String) { ProcessInstance(processInstanceId: $id) { processInstanceId, tasks { id, name } } }",
  "variables": { "id": "value" }
}
```

The field `variables` is optional.

In addition to the above, `ActivitiQraphQLController` supports the following cases:

* If the `application/x-www-form-urlencoded` Content-Type header is present in the POST request, the GraphQL query should be specified in the `query` parameter string. Query variables can be sent as a JSON-encoded string in an additional query parameter called `variables`.
* If the "application/graphql" Content-Type header is present, treat the HTTP POST body contents as the GraphQL query string.

#### GraphQL Query Response

Regardless of the method by which the query and variables were sent, the response is returned in the body of the request in JSON format.

```text
{
  "data": {
    "ProcessInstance": {
      "processInstanceId": "1",
      "tasks": [
        {
          "id": "4",
          "name": "task4",
          "assignee": "assignee",
          "status": "COMPLETED",
        },
        {
          "id": "5",
          "name": "task5",
          "assignee": "assignee",
          "status": "COMPLETED",
        }
      ]
    }
  },
  "errors": null
}
```

If there were no errors returned, the "errors" field will be null in the response object.

### GraphQL Query Schema

Activiti GraphQL query endpoint provides schema descriptions derived from JPA entity model at runtime for the following entities: `ProcessInstance`, `Task`, `ProcessVariable` and `TaskVariable`. The schema also derives GraphQL scalar types from JPA entity model attributes to validate provided variable values against the schema.

Each entity in the query model is wrapped into two GraphQL query fields, i.e. ProcessInstance entity will have two representations in the GraphQL schema:

* One that models the entity directly using singular form, i.e. `ProcessInstance` or `Task` to query single entity instance by `id` argument.
* One that wraps the entity in a pagable query request with `where` criteria expression using Entity pluralized form, i.e. `ProcessInstances` or `Tasks`

For example, you can use singular query wrapper, if you need a single object as root of your query:

```text
query {
  ProcessInstance(processInstanceId:"1") {
    processInstanceId
    tasks {
      id
      name
      assignee
      status
    }
  }
}
```

Will return:

```text
{
  "ProcessInstance": {
    "processInstanceId": "1",
    "tasks": [
      {
        "id": "4",
        "name": "task4",
        "assignee": "assignee",
        "status": "ASSIGNED"
      },
      {
        "id": "5",
        "name": "task5",
        "assignee": "assignee",
        "status": "COMPLETED"
      }
    ]
  }
}
```

### GraphQL Pluralized Query Wrapper with Where Criteria Expressions

This GraphtQL schema supports flexible type safe criteria expressions with familiar SQL query syntax semantics using `where` arguments int the `select` query field to use any combination of logical expressions like `OR`, `AND`, `EQ`, `NE`, `GT`, `GE`, `LT`, `LR`, `IN`, `NIN`, `IS_NULL`, `NOT_NULL`, `BETWEEN`, `NOT_BETWEEN` provided by SQL query language.

For example, the following query will find all running process instances with active tasks:

```text
query {
  ProcessInstances(where: {
    status: { IN: RUNNING }
  }) {
    select {
      processInstanceId
      status
      tasks(where: {status: {IN: [CREATED,ASSIGNED] }}) {
        id
        name
        assignee
        status
      }
    }
  }
}
```

Will return

```text
{
  "ProcessInstances": {
    "select": [
      {
        "processInstanceId": "0",
        "status": "RUNNING",
        "tasks": [
          {
            "id": "1",
            "name": "task1",
            "assignee": "assignee",
            "status": "ASSIGNED"
          }
        ]
      },
      {
        "processInstanceId": "1",
        "status": "RUNNING",
        "tasks": [
          {
            "id": "5",
            "name": "task5",
            "assignee": "assignee",
            "status": "ASSIGNED"
          }
        ]
      }
    ]
  }
}
```

### GraphQL Reverse Query

You can execute an inverse query to fitler results with a join in many-to-one association.

For Example:

```text
query {
  Tasks {
    select {
      name
      assignee
      status
      processInstance(where: {processInstanceId: {EQ: "1"}}) {
        processInstanceId
        status
      }
      variables {
        name
        type
        value
      }
    }
  }
}
```

Will return result:

```text
{
  "Tasks": {
    "select": [
      {
        "name": "task4",
        "assignee": "assignee",
        "status": "Running",
        "processInstance": {
          "processInstanceId": "1",
          "status": "RUNNING"
        },
        "variables": [
          {
            "name": "variable5",
            "type": "String",
            "value": "value5"
          },
          {
            "name": "variable6",
            "type": "String",
            "value": "value6"
          }
        ]
      },
      {
        "name": "task5",
        "assignee": "assignee",
        "status": "COMPLETED",
        "processInstance": {
          "processInstanceId": 1,
          "status": "RUNNING"
        },
        "variables": []
      }
    ]
  }
}
```

### GraphQL Paged Queries

Use plural query wrapper with Where Criteria Expressions to run complex queries with paged collection results and request total records and pages counts:

```text
query {
  ProcessInstances(page: {start:1, limit:1}) {
    pages
    total
    select {
      processInstanceId
      tasks {
        id
        name
        assignee
        status
      }
    }
  }
}
```

The result will be:

```text
{
  "ProcessInstances": {
    "pages": 2,
    "total": 2,
    "select": [
      {
        "processInstanceId": 0,
        "tasks": [
          {
            "id": "2",
            "name": "task2",
            "assignee": "assignee",
            "status": "RUNNING"
          },
          {
            "id": "3",
            "name": "task3",
            "assignee": "assignee",
            "status": "RUNNING"
          },
          {
            "id": "1",
            "name": "task1",
            "assignee": "assignee",
            "status": "COMPLETED"
          }
        ]
      }
    ]
  }
}
```

To optimize query performance, the GraphQL Query DataFetcher implementation will execute an extra query to get the total elements only if you have requested 'pages' or 'total' fields.

### GraphQL Query Sorting

Sorting is supported on any field. Simply pass in an 'orderBy' argument with the value of ASC or DESC in the query field attribute. Here's an example of sorting by name for Task objects. If sort order is not specified and there is no field with default sort order provided, query data fetcher use the identity field for default sorting in order to avoid paging confusions.

For Example:

```text
query {
  Tasks {
    select {
      id
      name(orderBy:DESC)
      assignee
      status
    }
  }
}
```

Will Return:

```text
{
  "Tasks": {
    "select": [
      {
        "id": "5",
        "name": "task5",
        "assignee": "assignee",
        "status": "Completed"
      },
      {
        "id": "4",
        "name": "task4",
        "assignee": "assignee",
        "status": "Running"
      },
      {
        "id": "3",
        "name": "task3",
        "assignee": "assignee",
        "status": "Running"
      },
      {
        "id": "2",
        "name": "task2",
        "assignee": "assignee",
        "status": "Running"
      },
      {
        "id": "1",
        "name": "task1",
        "assignee": "assignee",
        "status": "Completed"
      }
    ]
  }
}
```

### Variable Parameter Bindings

Just like a REST API, it is possible to pass arguments to an endpoint in a GraphQL API. By declaring the arguments in the query defintion, typechecking happens automatically. Each argument must be named with `$` prefix, have a type. To use variable, simply reference it in any criteria expressions inside query statements. Each argument's value will be resolved during query execution pipeline.

```text
{
   "query": "query($status: String!) {
      ProcessInstances(where: {
        OR: {
          status: { EQ: $status}
        }
      }) {
        select {
          processInstanceId
          status
          tasks(where: {status: {EQ: COMPLETED}}) {
            id
            name
            assignee
            status
          }
        }
      }
    }
  ",
   "variables":{"status":"RUNNING"}
}
```

### GraphQL Query Performance

The Activiti GraphQL Data Fetcher implementation will build dynamic JPA fetch graph in order to minimize the number of queries executed against database in order to avoid 1+N lazy loading problems.

### How to demo Activiti GraphQL Query API

The GraphiQL app browser can be used for simple testing. It provides schema documentation browser and query builder with auto-completion support, as well as parameter bindings.

Then, navigate to [http://host/notifications/graphiql](http://host/notifications/graphiql) to load GraphiQL browser. Use `hradmin` or `testadmin` user to login. 

The collapsed Docs panel can opened by clicking on the button in the upper right corner to expose current test schema models.

You can run GraphQL queries in the left pannel. Type the query and hit the run button. The results should come up in the middle panel. If your query has variables, there is a minimized panel at the bottom left. Simply click on this to expand, and type in your variables as a JSON string with quoted keys.



