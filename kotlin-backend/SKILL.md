---
name: kotlin-backend
description: >
  Kotlin backend conventions, used together with `kotlin`. MUST be loaded before creating or editing backend Kotlin — Ktor, Exposed, services, routing, persistence, API or server modules.
when_to_use: >
  Invoke alongside `kotlin` before writing or modifying any server-side Kotlin, not after.
---

# Libraries

- Prefer Kotlin-first libraries where practical: Ktor, coroutines, kotlinx.serialization, Exposed, etc., unless I explicitly ask otherwise.
- Use `logback-classic` for logging. Always provide a default logging configuration in resources with a readable pattern.
