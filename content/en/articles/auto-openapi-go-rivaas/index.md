---
title: "Auto-Generating OpenAPI Docs in Go with Rivaas"
date: 2026-04-15
description: "Stop writing OpenAPI specs by hand. Learn how Rivaas generates OpenAPI 3.1 docs from your Go types and route definitions automatically."
author: "Rivaas Team"
tags: [openapi, swagger, tutorial, code-generation]
keywords:
  - go openapi generation
  - golang swagger automatic
  - openapi from go code
  - go api documentation
draft: true
sitemap:
  priority: 0.8
---

Writing and maintaining OpenAPI specifications by hand is tedious, error-prone, and falls out of sync with your actual API. Rivaas takes a different approach: it generates OpenAPI 3.1 documentation directly from your Go types and route definitions. No annotations, no separate spec files, no code generation step.

## The Problem with Manual OpenAPI

If you've maintained an API, you've probably experienced this:

1. You add a new field to your request struct
2. You forget to update the OpenAPI spec
3. Your API consumers get incorrect documentation
4. Trust erodes

Tools like [swaggo/swag](https://github.com/swaggo/swag) improve this by generating specs from code comments, but you're still maintaining two sources of truth: the Go code and the annotations.

## How Rivaas Does It Differently

Rivaas reads your Go types at startup and generates an OpenAPI 3.1 specification. Your code **is** the documentation.

```go
type CreateOrderRequest struct {
    CustomerID string        `json:"customer_id" validate:"required"`
    Items      []OrderItem   `json:"items"       validate:"required,min=1"`
    Notes      string        `json:"notes,omitempty"`
}

type OrderItem struct {
    ProductID string `json:"product_id" validate:"required"`
    Quantity  int    `json:"quantity"    validate:"required,min=1"`
}

type CreateOrderResponse struct {
    OrderID   string    `json:"order_id"`
    Status    string    `json:"status"`
    CreatedAt time.Time `json:"created_at"`
}
```

When you register a route with these types, Rivaas automatically:
- Generates JSON Schema for request and response bodies
- Infers required fields from `validate:"required"` tags
- Handles nested types and arrays
- Documents optional fields from `omitempty`

## Step-by-Step Example

### 1. Define Your Types

<!-- TODO: Full working example with imports -->

### 2. Register Routes

```go
a.POST("/orders", createOrder,
    openapi.Summary("Create a new order"),
    openapi.Tags("orders"),
)
```

### 3. Access the Generated Docs

Start your application and visit:
- `http://localhost:8080/openapi.json` -- the raw OpenAPI 3.1 spec
- `http://localhost:8080/swagger/` -- interactive Swagger UI

### 4. Customize the Spec

You can add metadata to your API without touching the generated schema:

```go
a, err := app.New(
    app.WithOpenAPI(
        openapi.WithTitle("Order Service API"),
        openapi.WithVersion("1.0.0"),
        openapi.WithDescription("API for managing customer orders"),
    ),
)
```

## Comparison: Rivaas vs swaggo vs Manual

| Approach        | Source of truth | Build step required | Stays in sync |
| --------------- | -------------- | ------------------- | ------------- |
| Manual YAML     | Spec file      | No                  | No            |
| swaggo          | Comments       | Yes (`swag init`)   | Mostly        |
| **Rivaas**      | Go types       | No                  | Always        |

## Advanced Features

<!-- TODO: Document operation options, security schemes, custom schema overrides -->

### Custom Operation Options

Rivaas provides fine-grained control over each operation in the generated spec through functional options.

### Validation-Aware Schemas

Validation tags like `validate:"min=1,max=100"` are reflected in the generated JSON Schema as `minimum` and `maximum` constraints.

## Next Steps

- [OpenAPI guide](/docs/guides/openapi/) -- full documentation on Rivaas's OpenAPI support
- [OpenAPI reference](/docs/reference/packages/openapi/) -- API reference for all options
- [Swagger UI configuration](/docs/reference/packages/openapi/swagger-ui-options/) -- customize the built-in Swagger UI
