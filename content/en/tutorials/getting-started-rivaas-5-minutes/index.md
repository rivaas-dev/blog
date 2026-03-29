---
title: "Getting Started with Rivaas in 5 Minutes"
date: 2026-03-07
description: "Build your first production-ready Go REST API with OpenAPI docs, health probes, and Swagger UI in under 5 minutes."
author: "Rivaas Team"
tags: [getting-started, tutorial, go]
keywords:
  - go api framework tutorial
  - golang rest api
  - rivaas getting started
  - go web framework tutorial
sitemap:
  priority: 0.8
---

Most Go web frameworks make you choose between simplicity and production features. With Rivaas, you get both. In this tutorial, you'll build a REST API with automatic OpenAPI documentation, health probes, and interactive Swagger UI — all in under 5 minutes.

## Prerequisites

- Go 1.25 or later
- A terminal and your favorite editor

## Step 1: Create Your Project

```bash
mkdir rivaas-quickstart && cd rivaas-quickstart
go mod init example.com/quickstart
go get rivaas.dev/app
go get rivaas.dev/openapi
```

## Step 2: Write Your First Handler

Create `main.go`:

```go
package main

import (
    "context"
    "log"
    "net/http"

    "rivaas.dev/app"
    "rivaas.dev/openapi"
)

func main() {
    a, err := app.New(
        app.WithHealthEndpoints(),
        app.WithOpenAPI(
            openapi.WithTitle("Quickstart API", "1.0.0"),
        ),
    )
    if err != nil {
        log.Fatal(err)
    }

    a.GET("/hello", func(c *app.Context) {
        c.JSON(http.StatusOK, map[string]string{
            "message": "Hello from Rivaas!",
        })
    })

    if err := a.Start(context.Background()); err != nil {
        log.Fatal(err)
    }
}
```

## Step 3: Run It

```bash
go run main.go
```

Your API is now running on `http://localhost:8080` with several endpoints ready out of the box.

## Step 4: Explore the Built-in Endpoints

Rivaas gives you production-ready endpoints without any extra configuration:

| Endpoint       | What It Does                               |
| -------------- | ------------------------------------------ |
| `/hello`       | Your handler                               |
| `/livez`       | Liveness probe (returns 200)               |
| `/readyz`      | Readiness probe (returns 204)              |
| `/openapi.json`| Auto-generated OpenAPI 3.1 specification   |
| `/docs`        | Interactive Swagger UI                     |

Try them:

```bash
curl http://localhost:8080/hello
curl http://localhost:8080/livez
curl http://localhost:8080/openapi.json
```

Open `http://localhost:8080/docs` in your browser to see the interactive API documentation — generated automatically from your registered routes and Go types.

## Step 5: Add Request Binding

Let's make the API more interesting with typed request handling. Add this inside `main()`, after the `GET` handler:

```go
type CreateUserRequest struct {
    Name  string `json:"name"  validate:"required"`
    Email string `json:"email" validate:"required,email"`
}

a.POST("/users", func(c *app.Context) {
    var req CreateUserRequest
    if err := c.Bind(&req); err != nil {
        return
    }

    c.JSON(http.StatusCreated, map[string]string{
        "message": "User created",
        "name":    req.Name,
    })
})
```

Rivaas automatically validates the request body, returns structured error messages, and updates the OpenAPI spec to include the new endpoint.

## What You Get Out of the Box

With the options used above, your Rivaas application includes:

- **Liveness and readiness probes** at `/livez` and `/readyz`
- **OpenAPI 3.1 spec** generated from your registered routes and Go types
- **Interactive Swagger UI** at `/docs`
- **Graceful shutdown** — `Start` handles SIGINT/SIGTERM built-in, no signal setup needed
- **Panic recovery** middleware

Need observability? Add `WithObservability()` to enable structured logging, OpenTelemetry tracing, and Prometheus metrics — each configurable independently.

## Next Steps

- [First application](/docs/getting-started/first-application/) — a deeper walkthrough of the full app setup
- [App guide](/docs/guides/app/) — lifecycle, middleware, and server configuration
- [Binding guide](/docs/guides/binding/) — JSON, query params, path params, and validation
- [OpenAPI guide](/docs/guides/openapi/) — customize your auto-generated API docs
