---
name: update-conventions
description: >
  Retroactively apply my current instructions and skills to an existing project.
disable-model-invocation: true
---

# Update conventions

Retroactively apply all currently applicable instructions and skills to the existing project, as if they had been in effect when the project was originally written.

- Inspect the whole relevant codebase, not only recent changes.
- Find existing code, structure, configuration, and project files that conflict with current conventions.
- Apply the fixes directly. Do not stop at reporting findings.
- Perform structural refactoring when required by current conventions.
- Preserve intentional project-specific decisions that clearly override general conventions.
- Do not make unrelated changes that are not justified by current instructions or skills.
- Verify the resulting project using its normal build, test, lint, and formatting tools.
- Finish with a concise summary of changes and anything that could not be reconciled automatically.
- Treat every skill explicitly invoked for this task as an applicable convention and apply it across the project, not only to newly changed code.
