---
name: jetpack-compose
description: >
  Jetpack Compose engineering conventions. Use together with the `kotlin` skill.
---

# Libraries

- Prefer official Jetpack Compose and AndroidX libraries.
- Use Room 3 exclusively for local database storage.

# Structure

Use different hierarchy strategies for UI and non-UI code.

- **Layer-first for non-UI code.** Top-level packages represent architectural layers such as `api`, `domain`, `database`, `platform`, and `utils`. Organize code within those layers by responsibility when needed.
- **Feature-first for UI code.** A screen and everything owned exclusively by it belong together under `ui/page/<feature>`, regardless of whether the code represents state, layout, or screen-specific components.
- Put reusable UI in `ui/component`. Component families may have their own package.
- Put shared code at its closest common owner. Promote it to a broader package only when its ownership is genuinely broader.
- Keep package and file ownership focused on one clear responsibility.

# Screens

Represent a screen with four pieces in the same feature package:

- `*View`: stateless UI. Takes a screen-specific `*Model`, observable data required for rendering, and typed callbacks. Contains no business logic or navigation.
- `*Model`: complete immutable screen-specific UI state.
- `*ViewModel`: owns business operations, network requests, screen-specific persistent UI state, model updates, and dedicated Room observers. Keep Room-backed state separate from the screen Model.
- `*Route`: glue between ViewModel and View. Collects observable state, provides navigation/environment dependencies, owns effects, and connects callbacks.

Persistent UI state belongs to Model/ViewModel.

Views may remember only transient rendering state such as animation, gesture, layout, or measurement state.

Keep navigation and external side effects at Route/ViewModel boundaries. Prefer typed callbacks into Views.

# Data flow

For persistent backend data used by UI, prefer a local-first reactive flow:

`ViewModel → Network → Room Repository → Room Observer → Route → View`

Room is the source of truth for persistent backend data.

- ViewModel initiates network operations. Successful persistent results are written through the Room repository.
- Do not copy Room-backed data into the screen `Model`. Keep persisted data and ViewModel-owned UI state as separate state channels.
- ViewModel owns dedicated Room observers for the persisted data required by the screen. A screen may have one or several observers.
- Room observers may be parameterized when the observed query depends on current screen state or arguments.
- Each observer exposes a subscribable value backed by a repository `Flow`.
- Route subscribes to the screen `Model` and Room observers independently, then passes their current values into View.
- View receives plain prepared values and remains unaware of Room, repositories, and network access.
- Repositories expose convenient, purpose-specific `Flow`s rather than requiring ViewModel or UI code to assemble raw database state.
- Do not persist inherently transient data merely to satisfy this pattern.

# Room

Use a consistent structure for persisted types:

`DatabaseInterface → DatabaseModel / DatabaseEntity → Dao → Repository`

## Models

- Define a small `*DatabaseInterface` containing the value shared by database representations.
- `*DatabaseModel` represents the database-facing value outside Room.
- `*DatabaseEntity` is the Room entity and implements the same interface.
- Keep explicit conversion between DatabaseModel and DatabaseEntity.
- Use `kotlin.uuid.Uuid` for UUID identifiers.
- Keep the Room table name as `TABLE_NAME` on the entity.

## DAO

Keep DAOs focused on database operations.

Provide only operations required by the domain, commonly:

- singular and collection reads;
- `Flow` variants for observable reads;
- insert/update with `OnConflictStrategy.REPLACE`;
- singular and bulk deletion.

Prefer Room `Flow` queries for observable data rather than manually invalidating or refreshing cached state.

## Repository

Hide DAOs behind a repository.

- Repository APIs expose domain values, not Room entities.
- Convert entities at the repository boundary.
- Expose convenient typed `Flow`s already mapped into values useful to consumers.
- Keep Room-specific details inside the database layer.
- Treat a missing value as an explicit domain/database condition rather than leaking nullable Room results when absence is exceptional.
- Log meaningful database reads, writes, deletes, and Flow updates at debug/verbose level.

## Complex values

For complex serializable values stored as a single database field:

- Prefer a typed Room converter backed by CBOR serialization.
- Keep converters generic/reusable where practical.
- Reference actual Kotlin types rather than hardcoded type names.

# Components

- Prefer one main composable per component file. Keep its implementation cohesive instead of splitting it into trivial private composables.
- Split genuinely different visual variants into separate components. Combinators should primarily select or arrange components.
- Keep component-specific calculations with the component. Extract them only when genuinely reused.
- For web targets, give resource-backed `Icon`s an explicit size matching the asset dimensions. Do not rely on intrinsic size while web resources are loading.

# Previews

- Keep previews and their private fixtures next to the component or View they demonstrate.
- View previews represent meaningful complete screen states, not every child-component combination.
- Name previews `Preview<Name><State>`, for example `PreviewSettingsProfileInitializing`.
- Use a project-wide preview theme wrapper providing `MaterialTheme` and required `CompositionLocal`s. Create one if absent.

# CompositionLocal

Use `CompositionLocal` for ambient, subtree-scoped presentation policy such as theme tokens, spacing, padding, borders, and component styling.

- Provide overrides at the nearest owner boundary.
- Provide stable defaults at the theme/root boundary.
- Do not use CompositionLocal for business state, persistent mutable state, callbacks, or ordinary data dependencies.
- Prefer explicit parameters when a value affects component behavior rather than ambient presentation.

# State and rendering

- Subscribe to feature data in ViewModel/state owners. Leaf components must not own feature-level repository subscriptions.
- Scope subscriptions to the required domain and preserve stable state across recomposition.
- Avoid per-item subscriptions unless explicitly required by the data source or ownership model.
- Keep gesture ownership at the container that owns the coordinate system; use parent-level hit testing when necessary.
- Preserve stable component identity. Key live data by stable IDs, not mutable snapshots.
- For continuous geometry such as time, use one canonical coordinate transform for rendering, items, hit testing, and boundaries.

# Theme

Prefer `MaterialTheme` for visual configuration.

Use project-specific overrides, typically through `CompositionLocal`, when MaterialTheme does not express the required component-level presentation policy.
