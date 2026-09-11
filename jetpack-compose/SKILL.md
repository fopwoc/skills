---
name: jetpack-compose
description: >
  Jetpack Compose conventions, used together with `kotlin`. MUST be loaded before creating or editing any Compose UI — @Composable functions, screens, previews, theming, navigation, androidx.compose or Compose Multiplatform code.
when_to_use: >
  Invoke alongside `kotlin` before designing the file layout or writing the first composable; it governs where components and previews live, not just how they are written.
---

# Reference implementation

ViewModel and state-handling style differs between my projects. Match the style already used in the project and treat an existing screen as the reference implementation; fall back to the rules below only where the project has no precedent.

# Libraries

- Prefer official Jetpack Compose and AndroidX libraries.
- Use Navigation3 as navigation library.
- Use Room 3 exclusively for local database storage.

# Structure

Use different hierarchy strategies for UI and non-UI code.

- **Layer-first for non-UI code.** Top-level packages represent architectural layers such as `api`, `domain`, `database`, `platform`, and `utils`. Organize code within those layers by responsibility when needed.
- **Feature-first for UI code.** Organize UI around screens under `ui/page/`.
- **One screen = one package.** Never place multiple screens directly in the same screen package.
- A standalone screen lives at `ui/page/<screen>/`.
- A family or flow of related screens lives under a common group package, with each screen still having its own package: `ui/page/<group>/<screen>/`.
- A group package organizes related screens; it is not itself a screen package.
- Keep screen-owned code inside its screen package.
- Keep the primary `*View`, `*Model`, `*ViewModel`, and `*Route` directly in the screen package.
- When enough supporting code of one responsibility accumulates, group it into a focused screen-owned subpackage such as `component/`, `model/`, or another appropriately named package.
- Do not create subpackages preemptively. Keep the screen package flat until a meaningful group exists.
- Put code shared only by screens in the same group at their closest common group package, following the same responsibility-based structure.
- Put reusable cross-feature UI under `ui/component/`.
- Promote code to a broader package only when its ownership is genuinely broader.

# Screen architecture

Each screen package owns its screen architecture and screen-specific components.

Represent a screen with four primary pieces:

- `*View`: stateless UI containing the complete visual structure of the screen. Takes a screen-specific `*Model`, observable data required for rendering, and typed callbacks. Contains no business logic or navigation.
- `*Model`: complete immutable screen-specific UI state.
- `*ViewModel`: owns business operations, network requests, screen-specific persistent UI state, model updates, and dedicated Room observers. Keep Room-backed state separate from the screen Model.
- `*Route`: glue between ViewModel and View. Collects observable state, provides navigation/environment dependencies, owns effects, and connects callbacks.

Persistent UI state belongs to Model/ViewModel. Views may remember only transient rendering state such as animation, gesture, layout, or measurement state.

Keep navigation and external side effects at Route/ViewModel boundaries. Prefer typed callbacks into Views.

## Components

Treat each UI file as one independently understandable, testable, and previewable UI unit.

- Keep the complete screen layout cohesive inside its `*View`, even when the View becomes large.
- Do not extract private composables merely to reduce function or file size.
- Extract UI only when it has a distinct responsibility and deserves to become a real component.
- Extract components by responsibility, never merely by size.
- Put each screen-specific component in its own file under that screen's `component/` package.
- Put components shared only by screens in the same group under that group's `component/` package.
- Put reusable cross-feature components under `ui/component/`.

# Previews

Previews belong to the UI unit they demonstrate.

- Always keep previews in the same file as the View or component they preview.
- Place previews after the implementation.
- Never create separate preview files.
- Previews are part of the UI unit, not independent UI units; any number of previews may live beside their single View/component.
- Use multiple previews to cover meaningful visual and business states of that unit.
- Keep preview-only fixtures private and in the same file.
- Do not use a screen View preview as a Cartesian showcase of every state of its child components; preview those components in their own files.
- Name previews `Preview<ComposableName><State>`, omitting the `View` suffix from screen Views, e.g. `SettingsProfileView` → `PreviewSettingsProfileInitializing`.
- Use a project-wide preview theme wrapper providing `MaterialTheme` and required `CompositionLocal`s. Create one if absent.

# State and rendering

- ViewModel owns and exposes the data sources and Room observers required by the screen; Route performs Compose subscriptions to them.
- Leaf components must not own feature-level repository or Room subscriptions.
- Scope observers to the required domain and preserve stable state across recomposition. Avoid per-item observers unless explicitly required by the data source or ownership model.
- Keep gesture ownership at the container that owns the coordinate system; use parent-level hit testing when necessary.
- Preserve stable component identity. Key live data by stable IDs, not mutable snapshots.
- For continuous geometry such as time, use one canonical coordinate transform for rendering, items, hit testing, and boundaries.

# Data and Room

Before touching ViewModel data loading, repositories, DAOs, entities, or Room observers, read `references/room.md`.

# Theme

Before touching theming, `CompositionLocal`s, or component-level styling policy, read `references/theme.md`.
