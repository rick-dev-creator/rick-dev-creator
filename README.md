# Tech Lead / Staff Engineer

20 years designing and building enterprise software. I specialize in turning complex business problems into scalable distributed systems.

## Current Focus

Leading architecture decisions for multi-tenant platforms using **.NET**, **Microsoft Orleans**, and **event-driven architectures**. I design systems that handle thousands of concurrent conversations with sub-millisecond latency using the Actor Model, Event Sourcing, and CQRS patterns.

## How I Approach Problems

I believe good architecture emerges from understanding the domain first. My systems are built on:

- **Domain-Driven Design** — Business logic lives in the domain, not in controllers or databases
- **Event Sourcing** — Every state change is an immutable event. Full audit trails, time-travel debugging, analytics from event streams
- **Actor Model (Orleans)** — Each entity is an independent actor. No locks, no race conditions, horizontal scaling built-in
- **Tiered Storage** — Hot data in memory, warm data in database. Automatic archival with temporal partitioning by day

## What I've Built

### Enterprise CRM Platform
A multi-tenant conversation management system handling WhatsApp, Instagram, and other messaging platforms.

**The challenge:** Route thousands of customer conversations through customizable workflows while maintaining real-time analytics and full audit compliance.

**The solution:**
- **Orleans Grains** for conversation state (ConversationGrain, InboxGrain, CardGrain, WorkflowGrain)
- **Event Sourcing** with hot storage (100 events in-memory) and warm archive (partitioned by day)
- **Broadcast Channels** for decoupled event distribution across the system
- **Workflow Engine** with kanban-style stages, cycle management, and load balancing (round-robin, weighted, least-busy)
- **Platform Strategy Pattern** abstracting messaging platform differences (24-hour windows, templates, capabilities)

**Scale:** 2,400+ source files, modular monolith architecture, real-time SSE updates, distributed cache invalidation.

### Income Dashboard
Clean Architecture implementation with Blazor SSR demonstrating modular monolith patterns and external API integrations.

### Modular Monolith Template
Open-source .NET 8 template that jumpstarts projects with Clean Architecture, FastEndpoints, MediatR, and FluentValidation.

## Technical Depth

```
Distributed Systems    Orleans Grains | Broadcast Channels | Temporal Partitioning
Architecture           Clean Architecture | Modular Monolith | DDD | CQRS
Event-Driven           Event Sourcing | Domain Events | Tiered Storage
Backend                .NET 8 | ASP.NET Core | .NET Aspire | Blazor | EF Core
Frontend               TypeScript | React | Astro | PWA
Infrastructure         Docker | Azure | PostgreSQL | Azure Table Storage
```

## Philosophy

Ship working software. Avoid premature abstraction. Let the domain drive the design. Document decisions, not just code.

---

![C#](https://img.shields.io/badge/C%23-239120?style=flat-square&logo=csharp&logoColor=white)
![.NET](https://img.shields.io/badge/.NET-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![Orleans](https://img.shields.io/badge/Orleans-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Blazor](https://img.shields.io/badge/Blazor-512BD4?style=flat-square&logo=blazor&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)
