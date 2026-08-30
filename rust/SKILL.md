---
name: rust
description: >
  Rust engineering conventions.
---

# General

- Write idiomatic Rust. Prefer expressing invariants through ownership, borrowing, enums, generics, traits, and the type system over runtime checks or conventions.
- Prefer safe Rust. Use `unsafe` only when necessary and keep it behind a small safe API with documented invariants.
- Prefer borrowing over cloning. Do not add `clone()` merely to work around ownership without considering a better ownership model.
- Keep visibility as narrow as practical. Do not make items `pub` without an actual crate/module boundary.
- Use `rustfmt`, keep Clippy clean, and verify changes with `cargo check` and relevant tests.

# Types and API

- Prefer domain-specific newtypes over primitives when values have distinct semantics.
- Prefer exhaustive enums over stringly typed states or loosely related booleans.
- Use typestate and generic constraints when they make invalid states or operation sequences impossible at compile time.
- Prefer `impl Trait` when callers do not need to know or name the concrete type.
- Avoid unnecessary `Box<dyn Trait>` and dynamic dispatch when static dispatch expresses the design naturally.

# Ownership

- Design ownership deliberately instead of fixing borrow-checker errors with `clone()`, `Arc`, or `Rc`.
- Prefer owned values at clear ownership boundaries and borrowing within local operations.
- Use `Arc`/`Rc` only when shared ownership is actually part of the data model.
- Keep lock scopes small. Never perform unrelated work while holding a lock.

# Structure

- Keep one primary substantial type per file. Use module directories when a responsibility consists of several related types.
- Keep modules focused and visibility narrow.
- Use crates for meaningful architectural or reusable boundaries, not merely to split code.
- Prefer Cargo workspaces for multi-crate projects.

# Cargo

- Use the latest stable Rust edition and toolchain unless compatibility requires otherwise.
- In workspaces, keep shared dependency versions and common package metadata in the workspace `Cargo.toml`.
- Keep dependency features minimal, explicit, additive, and composable. Disable large default feature sets when they are not needed.
- Prefer established crates over implementing substantial infrastructure yourself.

# Errors

- Use `Result` for recoverable failures and `Option` for absence.
- Do not use `unwrap()` or `expect()` where failure is realistically possible.
- Use typed errors at meaningful library/domain boundaries.
- Preserve useful context when propagating errors across layers.
- Do not create elaborate error hierarchies when a simpler representation is sufficient.

# Serialization

- Keep serialization models separate from domain models when the external representation imposes different constraints.
- Prefer strongly typed Serde models over manual JSON/value manipulation.

# Async

- Use async only for genuinely asynchronous work.
- Do not perform blocking operations or hold blocking locks in async paths.
- Prefer structured task ownership. Do not spawn detached tasks without a clear lifecycle and error-handling strategy.

# Versioning

When build-time version information is required, resolve it during the Cargo build and expose it as compile-time metadata. Do not invoke Git from application runtime code.
