---
title: "Built-in Observability in Go: Tracing and Metrics with Rivaas"
date: 2026-07-01
description: "Learn how Rivaas integrates OpenTelemetry tracing, Prometheus metrics, and structured logging into your Go API without extra dependencies or boilerplate."
author: "Rivaas Team"
tags: [observability, opentelemetry, prometheus, tracing, metrics]
keywords:
  - opentelemetry go example
  - golang observability middleware
  - go tracing metrics
  - prometheus go framework
  - opentelemetry go framework
draft: true
sitemap:
  priority: 0.8
---

Modern APIs need observability from day one -- not bolted on as an afterthought. This deep dive shows how Rivaas ships with OpenTelemetry tracing, Prometheus metrics, and structured logging built in, so you get production-grade visibility without writing middleware plumbing.

## The Observability Problem in Go

-   Most Go frameworks require manual OpenTelemetry setup
-   Wiring up tracing, metrics, and logging is repetitive boilerplate
-   Libraries often conflict on instrumentation approaches

## How Rivaas Solves It

-   OpenTelemetry-native from the core
-   Zero-config tracing for every HTTP request
-   Prometheus metrics endpoint out of the box

## Tracing Every Request

-   Automatic span creation and propagation
-   Context-aware logging with trace IDs
-   Example: tracing a request through middleware -> handler -> database

## Metrics That Matter

-   Request duration histograms
-   Error rate counters
-   Custom business metrics

## Structured Logging with Trace Correlation

-   slog integration with trace/span IDs
-   JSON output for log aggregation (ELK, Loki, CloudWatch)

## Putting It All Together

-   Complete example: API with tracing, metrics, and logging
-   Grafana dashboard screenshot / configuration
-   Jaeger trace visualization

## Comparison with Manual Setup

-   Lines of code: Rivaas built-in vs manual OpenTelemetry wiring
-   Maintenance burden over time

## Links

-   [Rivaas Observability Guide](/docs/guides/app/observability/)
-   [OpenTelemetry Go SDK](https://opentelemetry.io/docs/languages/go/)
-   [Getting Started with Rivaas](/docs/getting-started/)
