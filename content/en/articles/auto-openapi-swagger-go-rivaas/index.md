---
title: "Auto-Generate OpenAPI and Swagger UI in Go with Rivaas"
date: 2026-03-29
description: "Learn how Rivaas builds your OpenAPI spec and Swagger UI from Go types and per-route docs. No hand-written YAML and no separate codegen step for the spec."
author: "Rivaas Team"
tags: [openapi, swagger, tutorial, go, rivaas]
keywords:
  - go openapi generation
  - swagger ui golang
  - rivaas openapi
  - automatic api documentation
  - go rest api docs
draft: false
sitemap:
  priority: 0.8
---

Keeping an OpenAPI file up to date by hand is slow and easy to get wrong. Your code changes, but the spec does not, and clients stop trusting the docs. Rivaas helps by building the **OpenAPI** document when your app runs. You describe each route next to the handler, and your Go types become **JSON Schema** in the spec. This tutorial shows a small working API and where to open **Swagger UI**.

**OpenAPI** is the standard format for describing HTTP APIs. **Swagger UI** is the page that lets you try requests against that description.

## How Rivaas fits in

You turn on OpenAPI on the app with `app.WithOpenAPI(...)`. For each route, you pass `app.WithDoc(...)` and options from `rivaas.dev/openapi`, such as `openapi.WithSummary`, `openapi.WithRequest`, and `openapi.WithResponse`.

By default the spec follows **OpenAPI 3.0.x**, which most tools understand well. If you need **OpenAPI 3.1.x** (closer to JSON Schema 2020-12), add `openapi.WithVersion(openapi.V31x)` inside `app.WithOpenAPI`.

Tools like [swaggo/swag](https://github.com/swaggo/swag) generate a spec from comments and need a build step. Here your types and `WithDoc` options stay close to the routes, and the spec is generated at runtime—no `swag init` for the spec itself.

## Example types

These structs are normal Go models. They are also what Rivaas uses to fill in request and response schemas in the spec when you reference them in `WithDoc`:

```go
type CreateOrderRequest struct {
	CustomerID string      `json:"customer_id" validate:"required"`
	Items      []OrderItem `json:"items"       validate:"required,min=1"`
	Notes      string      `json:"notes,omitempty"`
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

Rivaas maps `json` tags to property names. It uses validation tags like `validate:"required"` to mark required fields where supported. Fields with `omitempty` are treated as optional in the schema. For edge cases and full rules, see the [schema generation](https://rivaas.dev/docs/guides/openapi/schema-generation/) guide.

## Step 1: Create the project

```bash
mkdir orders-api && cd orders-api
go mod init example.com/orders
go get rivaas.dev/app
go get rivaas.dev/openapi
```

## Step 2: Add a full `main.go`

The app below enables OpenAPI and **Swagger UI** at `/docs`. It registers `POST /orders` with a documented request and response. `MustBind` reads and validates the JSON body; if something is wrong, Rivaas sends an error response and the handler returns early.

```go
package main

import (
	"context"
	"log"
	"net/http"
	"time"

	"rivaas.dev/app"
	"rivaas.dev/openapi"
)

type CreateOrderRequest struct {
	CustomerID string      `json:"customer_id" validate:"required"`
	Items      []OrderItem `json:"items"       validate:"required,min=1"`
	Notes      string      `json:"notes,omitempty"`
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

func main() {
	a, err := app.New(
		app.WithServiceName("orders-api"),
		app.WithServiceVersion("1.0.0"),
		app.WithOpenAPI(
			openapi.WithDescription("Small example API with generated OpenAPI and Swagger UI."),
			openapi.WithServer("http://localhost:8080", "Local"),
			openapi.WithSwaggerUI("/docs"),
		),
	)
	if err != nil {
		log.Fatal(err)
	}

	a.POST("/orders", createOrder,
		app.WithDoc(
			openapi.WithSummary("Create order"),
			openapi.WithRequest(CreateOrderRequest{}),
			openapi.WithResponse(http.StatusCreated, CreateOrderResponse{}),
			openapi.WithTags("orders"),
		),
	)

	if err := a.Start(context.Background()); err != nil {
		log.Fatal(err)
	}
}

func createOrder(c *app.Context) {
	var req CreateOrderRequest
	if !c.MustBind(&req) {
		return
	}
	resp := CreateOrderResponse{
		OrderID:   "ord_001",
		Status:    "pending",
		CreatedAt: time.Now().UTC(),
	}
	_ = c.JSON(http.StatusCreated, resp)
}
```

## Step 3: Run the server and open Swagger UI

```bash
go run .
```

- **`GET /openapi.json`** — JSON document for your API. You can change the path with `openapi.WithSpecPath` if needed.
- **`GET /docs`** — **Swagger UI** (because of `openapi.WithSwaggerUI("/docs")`).

Open `http://localhost:8080/docs` in a browser to explore and try the operation.

## Step 4: Optional API metadata and OpenAPI 3.1

`app.WithServiceName` and `app.WithServiceVersion` feed into the spec’s title and version. You can also set them directly:

```go
app.WithOpenAPI(
	openapi.WithTitle("Order Service API", "1.0.0"),
	openapi.WithDescription("Manage customer orders."),
	openapi.WithVersion(openapi.V31x), // use 3.1.x; omit for default 3.0.x
	openapi.WithSwaggerUI("/docs"),
)
```

For auth in the spec, common helpers include `openapi.WithBearerAuth` and `openapi.WithAPIKeyAuth`. See [OpenAPI configuration](https://rivaas.dev/docs/guides/openapi/configuration/) for the full list.

## Compare approaches

| Approach | Main source of truth | Extra build step for the spec | Stays aligned with handlers |
| -------- | -------------------- | ------------------------------ | --------------------------- |
| Hand-written YAML | The spec file | No | Only if you edit the file every time |
| swaggo | Comments + code | Yes (`swag init`) | Mostly, if you maintain comments |
| **Rivaas** | Types + `WithDoc` | No | Yes, for routes you document |

## More detail on operations and schemas

Per-route options (summaries, examples, security, multiple responses) are listed in the [operation options](https://rivaas.dev/docs/reference/packages/openapi/operation-options/) reference and the [operations](https://rivaas.dev/docs/guides/openapi/operations/) guide. Validation tags such as `validate:"min=1,max=100"` can appear as JSON Schema limits where the generator supports them. Optional spec self-check (meta-validation) is off by default for speed; see [validation](https://rivaas.dev/docs/guides/openapi/validation/) if you want to turn it on in development or CI.

## Next steps

- [OpenAPI guides](https://rivaas.dev/docs/guides/openapi/) — install, configure, security, diagnostics
- [App package: OpenAPI](https://rivaas.dev/docs/guides/app/openapi/) — how `WithOpenAPI` and `WithDoc` work together
- [OpenAPI package reference](https://rivaas.dev/docs/reference/packages/openapi/)
- [Swagger UI options](https://rivaas.dev/docs/reference/packages/openapi/swagger-ui-options/)

Run your service, open `/docs`, and use the live spec as the single place your team and clients read for this API.
