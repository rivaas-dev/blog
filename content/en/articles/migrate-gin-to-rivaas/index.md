---
title: "Migrating from Gin to Rivaas"
date: 2026-10-15
description: "A practical migration guide from Gin to Rivaas with code mapping tables, step-by-step instructions, and tips for a smooth transition."
author: "Rivaas Team"
tags: [tutorial, migration, gin]
keywords:
  - migrate gin to rivaas
  - switch go framework
  - gin to rivaas migration guide
  - replace gin golang
draft: true
sitemap:
  priority: 0.8
---

Thinking about switching from Gin to Rivaas? This guide provides a concrete migration path with side-by-side code examples, common patterns, and solutions for the differences you'll encounter.

## Why Migrate?

-   Built-in observability vs bolt-on middleware
-   Automatic OpenAPI generation vs swaggo annotations
-   Integrated health probes and graceful shutdown

## API Mapping Table

| Gin | Rivaas | Notes |
|-----|--------|-------|
| `gin.Default()` | `app.New()` | Rivaas includes more defaults |
| `c.JSON()` | `ctx.JSON()` | Similar API |
| `c.ShouldBindJSON()` | Automatic binding | Rivaas binds from route definitions |
| `gin.H{}` | Standard maps/structs | No framework-specific types |

## Step 1: Replace the Engine

-   Gin `gin.New()` / `gin.Default()` to Rivaas `app.New()`
-   Configuration differences

## Step 2: Migrate Routes

-   Route group syntax comparison
-   Path parameter syntax
-   Middleware attachment

## Step 3: Update Handlers

-   Context API differences
-   Response writing patterns
-   Error handling

## Step 4: Replace Middleware

-   Recovery: built-in in both
-   CORS, rate limiting, auth
-   Custom middleware adapter pattern

## Step 5: Remove Gin-Specific Dependencies

-   Replace gin-contrib packages
-   Remove swaggo annotations (Rivaas auto-generates OpenAPI)
-   Update OpenTelemetry wiring

## Testing After Migration

-   API compatibility verification
-   Performance comparison
-   Observability validation

## Links

-   [Rivaas vs Gin Comparison](/blog/comparisons/rivaas-vs-gin-observability/)
-   [Rivaas Migration Guide](/docs/guides/app/migration/)
-   [Getting Started with Rivaas](/docs/getting-started/)
