---
name: kotlin
description: >
  Kotlin engineering conventions. MUST be loaded before creating or editing any .kt or .kts file, Gradle Kotlin DSL, or Kotlin project structure — Android, Compose Multiplatform, KMP, Ktor backends and KSP processors alike.
when_to_use: >
  Invoke on the first Kotlin file of a task, before writing code, not after. Combine with `jetpack-compose` for Compose UI.
---

# General

- Prefer idiomatic Kotlin over Java-style code. Embrace Kotlin syntax sugar and expressive language features to write elegant code.
- Keep the file's `package` declaration consistent with its actual path within the module.
- Do not add command-line properties, system properties, or Gradle property passthrough for application startup unless I explicitly ask for it.
- Always verify that your changes compile without errors. Use `./gradlew`.

# Versioning

For Gradle projects, resolve Git-derived version information in `buildSrc`.

Keep version resolution in one shared helper/singleton. Resolve values lazily and cache them so every module observes exactly the same version, build number, commit hash, and related build metadata.

Modules must consume this shared build information instead of invoking Git or independently calculating versions.

# Multiplatform

Android apps, including Jetpack Compose apps, should always use a Kotlin Multiplatform project structure, even when Android is currently the only target.

- For platform-specific functionality, define the API in common code and keep platform details behind it. Platform code should provide an implementation of the common API. Design the boundary so adding another platform does not require changing common consumers.

# Backend

- Backend projects are JVM-only by default unless I explicitly ask otherwise.
- Prefer Kotlin-first libraries where practical: Ktor, coroutines, kotlinx.serialization, Exposed, etc., unless I explicitly ask otherwise.
- Use `logback-classic` for logging. Always provide a default logging configuration in resources with a readable pattern.

# KSP

When writing or modifying a KSP processor, annotations, or code generation, read `references/ksp.md` first.
