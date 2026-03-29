---
title: "Building Microservices with Rivaas in Kubernetes"
date: 2026-10-01
description: "Deploy Go microservices built with Rivaas to Kubernetes. Learn how built-in health probes, graceful shutdown, and OpenTelemetry make Rivaas a natural fit for K8s."
author: "Rivaas Team"
tags: [tutorial, kubernetes, microservices, cloud-native]
keywords:
  - go microservices framework
  - rivaas kubernetes
  - go api kubernetes deployment
  - cloud native go framework
draft: true
sitemap:
  priority: 0.8
---

Rivaas was designed for cloud-native environments. This tutorial shows how its built-in health probes, graceful shutdown, and OpenTelemetry integration map directly to Kubernetes concepts -- no extra middleware or custom code required.

## Why Rivaas for Kubernetes

-   Built-in liveness and readiness probes
-   Graceful shutdown with configurable drain period
-   OpenTelemetry tracing for distributed service calls

## Architecture

-   Two microservices: an API gateway and a backend service
-   Service-to-service communication with trace propagation
-   Kubernetes deployment with health checks

## Service 1: API Gateway

-   Route definitions and middleware
-   Forwarding requests to backend service
-   Trace context propagation

## Service 2: Backend Service

-   Business logic handlers
-   Database connectivity
-   Health probe customization

## Kubernetes Manifests

-   Deployment with liveness/readiness probes
-   Service and Ingress configuration
-   ConfigMap for environment-based settings

## Graceful Shutdown in Practice

-   How Rivaas handles SIGTERM
-   Connection draining
-   Kubernetes termination grace period alignment

## Observability in K8s

-   Prometheus ServiceMonitor for metrics scraping
-   Jaeger/Tempo for distributed tracing
-   Log aggregation with structured JSON output

## Links

-   [Rivaas Health Endpoints Guide](/docs/guides/app/health-endpoints/)
-   [Rivaas Lifecycle Guide](/docs/guides/app/lifecycle/)
-   [Building a REST API with Rivaas](/blog/tutorials/build-rest-api-rivaas/)
