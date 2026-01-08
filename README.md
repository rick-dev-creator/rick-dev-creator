# Software Architect | .NET & TypeScript

Building enterprise-grade distributed systems with **Clean Architecture**, **Domain-Driven Design**, and **Actor Model** patterns since 2006.

## Tech Stack

```text
Backend         .NET 8 | ASP.NET Core | Microsoft Orleans | .NET Aspire | Blazor
Frontend        TypeScript | React | Astro | PWA
Architecture    Clean Architecture | Modular Monolith | DDD | CQRS | Event Sourcing
Distributed     Orleans Grains | Broadcast Channels | Temporal Partitioning | Tiered Storage
Database        PostgreSQL | Azure Table Storage | Entity Framework Core
DevOps          Docker | GitHub Actions | Azure
Patterns        MediatR | FastEndpoints | FluentValidation | Result Pattern
```

## Architectural Expertise

```
┌─────────────────────────────────────────────────────────────┐
│                      Presentation                            │
│           (FastEndpoints / Blazor / React / SSE)            │
├─────────────────────────────────────────────────────────────┤
│                      Application                             │
│              (CQRS Commands/Queries + MediatR)              │
├─────────────────────────────────────────────────────────────┤
│                        Domain                                │
│       (Aggregates, Value Objects, Domain Events, DDD)       │
├─────────────────────────────────────────────────────────────┤
│                    Infrastructure                            │
│   (Orleans Grains, EF Core, External APIs, Message Brokers) │
└─────────────────────────────────────────────────────────────┘
```

## What I Build

**Distributed Systems with Orleans**
Actor-based architectures using Microsoft Orleans virtual actors (Grains), broadcast channels for pub/sub, and tiered storage strategies with hot/warm data separation.

**Event-Sourced Platforms**
Complete event sourcing implementations with temporal partitioning, event replay capabilities, and analytics reconstruction from event streams.

**Multi-Tenant Enterprise Systems**
Modular monolith platforms with domain-driven design, CQRS patterns, workflow engines, and distributed cache invalidation.

**Real-Time Applications**
Full-stack dashboards with Blazor, Server-Sent Events (SSE), and live analytics integration.

## Advanced Patterns I Implement

| Pattern | Implementation |
|---------|----------------|
| **Actor Model** | Orleans Grains with single-threaded execution, location transparency |
| **Event Sourcing** | Immutable event streams, state reconstruction, audit trails |
| **Temporal Partitioning** | Archive grains partitioned by day for efficient date-range queries |
| **Tiered Storage** | Hot storage (in-memory) + Warm storage (database) with auto-archival |
| **Broadcast Channels** | Decoupled pub/sub for cross-grain event distribution |
| **Platform Strategy** | Abstracted platform-specific logic (WhatsApp, Instagram, etc.) |
| **Workflow System** | Kanban-style card progression with cycles, stages, and load balancing |
| **Distributed Cache** | Cross-module cache invalidation via domain events |

## Featured Work

### CRM Platform (Enterprise)
Multi-tenant CRM with Microsoft Orleans, event sourcing, and workflow automation. Features conversation grains, inbox management, and real-time analytics with temporal partitioning.

`Microsoft Orleans` `Event Sourcing` `CQRS` `Temporal Partitions` `Broadcast Channels` `Azure Table Storage`

### Income Dashboard
Clean Architecture dashboard with Blazor SSR, modular monolith design, and crypto API integrations. Demonstrates modern .NET 8 patterns with Docker deployment.

`ASP.NET Core` `Blazor` `Clean Architecture` `Docker` `Modular Monolith`

### Modular Monolith Template
Production-ready .NET 8 template for building scalable applications. Includes module scaffolding, validation, and endpoint patterns.

`ASP.NET Core` `MediatR` `FastEndpoints` `FluentValidation` `.NET 8`

---

![C#](https://img.shields.io/badge/C%23-239120?style=flat-square&logo=csharp&logoColor=white)
![.NET](https://img.shields.io/badge/.NET-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![Microsoft Orleans](https://img.shields.io/badge/Orleans-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Blazor](https://img.shields.io/badge/Blazor-512BD4?style=flat-square&logo=blazor&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)
