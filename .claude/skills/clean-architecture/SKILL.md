---
name: clean-architecture
description: >
  Apply Clean Architecture (Uncle Bob's layered design with the Dependency
  Rule) when writing or structuring implementation code for a feature or
  service — entities/domain, use cases, interface adapters, and
  frameworks/drivers kept in separate layers, with dependencies pointing
  only inward. Trigger on requests like "write this in clean
  architecture", "structure this with clean/hexagonal/ports-and-adapters
  architecture", "set up the layers for this service", or whenever code is
  about to be laid out for a new backend feature and no existing project
  structure already dictates otherwise (including when the `coder` agent
  is implementing a task breakdown and the task/plan calls for clean
  architecture).
---

# Clean Architecture

A concrete process for laying out and writing code in four dependency-ruled
layers, language-agnostic (Python, Node.js, or otherwise — follow whatever
language the task/plan already settled on; this skill governs structure,
not stack choice).

## The Dependency Rule (non-negotiable)

Source-code dependencies may only point **inward**. Nothing in an inner
layer may know the name of, import, or reference anything in an outer
layer. Outer layers depend on inner layers only through interfaces
("ports") the inner layer defines — never the concrete implementation.

```
   Frameworks & Drivers   (web framework, ORM/DB client, HTTP clients, CLI/UI, DI wiring)
        ↓ implements ports defined inward
   Interface Adapters     (controllers, presenters, repository/gateway implementations)
        ↓ implements ports defined inward
   Use Cases              (application services — orchestrate entities; define ports
                            for anything they need from outside)
        ↓ uses
   Entities                (core domain objects + business rules — zero external deps)
```

Arrows of *knowledge* point down this list; arrows of *control/invocation*
can point either way, but only through an inward-defined interface.

## Process

1. **Identify the entities.** The core domain objects and business rules
   for this feature — no framework, DB, HTTP, or UI types anywhere near
   them. Plain data + behavior only.
2. **Identify the use case(s).** The application-specific operations
   (e.g. "register a user", "tag a commit"). A use case depends only on
   entities and on **ports** — interfaces it defines for anything it
   needs from the outside world (a repository, a notifier, a clock).
3. **Identify the interface adapters.** Controllers/handlers that invoke
   use cases, presenters that shape their output, and the concrete
   implementations of the ports (e.g. a `SqlUserRepository` implementing
   the use case's `UserRepository` port). These depend inward on the use
   case's ports; the use case never imports them.
4. **Identify the frameworks & drivers.** The outermost layer: the actual
   web framework, DB driver/ORM, message queue client, CLI entrypoint, and
   the composition root/DI wiring that instantiates concrete adapters and
   injects them into use cases. This is the only place concrete
   adapter/infra classes get instantiated.
5. **Lay out folders to mirror the layers.** Keep the same shape
   regardless of language:
   ```
   src/
     domain/            # entities + value objects + business rules, zero deps
     usecases/
       ports/           # interfaces use cases need (repository, gateway, clock, ...)
     adapters/          # controllers, presenters, port implementations
     infra/             # framework/db/queue clients, DI wiring, main/entrypoint
   ```
   - Node.js: same shape under `src/` (`src/domain`, `src/usecases`,
     `src/adapters`, `src/infra`).
   - Python: same shape as packages (`domain/`, `usecases/`, `adapters/`,
     `infra/`) under the project's root package.
   - Only create a layer folder the feature actually needs — don't scaffold
     empty layers "just in case" — but once a layer exists, its dependency
     direction is not optional.
6. **Wire dependencies only at the composition root.** One place (the
   `infra` entrypoint / DI setup) constructs concrete adapters and passes
   them into use cases. Nothing inside `usecases/` or `domain/` should ever
   do `new ConcreteRepo()` / `ConcreteRepo()` itself.
7. **Write tests per layer.** `domain/` and `usecases/` tests must run
   without a real framework, DB, or network call — use fakes/mocks for the
   ports. Adapter/infra tests are where real integrations get exercised.

## Guardrails

- Never import a web framework, ORM/DB client, queue client, or UI
  library inside `domain/` or `usecases/`.
- A use case may reference only its own ports (interfaces), never a
  concrete adapter class, even by type-hint/import.
- Keep exactly one composition root responsible for wiring concrete
  adapters into use cases — don't scatter instantiation of infra classes
  through the codebase.
- If a task breakdown or plan already named a language/framework, this
  skill does not override that — it only shapes the internal layering
  within that stack.
- If the feature is trivial enough that a full four-layer split would add
  more ceremony than value (a one-off script, a single pure function),
  say so rather than forcing the structure — clean architecture is a tool
  for managing complexity, not a checklist to satisfy regardless of size.
