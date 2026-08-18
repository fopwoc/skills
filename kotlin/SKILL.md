---
name: kotlin
description: >
  General Kotlin engineering conventions. Use for all Kotlin code.
---

# General

- Prefer idiomatic Kotlin over Java-style code. Embrace Kotlin syntax sugar and expressive language features to write elegant code.
- Keep the file's `package` declaration consistent with its actual path within the module.
- Do not add command-line properties, system properties, or Gradle property passthrough for application startup unless I explicitly ask for it.
- Always verify that your changes compile without errors. Use `./gradlew`.

# Multiplatform

Backend projects are JVM-only by default unless I explicitly ask otherwise.

Android apps, including Jetpack Compose apps, should always use a Kotlin Multiplatform project structure, even when Android is currently the only target.

- For platform-specific functionality, define the API in common code and keep platform details behind it. Platform code should provide an implementation of the common API. Design the boundary so adding another platform does not require changing common consumers.

# KSP

When KSP is used:

- Use KSP to generate boilerplate and repetitive code.
- Separate KSP into a common Kotlin annotations module and a processor module.
- Use KotlinPoet for generated Kotlin code.
- Use actual type declarations (`ClassName`, `TypeName`, etc.) and imports instead of constructing type references from hardcoded strings. Apply the same rule to annotations.
