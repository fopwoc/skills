# Theme

Prefer `MaterialTheme` for visual configuration.

Use project-specific overrides, typically through `CompositionLocal`, when MaterialTheme does not express the required component-level presentation policy.

# CompositionLocal

Use `CompositionLocal` for ambient, subtree-scoped presentation policy such as theme tokens, spacing, padding, borders, and component styling.

- Provide overrides at the nearest owner boundary.
- Provide stable defaults at the theme/root boundary.
- Do not use CompositionLocal for business state, persistent mutable state, callbacks, or ordinary data dependencies.
- Prefer explicit parameters when a value affects component behavior rather than ambient presentation.
