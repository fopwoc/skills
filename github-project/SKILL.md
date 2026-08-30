---
name: github-project
description: >
  Conventions for preparing and maintaining my personal projects on GitHub.
disable-model-invocation: true
---

# General

Treat the repository as a public GitHub project.

Keep repository-facing infrastructure simple and proportional to the project's size.

# Versioning

For versioned projects, derive build identity from Git instead of maintaining versions manually in source files.

- A release tag is exactly the release version, e.g. `1.4.2`; do not use a `v` prefix unless required by external tooling.
- For tagged builds, use the exact tag as the version.
- When a numeric build/version code is required, use the repository commit count at that revision.
- For untagged development builds, derive a non-release version as `<YYYYMMDD>-<short-commit-hash>`, e.g. `20260830-aabbccdd`, using the same versioning mechanism.
- Resolve version information once at build time and use the same resolved identity for all modules and produced artifacts.
- Do not invoke Git or derive version information at application runtime.

# GitHub Actions

- Add useful CI for building and verifying the project.
- Use the latest stable major versions of established actions.
- Run the same build/test commands used locally.
- Prefer project-provided wrappers and scripts over duplicating build logic in workflow YAML.
- Use official caching mechanisms where useful.
- Keep workflow permissions minimal.

## CI

### Build check

- Run on changes to the default branch and on pull requests targeting it.
- Do not run build/test jobs for documentation-only changes. Documentation-specific jobs may run only when relevant documentation changes.
- Build the project and run tests when present.
- Upload useful build outputs as GitHub Actions artifacts with a short retention period.

### Tag build

- Trigger releases from semantic version tags.
- Prefer `*.*.*` tags over `v*.*.*` unless tooling requires the `v` prefix.
- Only release commits reachable from the protected release/default branch.
- Create a GitHub Release for the tag.
- Attach release build outputs to the GitHub Release when applicable.
- For projects that produce Docker images, publish them to GitHub Container Registry (GHCR) by default. Use another registry only when explicitly requested.
- Tag container images with the release version and update `latest` for normal stable releases.
- Do not invent polished release notes from commit history. Prefer a concise commit list, grouped when explicit commit metadata such as `Changelog: frontend` is available.

## Dependabot

Always configure Dependabot.

- Cover every supported dependency ecosystem used in the monorepo.
- Cover dependency manifests in all relevant modules/subprojects, not only the repository root.
- Always include the `github-actions` ecosystem to keep CI actions updated.
- Prefer grouped updates where appropriate to avoid excessive dependency PR noise.

# README

When a README is requested:

- Keep it concise and practical.
- Explain what the project is before implementation details.
- Include only useful setup, build, run, and usage instructions.
- Prefer examples and commands over long explanations.
- Do not add generic sections merely because GitHub READMEs commonly have them.
- Do not add badges unless they provide useful information.
- Do not use marketing language for small personal projects.


# Licensing

- Use WTFNMFPL unless I explicitly request another license.
- Keep the original license text in `LICENSE`.
- Use `WTFNMFPL` as the SPDX license identifier where applicable.
- Keep copyright information in `COPYRIGHT`.

`COPYRIGHT` should normally look like:

```text
Copyright (C) 2026 Ilya Dobryakov

Licensed under WTFPL.
```

## Third-party notices

Create `THIRD_PARTY.md` when the project contains third-party material, adapted work, copied or derived assets/code, reverse-engineered behavior, inherited project files, or anything else with licensing/provenance that deserves explicit explanation.

- Describe what was used, where it came from, how it relates to the project, and any relevant licensing or redistribution constraints.
- Clearly identify parts of the project that are not covered by the project's main license.
- Preserve required upstream notices and link to original sources/licenses when useful.
- Do not use `THIRD_PARTY.md` as a dependency/license inventory. Ordinary package dependencies do not need notices unless their license or attribution requirements make them relevant.
- When `THIRD_PARTY.md` exists, reference it from `COPYRIGHT`.

# AI usage

For AI-directed projects, create `AI_USAGE.md` describing the role of AI in development.

- State that the project contains AI-generated code developed under human direction and review.
- List the models materially used.
- Briefly describe the scope of AI involvement.
- Keep the disclosure factual and concise.
- Do not imply that AI is an author or copyright holder.

`AI_USAGE.md` should normally follow this structure:

```markdown
# AI usage

This project contains AI-generated code produced under human direction and review.

## Involvement

AI is used extensively for implementation, refactoring, research, testing, and documentation. Project direction, requirements, architecture decisions, review, and acceptance remain under human control.

## Models

Models materially used in the development of this project:

- <Model>
- <Model>

This information describes development provenance and does not imply AI authorship or copyright ownership.
```

Add a short notice near the end of `README.md` linking to `AI_USAGE.md`:

```markdown
> [!NOTE]
> This project contains AI-generated code. See [AI_USAGE.md](AI_USAGE.md) for details.
```
