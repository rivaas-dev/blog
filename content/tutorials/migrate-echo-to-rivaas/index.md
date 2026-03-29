---
title: "Migrating from Echo to Rivaas"
date: 2027-02-15
description: "Step-by-step guide to migrating your Go API from Echo to Rivaas, with API mapping tables, middleware conversion patterns, and testing strategies."
author: "Rivaas Team"
tags: [tutorial, migration, echo]
keywords:
  - echo vs rivaas
  - migrate echo rivaas
  - echo go alternative
  - switch from echo to rivaas
draft: true
sitemap:
  priority: 0.7
---

Echo is a popular, minimalist Go framework with a clean API. If you're considering Rivaas for its built-in observability and OpenAPI generation, this guide provides a concrete migration path from Echo.

## Why Consider Switching

-   Built-in OpenTelemetry vs manual instrumentation
-   Automatic OpenAPI generation vs manual spec maintenance
-   Integrated health probes and lifecycle management

## API Mapping Table

| Echo | Rivaas | Notes |
|------|--------|-------|
| `echo.New()` | `app.New()` | Similar initialization |
| `e.GET("/path", handler)` | Route definitions | Different handler signatures |
| `c.Bind()` | Automatic binding | Rivaas binds from route definitions |
| `c.JSON()` | `ctx.JSON()` | Similar response API |
| `echo.MiddlewareFunc` | `rivaas.Middleware` | Adapter pattern available |

## Step 1: Replace the Echo Instance

-   Echo `echo.New()` to Rivaas `app.New()`
-   Configuration mapping

## Step 2: Migrate Route Definitions

-   Group and route syntax differences
-   Path parameter handling
-   Query parameter binding

## Step 3: Update Handler Functions

-   Context API comparison
-   Response writing patterns
-   Error handling with `echo.HTTPError` vs Rivaas problem details

## Step 4: Convert Middleware

-   Logger, recover, CORS
-   Custom middleware adaptation
-   Removing echo-specific middleware dependencies

## Step 5: Update Tests

-   Test helper comparison
-   HTTP test patterns
-   Verifying API compatibility

## Links

-   [Go API Frameworks Compared](/blog/comparisons/go-api-frameworks-compared/)
-   [Rivaas Migration Guide](/docs/guides/app/migration/)
-   [Getting Started with Rivaas](/docs/getting-started/)
